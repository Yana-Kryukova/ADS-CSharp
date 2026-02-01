### **Лекция 2: C#: Значимые и ссылочные типы. Классы и методы. Массивы и строки**

**Цель:** Научить студентов понимать фундаментальные различия в хранении данных, организовывать код с помощью классов и методов, работать с коллекциями данных и текстом.

---

## **1. Значимые и ссылочные типы данных - основа понимания C#**

**Значимые типы** — это типы данных, которые хранят свои значения непосредственно в памяти стека. Переменная такого типа содержит само значение.

**Ссылочные типы** — это типы данных, которые хранят в переменной не само значение, а ссылку (адрес в памяти) на место в управляемой куче, где расположены данные.

### **1.1. Ключевые различия**
Перед изучением классов и методов важно понять, КАК данные хранятся в памяти.

| **Критерий** | **Значимые типы (Value Types)** | **Ссылочные типы (Reference Types)** |
|--------------|--------------------------|-------------------------------|
| **Где хранятся?** | В **стеке** (stack) - быстрая память, очищается автоматически | В **управляемой куче** (heap) - управляется сборщиком мусора |
| **Что хранит переменная?** | **Непосредственно значение** | **Ссылку** (адрес в памяти) на объект |
| **Присваивание** | **Копируется значение**. Создается независимая копия | **Копируется ссылка**. Обе переменные указывают на один объект |
| **Размер** | Известен на этапе компиляции | Может меняться во время выполнения |
| **Примеры в C#** | `int`, `double`, `bool`, `char`, `struct`, `enum` | `string`, `class`, `Array`, `interface`, `object` |
| **Значение по умолчанию** | `0`, `false`, `'\0'` и т.д. | `null` (отсутствие ссылки) |

### **1.2. Наглядные примеры**
```csharp
// ЗНАЧИМЫЙ тип - копируется ЗНАЧЕНИЕ
int a = 5;          // a содержит число 5
int b = a;          // В b копируется ЧИСЛО 5 (новая ячейка памяти)
b = 10;             // a = 5, b = 10 (разные ячейки памяти)

// ССЫЛОЧНЫЙ тип - копируется ССЫЛКА
int[] array1 = { 1, 2, 3 };  // array1 содержит ссылку на массив
int[] array2 = array1;       // array2 получает КОПИЮ ссылки (на тот же массив)
array2[0] = 99;              // Изменяем через array2, но array1[0] тоже становится 99!
```

**Визуализация:**
```
Значимые типы (стек):
a: | 5 |
b: | 5 | → | 10 |  (копируется значение)

Ссылочные типы:
array1: | ссылка | ---------> | 99 | 2 | 3 | (куча)
array2: | ссылка | -----------↑
```

### **1.3. Null и Nullable типы**
**`null`** — специальное значение, указывающее на отсутствие ссылки на объект.

```csharp
// Для ссылочных типов null - нормальное значение
string text = null; // text не указывает ни на какой объект
// Console.WriteLine(text.Length); // NullReferenceException!

// Для значимых типов null по умолчанию невозможен
// int age = null; // ОШИБКА!

// Nullable типы (значимые + возможность быть null)
int? nullableAge = null; // ? делает значимый тип nullable
if (nullableAge.HasValue) // Проверка наличия значения
{
    int age = nullableAge.Value; // Получение значения
}
// Или безопасное получение:
int safeAge = nullableAge ?? 0; // Если null, то 0
```

---

## **2. Классы - создаем свои типы данных**

**Класс** — это пользовательский тип данных, который описывает структуру и поведение объектов. Он определяет, какие данные (поля) и операции (методы) будут у объектов этого типа.

**Объект** — конкретный экземпляр класса, созданный в памяти во время выполнения программы. Каждый объект имеет собственное состояние (значения полей).

**Конструктор** — специальный метод класса, который вызывается при создании нового объекта и инициализирует его поля.

### **2.1. Класс как пользовательский ссылочный тип**
```csharp
// Определение класса (нового типа данных)
public class Person
{
    // ПОЛЯ - хранят состояние объекта (данные)
    public string Name;    // Поле типа string (ссылочный тип)
    public int Age;        // Поле типа int (значимый тип)
    
    // МЕТОД - определяет поведение объекта
    public void Introduce()
    {
        Console.WriteLine($"Меня зовут {Name}, мне {Age} лет.");
    }
}
```

### **2.2. Объекты - экземпляры класса**
```csharp
// Создание объектов (экземпляров класса)
Person person1 = new Person(); // new создает объект в КУЧЕ
person1.Name = "Анна";         // Обращение к полям через точку
person1.Age = 25;

Person person2 = new Person();
person2.Name = "Иван";
person2.Age = 30;

// Демонстрация ссылочной природы класса
Person person3 = person1;  // Копируется ССЫЛКА, а не объект!
person3.Age = 26;          // person1.Age тоже становится 26!
```

### **2.3. Конструкторы**
```csharp
public class Student
{
    public string Name;
    public int Course;
    
    // Конструктор - специальный метод для инициализации
    public Student(string name, int course)
    {
        Name = name;
        Course = course;
    }
    
    // Метод без параметров
    public void PrintInfo()
    {
        Console.WriteLine($"Студент {Name}, {Course} курс");
    }
}

// Использование конструктора
Student student1 = new Student("Мария", 2); // Вызов конструктора
student1.PrintInfo();
```

### **2.4. Модификаторы доступа**
**Модификаторы доступа** — ключевые слова, определяющие видимость членов класса (полей, методов) из других частей программы.

```csharp
public class BankAccount
{
    // private - доступно только внутри класса
    private decimal balance; 
    private string accountNumber;
    
    // public - доступно из любого места
    public string OwnerName;
    
    public BankAccount(string owner, string accNumber)
    {
        OwnerName = owner;
        accountNumber = accNumber;
        balance = 0;
    }
    
    // public методы для работы с private полями
    public void Deposit(decimal amount)
    {
        if (amount > 0)
            balance += amount;
    }
    
    public decimal GetBalance()
    {
        return balance;
    }
}

// Использование
BankAccount account = new BankAccount("Иван", "123456");
account.Deposit(1000);
// account.balance = 5000; // ОШИБКА: private поле!
Console.WriteLine(account.GetBalance()); // Правильный способ
```

---

## **3. Методы - функциональность классов**

**Метод** — это блок кода, который выполняет определенную задачу и может быть вызван по имени. Методы организуют код, делают его переиспользуемым и легко читаемым.

**Сигнатура метода** — уникальный идентификатор метода, включающий его имя и типы параметров (порядок важен!). Возвращаемый тип не входит в сигнатуру.

### **3.1. Структура метода**
```csharp
// Метод внутри класса
public class Calculator
{
    // Метод, возвращающий значение
    public int Add(int a, int b)
    {
        int result = a + b;
        return result; // Обязательно для методов с возвращаемым типом
    }
    
    // Void-метод (не возвращает значение)
    public void PrintResult(int result)
    {
        Console.WriteLine($"Результат: {result}");
        // return; // Необязателен, используется для досрочного выхода
    }
    
    // Метод без параметров
    public void Beep()
    {
        Console.Beep();
    }
}
```

### **3.2. Статические vs нестатические методы**
**Статический метод** — метод, принадлежащий классу, а не конкретному объекту. Вызывается через имя класса и не может работать с нестатическими полями.

**Нестатический метод** — метод, принадлежащий конкретному объекту класса. Вызывается через экземпляр класса и работает с данными этого объекта.

```csharp
public class MathHelper
{
    // НЕстатический метод - работает с данными КОНКРЕТНОГО объекта
    private int baseValue;
    
    public int MultiplyByBase(int x)
    {
        return baseValue * x; // Использует поле объекта
    }
    
    // Статический метод - принадлежит КЛАССУ, а не объекту
    public static double CalculateCircleArea(double radius)
    {
        return Math.PI * radius * radius; // Не использует поля объекта
    }
}

// Использование
MathHelper helper = new MathHelper();
// helper.baseValue = 10; // Если бы поле было public
// int result = helper.MultiplyByBase(5); // Вызов нестатического метода

// Вызов статического метода - через имя класса
double area = MathHelper.CalculateCircleArea(5);
// Стандартные примеры:
int max = Math.Max(10, 20);          // Статический метод
string text = Console.ReadLine();    // Статический метод
Random rnd = new Random();
int num = rnd.Next(1, 100);          // Нестатический метод
```

### **3.3. Передача параметров по ссылке**
**Параметры по значению** — способ передачи параметров, при котором в метод передается копия значения (по умолчанию для значимых типов).

**Параметры по ссылке** (`ref`/`out`) — способ передачи параметров, при котором метод работает непосредственно с исходной переменной, а не с ее копией.

```csharp
public class NumberSwapper
{
    // Обычная передача (по значению) - для значимых типов
    public void TrySwap(int a, int b)
    {
        int temp = a;
        a = b;
        b = temp;
        // Изменения НЕ видны снаружи!
    }
    
    // Передача по ссылке (ref) - изменения сохраняются
    public void RealSwap(ref int a, ref int b)
    {
        int temp = a;
        a = b;
        b = temp;
    }
    
    // out - параметр только для возврата значения
    public bool TryParsePositive(string input, out int result)
    {
        if (int.TryParse(input, out int num) && num > 0)
        {
            result = num; // ОБЯЗАНЫ присвоить значение
            return true;
        }
        result = 0; // ОБЯЗАНЫ присвоить даже в случае ошибки
        return false;
    }
}

// Использование
NumberSwapper swapper = new NumberSwapper();
int x = 5, y = 10;

swapper.TrySwap(x, y); // x=5, y=10 (не изменилось)
swapper.RealSwap(ref x, ref y); // x=10, y=5 (изменилось)

if (swapper.TryParsePositive("42", out int number))
{
    Console.WriteLine($"Положительное: {number}");
}
```

---

## **4. Массивы - коллекции элементов**

**Массив** — структура данных для хранения фиксированного количества элементов одного типа. В C# массивы являются ссылочными типами.

**Индекс** — числовое значение, указывающее на позицию элемента в массиве. Индексация начинается с 0.

### **4.1. Массивы как ссылочный тип**
```csharp
// Создание массива (ссылочный тип!)
int[] numbers = new int[5]; // Создается в КУЧЕ, numbers - ссылка
numbers[0] = 10;            // Обращение по индексу (с 0!)
numbers[1] = 20;
// numbers[5] = 30; // ОШИБКА: IndexOutOfRangeException

// Массив передается по ссылке
void ModifyArray(int[] arr)
{
    arr[0] = 100; // Изменяется исходный массив!
}

ModifyArray(numbers); // numbers[0] теперь = 100
```

### **4.2. Работа с массивами на практике**
```csharp
public class ArrayProcessor
{
    // Метод заполнения массива случайными числами
    public void FillRandom(int[] array, int min, int max)
    {
        Random rnd = new Random();
        for (int i = 0; i < array.Length; i++)
        {
            array[i] = rnd.Next(min, max);
        }
    }
    
    // Метод поиска максимального элемента
    public int FindMax(int[] array)
    {
        if (array.Length == 0) return 0;
        
        int max = array[0];
        for (int i = 1; i < array.Length; i++)
        {
            if (array[i] > max)
                max = array[i];
        }
        return max;
    }
    
    // Метод подсчета элементов по условию
    public int CountEvenNumbers(int[] numbers)
    {
        int count = 0;
        foreach (int num in numbers) // Цикл foreach для массивов
        {
            if (num % 2 == 0)
                count++;
        }
        return count;
    }
}

// Использование
ArrayProcessor processor = new ArrayProcessor();
int[] data = new int[10];
processor.FillRandom(data, 1, 100);
int maxValue = processor.FindMax(data);
int evenCount = processor.CountEvenNumbers(data);
```

### **4.3. Встроенные методы Array**
```csharp
int[] numbers = { 5, 2, 8, 1, 9 };

// Сортировка (изменяет исходный массив)
Array.Sort(numbers);       // {1, 2, 5, 8, 9}
Array.Reverse(numbers);    // {9, 8, 5, 2, 1}

// Поиск
int index = Array.IndexOf(numbers, 5); // 2
bool contains = Array.Exists(numbers, x => x > 10); // false

// Изменение размера (редко используется)
Array.Resize(ref numbers, 15); // Теперь длина 15
```

---

## **5. Строки и StringBuilder**

**Строка (`string`)** — неизменяемый (immutable) ссылочный тип, представляющий последовательность символов Unicode. Любая операция над строкой создает новый объект.

**StringBuilder** — класс из пространства имен `System.Text`, предназначенный для эффективной работы с изменяемыми строками, особенно при множественных операциях конкатенации.

### **5.1. String как неизменяемый ссылочный тип**
```csharp
string text = "Hello";
text = text + " World"; // Создается НОВАЯ строка в куче
// "Hello" остается в памяти (сборщик мусора ее удалит позже)

// Проблема производительности
string result = "";
for (int i = 0; i < 10000; i++)
{
    result += i.ToString(); // Каждая итерация создает новый объект!
    // Создано ~10000 объектов, 9999 из них - мусор
}
```

### **5.2. Основные операции со строками**
```csharp
public class StringHelper
{
    // Проверка на палиндром
    public bool IsPalindrome(string text)
    {
        string cleaned = text.ToLower().Replace(" ", "");
        for (int i = 0; i < cleaned.Length / 2; i++)
        {
            if (cleaned[i] != cleaned[cleaned.Length - 1 - i])
                return false;
        }
        return true;
    }
    
    // Подсчет слов
    public int CountWords(string text)
    {
        if (string.IsNullOrWhiteSpace(text))
            return 0;
            
        string[] words = text.Split(new char[] { ' ', ',', '.', '!' }, 
                                  StringSplitOptions.RemoveEmptyEntries);
        return words.Length;
    }
}

// Использование
StringHelper helper = new StringHelper();
bool isPal = helper.IsPalindrome("А роза упала на лапу Азора");
int wordCount = helper.CountWords("Hello, world! How are you?");
```

### **5.3. StringBuilder для эффективной работы**
```csharp
using System.Text;

public class ReportGenerator
{
    public string GenerateReport(List<Student> students)
    {
        StringBuilder report = new StringBuilder();
        
        report.AppendLine("=== ОТЧЕТ О СТУДЕНТАХ ===");
        report.AppendLine($"Дата: {DateTime.Now}");
        report.AppendLine();
        
        foreach (var student in students)
        {
            report.AppendLine($"- {student.Name}, {student.Course} курс");
            // Нет создания промежуточных строк!
        }
        
        report.AppendLine();
        report.AppendLine($"Всего студентов: {students.Count}");
        
        return report.ToString(); // Однократное создание строки
    }
}
```

---

### **Итоговый пример: объединяем все знания**
```csharp
public class University
{
    private Student[] students; // Массив объектов
    
    public University(int capacity)
    {
        students = new Student[capacity];
    }
    
    public void AddStudent(string name, int course, int index)
    {
        if (index >= 0 && index < students.Length)
        {
            students[index] = new Student(name, course);
        }
    }
    
    public string GetReport()
    {
        StringBuilder report = new StringBuilder();
        report.AppendLine("Список студентов:");
        
        foreach (var student in students)
        {
            if (student != null) // Проверка на null
                report.AppendLine(student.GetInfo());
        }
        
        return report.ToString();
    }
}

// Использование
University uni = new University(10);
uni.AddStudent("Анна", 2, 0);
uni.AddStudent("Иван", 3, 1);
Console.WriteLine(uni.GetReport());
```

---

### **Задачи для практики**

#### **Блок 1: Передача параметров (ref, in, out)**
1. **Swap с использованием ref:** Напишите метод `SwapNumbers`, который принимает два целых числа по ссылке (`ref`) и меняет их значения местами. Протестируйте работу метода в `Main`.

2. **Безопасное вычисление с out:** Создайте метод `TryCalculateAverage`, который принимает массив чисел и возвращает через `out` параметр их среднее значение. Метод должен возвращать `bool` (успех/неудача). Неудача, если массив пустой или null.

3. **Оптимизация с in:** Напишите метод `CalculateDistance`, который принимает две точки (структура `Point` с полями X, Y) с модификатором `in` и возвращает расстояние между ними. Создайте структуру `Point` и продемонстрируйте, что попытка изменить точки внутри метода приводит к ошибке компиляции.

#### **Блок 2: Методы и массивы (без классов)**
1. **Фильтрация массива:** Напишите метод `FilterPositiveNumbers`, который принимает массив целых чисел и возвращает новый массив, содержащий только положительные элементы из исходного.

2. **Поиск индексов:** Создайте метод `FindAllIndices`, который принимает массив строк и искомую строку, возвращает массив индексов, где эта строка встречается. Если строка не найдена, возвращайте пустой массив.

3. **Ротация массива:** Напишите метод `RotateArray`, который принимает массив чисел и число k, циклически сдвигает элементы массива вправо на k позиций (например, [1,2,3,4] при k=1 → [4,1,2,3]).

#### **Блок 3: Строки и методы (без классов)**
1. **Анализ строки:** Создайте метод `AnalyzeString`, который принимает строку и возвращает кортеж `(letterCount, digitCount, spaceCount)` - количество букв, цифр и пробелов в строке.

2. **Форматирование строки:** Напишите метод `FormatPhoneNumber`, который принимает строку из 11 цифр (например "79991234567") и возвращает отформатированную строку в виде "+7 (999) 123-45-67". Используйте `StringBuilder`.

3. **Разделение и обработка:** Создайте метод `ProcessCSVLine`, который принимает строку в формате CSV (значения разделены запятыми) и возвращает массив строк, где каждая строка очищена от лишних пробелов и приведена к нижнему регистру.

#### **Блок 4: Классы и методы**
1. **Класс "Прямоугольник":** Создайте класс `Rectangle` с приватными полями `Width` и `Height`. Добавьте:
   - Конструктор, принимающий ширину и высоту
   - Метод `CalculateArea()` для вычисления площади
   - Метод `CalculatePerimeter()` для вычисления периметра
   - Метод `GetInfo()`, возвращающий строку с информацией о прямоугольнике

2. **Класс "Счетчик":** Создайте класс `Counter` с:
   - Приватным полем `count` (начальное значение 0)
   - Метод `Increment()` - увеличивает счетчик на 1
   - Метод `Decrement()` - уменьшает счетчик на 1 (но не ниже 0)
   - Метод `GetValue()` - возвращает текущее значение
   - Метод `Reset()` - сбрасывает счетчик в 0

3. **Класс "Учет студентов":** Создайте класс `StudentManager` с:
   - Приватным массивом `Student[]` (максимум 100 студентов)
   - Метод `AddStudent(name, age)` - добавляет нового студента
   - Метод `FindStudent(name)` - ищет студента по имени
   - Метод `GetAverageAge()` - вычисляет средний возраст студентов
   - Метод `GenerateReport()` с использованием `StringBuilder`

---

### **Что будет на следующей лекции:**
**Лекция 3: Анализ сложности алгоритмов. Простые структуры данных: списки**

**Цель:** Заложить фундаментальную основу для оценки алгоритмов и ввести первую динамическую структуру данных.

**Содержание:**
1.  **Анализ алгоритмов:** время работы и потребление памяти. Практическая значимость.
2.  **Асимптотическая сложность (нотация О-большое):** O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ). Примеры для каждого случая.
3.  **Списки (`List<T>`):**
    *   Понятие динамического массива. Внутреннее устройство `List<T>` (массив под капотом, увеличение `Capacity`).
    *   Основные операции: `Add`, `Insert`, `Remove`, `RemoveAt`, доступ по индексу, `Contains`. Анализ их амортизированной сложности.
    *   Отличие от классического массива (`T[]`). Когда что использовать.
    *   Сравнение структур на практике: Массив vs `List<T>` vs `StringBuilder` (как специализированная коллекция для символов).
4.  **Практический пример:** Реализация алгоритма, использующего `List<T>` (например, фильтрация коллекции), и анализ его сложности.