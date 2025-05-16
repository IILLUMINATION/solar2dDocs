# **Руководство по созданию Java-плагинов для Solar2D**

## **1. Введение**
Это руководство описывает процесс создания нативных Java-плагинов для Solar2D, которые позволяют расширять функциональность движка с помощью собственного кода на Java.

## **2. Структура плагина**

### **2.1 Организация пакетов**
Каждый плагин должен использовать уникальное имя пакета в формате:
```java
plugin.<название_плагина>
```
Например:
```java
plugin.mycustomplugin
```

### **2.2 Основные компоненты**
1. **Главный загрузчик (LuaLoader)** - регистрирует функции в Lua
2. **Классы функций** - реализуют конкретную логику

## **3. Реализация загрузчика**

```java
package plugin.mycustomplugin;

import com.naef.jnlua.LuaState;
import com.naef.jnlua.JavaFunction;
import com.naef.jnlua.NamedJavaFunction;

public class LuaLoader implements JavaFunction {
    @Override
    public int invoke(LuaState luaState) {
        // Регистрация функций плагина
        luaState.register(
            luaState.toString(1),  // Получаем имя модуля из require()
            new NamedJavaFunction[] {
                new CustomFunction() // Добавляем реализованные функции
            }
        );
        return 1; // Стандартное возвращаемое значение
    }
}
```

## **4. Создание пользовательских функций**

### **4.1 Базовый шаблон**

```java
package plugin.mycustomplugin;

import com.naef.jnlua.LuaState;
import com.naef.jnlua.NamedJavaFunction;

public class CustomFunction implements NamedJavaFunction {
    @Override
    public String getName() {
        return "customFunction"; // Имя функции в Lua
    }

    @Override
    public int invoke(LuaState luaState) {
        // Реализация функции
        return 1; // Количество возвращаемых значений
    }
}
```

### **4.2 Работа с параметрами**

Параметры доступны через методы объекта LuaState:

```java
// Получение параметров
String textParam = luaState.toString(1);    // Строковый параметр
double numParam = luaState.toNumber(2);     // Числовой параметр
boolean boolParam = luaState.toBoolean(3);  // Логический параметр
```

### **4.3 Возврат значений**

```java
// Возврат значений в Lua
luaState.pushString("Результат");  // Строка
luaState.pushNumber(42.0);         // Число
luaState.pushBoolean(true);        // Логическое значение
return 1; // Указываем количество возвращаемых значений
```

## **5. Практический пример**

Реализация функции сложения двух чисел:

```java
public class AddNumbers implements NamedJavaFunction {
    @Override
    public String getName() {
        return "addNumbers";
    }

    @Override
    public int invoke(LuaState luaState) {
        double a = luaState.toNumber(1);
        double b = luaState.toNumber(2);
        double result = a + b;
        luaState.pushNumber(result);
        return 1;
    }
}
```

Использование в Lua:
```lua
local sum = plugin.mycustomplugin.addNumbers(10, 20)  -- Вернет 30
```

## **6. Сборка и подключение**

1. Скомпилируйте код в JAR-файл
2. Поместите файл в директорию проекта:
   ```
   /project/androidResources/plugin.mycustomplugin.jar
   ```
3. Пересоберите проект Solar2D

## **7. Дополнительные сведения**

В следующих версиях руководства будут рассмотрены:
- Работа с Lua-таблицами
- Реализация обработчиков событий
- Особенности отладки плагинов

## **8. Заключение**

Это руководство покрывает базовые аспекты создания плагинов для Solar2D. Для более сложных сценариев рекомендуется изучить официальную документацию по JNLua.
