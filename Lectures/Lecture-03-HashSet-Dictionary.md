# Лекция: Множества и хеш-таблицы в алгоритмах на C#

---

## Часть 1. Введение в хеш-таблицы

### Проблема линейного поиска

```csharp
// Пример: поиск элемента в списке
var numbers = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
int target = 7;

// Поиск в массиве - O(n)
bool found = numbers.Contains(target); // В худшем случае 10 сравнений
```

Что если у нас миллион элементов? Линейный поиск становится непозволительной роскошью.

### Основная идея хеш-таблиц

**Определение:** Хеш-таблица — это структура данных, которая использует хеш-функцию для преобразования ключа в индекс массива, что позволяет выполнять операции поиска, вставки и удаления в среднем за O(1).

Хеш-таблица использует функцию хеширования для преобразования ключа в индекс массива:

```csharp
// Концептуальная иллюстрация
int HashFunction(string key) 
{
    int hash = 0;
    foreach (char c in key)
        hash += c; // Упрощенная хеш-функция
    return hash % 1000; // Индекс в диапазоне 0-999
}

// Теперь поиск:
int index = HashFunction("apple"); // O(1)
// Значение хранится по этому индексу
```

### Коллизии и их разрешение

**Определение:** Коллизия — это ситуация, когда два различных ключа после применения хеш-функции дают одинаковый индекс в массиве.

```csharp
// Коллизия: два ключа дают одинаковый индекс
// "apple" -> 42
// "elppa" -> 42 (коллизия!)

// В C# используется метод цепочек:
// Каждая "корзина" (bucket) содержит связный список элементов
```

---

## Часть 2. HashSet<T> - множество уникальных элементов

**Определение:** HashSet<T> — это коллекция, которая хранит только уникальные элементы. Она реализована на основе хеш-таблицы и обеспечивает среднее время выполнения операций добавления, удаления и поиска за O(1).

### Создание и базовые операции

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

class HashSetBasics
{
    static void Main()
    {
        // Создание HashSet
        var fruits = new HashSet<string>();
        
        // Добавление элементов
        fruits.Add("apple");
        fruits.Add("banana");
        fruits.Add("orange");
        
        // Add возвращает bool - был ли добавлен элемент
        bool added = fruits.Add("apple"); // false - уже существует
        Console.WriteLine($"Apple added again: {added}"); // False
        
        // Проверка наличия - O(1)
        bool hasBanana = fruits.Contains("banana");
        Console.WriteLine($"Has banana: {hasBanana}"); // True
        
        // Удаление
        fruits.Remove("orange");
        
        // Количество элементов
        Console.WriteLine($"Count: {fruits.Count}"); // 2
        
        // Итерация
        foreach (var fruit in fruits)
        {
            Console.WriteLine(fruit);
        }
    }
}
```

### Инициализация разными способами

```csharp
class HashSetInitialization
{
    static void Examples()
    {
        // 1. С коллекцией инициализаторов
        var set1 = new HashSet<int> { 1, 2, 3, 4, 5 };
        
        // 2. Из существующей коллекции
        var list = new List<int> { 1, 2, 2, 3, 3, 3 };
        var set2 = new HashSet<int>(list);
        Console.WriteLine($"Set2 count: {set2.Count}"); // 3 (1,2,3)
        
        // 3. С кастомным компаратором
        var set3 = new HashSet<string>(StringComparer.OrdinalIgnoreCase);
        set3.Add("hello");
        set3.Add("HELLO"); // Не добавится - считается дубликатом
        Console.WriteLine($"Set3 count: {set3.Count}"); // 1
    }
}
```

### Теоретико-множественные операции

**Определение:** Теоретико-множественные операции — это операции над множествами, такие как объединение, пересечение и разность, которые в HashSet<T> реализованы как методы, изменяющие текущее множество.

```csharp
class SetOperations
{
    static void Main()
    {
        var setA = new HashSet<int> { 1, 2, 3, 4, 5 };
        var setB = new HashSet<int> { 4, 5, 6, 7, 8 };
        
        // Объединение (Union) - A ∪ B
        var union = new HashSet<int>(setA);
        union.UnionWith(setB);
        Console.WriteLine($"Union: {string.Join(", ", union)}");
        // Результат: 1, 2, 3, 4, 5, 6, 7, 8
        
        // Пересечение (Intersection) - A ∩ B
        var intersection = new HashSet<int>(setA);
        intersection.IntersectWith(setB);
        Console.WriteLine($"Intersection: {string.Join(", ", intersection)}");
        // Результат: 4, 5
        
        // Разность (Difference) - A \ B
        var difference = new HashSet<int>(setA);
        difference.ExceptWith(setB);
        Console.WriteLine($"Difference: {string.Join(", ", difference)}");
        // Результат: 1, 2, 3
        
        // Симметрическая разность - (A ∪ B) \ (A ∩ B)
        var symmetricDiff = new HashSet<int>(setA);
        symmetricDiff.SymmetricExceptWith(setB);
        Console.WriteLine($"Symmetric difference: {string.Join(", ", symmetricDiff)}");
        // Результат: 1, 2, 3, 6, 7, 8
        
        // Проверка подмножества
        var subset = new HashSet<int> { 1, 2 };
        Console.WriteLine($"Is subset: {subset.IsSubsetOf(setA)}"); // True
        
        // Проверка надмножества
        Console.WriteLine($"Is superset: {setA.IsSupersetOf(subset)}"); // True
        
        // Пересекаются ли множества
        Console.WriteLine($"Overlaps: {setA.Overlaps(setB)}"); // True
    }
}
```

### Алгоритмические задачи с HashSet

#### Задача 1: Поиск дубликатов

```csharp
class FindDuplicates
{
    // Найти все дубликаты в массиве
    public static IEnumerable<int> FindDuplicateElements(int[] nums)
    {
        var seen = new HashSet<int>();
        var duplicates = new HashSet<int>();
        
        foreach (var num in nums)
        {
            if (!seen.Add(num)) // Если не удалось добавить - элемент уже был
            {
                duplicates.Add(num);
            }
        }
        
        return duplicates;
    }
    
    // Проверить, есть ли дубликаты (простой вариант)
    public static bool ContainsDuplicate(int[] nums)
    {
        var set = new HashSet<int>(nums);
        return set.Count != nums.Length;
    }
    
    static void Main()
    {
        int[] arr = { 1, 2, 3, 2, 4, 5, 3, 6 };
        
        var duplicates = FindDuplicateElements(arr);
        Console.WriteLine($"Duplicates: {string.Join(", ", duplicates)}");
        // Результат: 2, 3
        
        bool hasDup = ContainsDuplicate(arr);
        Console.WriteLine($"Has duplicates: {hasDup}"); // True
    }
}
```

#### Задача 2: Пересечение двух массивов

```csharp
class ArrayIntersection
{
    // Найти уникальные общие элементы
    public static int[] Intersection(int[] nums1, int[] nums2)
    {
        var set1 = new HashSet<int>(nums1);
        var set2 = new HashSet<int>(nums2);
        
        // Оставляем только те, что есть в обоих множествах
        set1.IntersectWith(set2);
        
        return set1.ToArray();
    }
    
    // Найти все общие элементы (включая повторения)
    public static int[] IntersectionWithDuplicates(int[] nums1, int[] nums2)
    {
        var dict = new Dictionary<int, int>();
        var result = new List<int>();
        
        // Подсчитываем частоту в первом массиве
        foreach (var num in nums1)
        {
            dict[num] = dict.GetValueOrDefault(num) + 1;
        }
        
        // Добавляем элементы из второго массива, если они есть в словаре
        foreach (var num in nums2)
        {
            if (dict.ContainsKey(num) && dict[num] > 0)
            {
                result.Add(num);
                dict[num]--;
            }
        }
        
        return result.ToArray();
    }
    
    static void Main()
    {
        int[] arr1 = { 1, 2, 2, 3, 4 };
        int[] arr2 = { 2, 2, 3, 5 };
        
        var intersection = Intersection(arr1, arr2);
        Console.WriteLine($"Intersection: {string.Join(", ", intersection)}");
        // Результат: 2, 3
        
        var intersectionWithDup = IntersectionWithDuplicates(arr1, arr2);
        Console.WriteLine($"Intersection with duplicates: {string.Join(", ", intersectionWithDup)}");
        // Результат: 2, 2, 3
    }
}
```

#### Задача 3: Longest Consecutive Sequence (классическая задача)

Дана неотсортированная последовательность целых чисел nums. Верните длину последовательности самых длинных последовательных элементов.
Вам необходимо написать алгоритм, который работает за  O(n) время .
```csharp
class LongestConsecutiveSequence
{
    public static int FindLongestConsecutive(int[] nums)
    {
        if (nums.Length == 0) return 0;
        
        var set = new HashSet<int>(nums);
        int longestStreak = 0;
        
        foreach (var num in set)
        {
            // Начинаем последовательность только если это первый элемент
            if (!set.Contains(num - 1))
            {
                int currentNum = num;
                int currentStreak = 1;
                
                // Продолжаем пока есть следующий элемент
                while (set.Contains(currentNum + 1))
                {
                    currentNum++;
                    currentStreak++;
                }
                
                longestStreak = Math.Max(longestStreak, currentStreak);
            }
        }
        
        return longestStreak;
    }
    
    static void Main()
    {
        int[] nums = { 100, 4, 200, 1, 3, 2 };
        int result = FindLongestConsecutive(nums);
        Console.WriteLine($"Longest consecutive sequence: {result}"); // 4 (1,2,3,4)
    }
}
```

---

## Часть 3. Dictionary<TKey, TValue> - словарь

**Определение:** Dictionary<TKey, TValue> — это коллекция пар "ключ-значение", где каждый ключ уникален. Как и HashSet<T>, словарь реализован на основе хеш-таблицы и обеспечивает доступ к значению по ключу в среднем за O(1).

### Создание и базовые операции

```csharp
class DictionaryBasics
{
    static void Main()
    {
        // Создание словаря
        var students = new Dictionary<int, string>();
        
        // Добавление элементов
        students.Add(1, "Alice");
        students.Add(2, "Bob");
        students.Add(3, "Charlie");
        
        // Альтернативный синтаксис
        students[4] = "David";
        
        // Доступ по ключу
        string student2 = students[2];
        Console.WriteLine($"Student 2: {student2}"); // Bob
        
        // Проверка существования ключа
        if (students.ContainsKey(5))
        {
            Console.WriteLine(students[5]);
        }
        else
        {
            Console.WriteLine("Student 5 not found");
        }
        
        // Безопасное получение значения
        if (students.TryGetValue(3, out string student3))
        {
            Console.WriteLine($"Student 3: {student3}"); // Charlie
        }
        
        // Итерация по ключам
        foreach (var key in students.Keys)
        {
            Console.WriteLine($"Key: {key}");
        }
        
        // Итерация по значениям
        foreach (var value in students.Values)
        {
            Console.WriteLine($"Value: {value}");
        }
        
        // Итерация по парам
        foreach (var pair in students)
        {
            Console.WriteLine($"{pair.Key}: {pair.Value}");
        }
        
        // Удаление
        students.Remove(2);
        
        // Очистка
        students.Clear();
    }
}
```

### Инициализация словаря

```csharp
class DictionaryInitialization
{
    static void Examples()
    {
        // 1. С коллекцией инициализаторов
        var dict1 = new Dictionary<string, int>
        {
            ["apple"] = 5,
            ["banana"] = 3,
            ["orange"] = 7
        };
        
        // 2. Альтернативный синтаксис
        var dict2 = new Dictionary<string, int>
        {
            { "apple", 5 },
            { "banana", 3 },
            { "orange", 7 }
        };
        
        // 3. С кастомным компаратором ключей
        var dict3 = new Dictionary<string, int>(StringComparer.OrdinalIgnoreCase);
        dict3["hello"] = 1;
        dict3["HELLO"] = 2; // Перезапишет "hello"
        Console.WriteLine($"Count: {dict3.Count}"); // 1
        Console.WriteLine($"Value: {dict3["hello"]}"); // 2
        
        // 4. Из другой коллекции
        var source = new List<KeyValuePair<string, int>>
        {
            new KeyValuePair<string, int>("x", 1),
            new KeyValuePair<string, int>("y", 2)
        };
        var dict4 = new Dictionary<string, int>(source);
    }
}
```

### Продвинутые методы работы со словарем

```csharp
class DictionaryAdvanced
{
    static void Main()
    {
        var dict = new Dictionary<string, int>();
        
        // GetValueOrDefault (C# 7.1+)
        int value = dict.GetValueOrDefault("missing", -1);
        Console.WriteLine($"Value: {value}"); // -1
        
        // TryAdd (C# 8.0+)
        bool added = dict.TryAdd("key1", 100);
        Console.WriteLine($"Added: {added}"); // True
        
        added = dict.TryAdd("key1", 200);
        Console.WriteLine($"Added again: {added}"); // False
        
        // EnsureCapacity (оптимизация памяти)
        dict.EnsureCapacity(1000); // Резервируем место под 1000 элементов
        
        // TrimExcess (освобождение памяти)
        dict.TrimExcess();
        
        // Обновление значения
        dict["key1"] = 150; // Обновляем существующий ключ
        
        // Условное обновление
        if (dict.TryGetValue("key1", out int currentValue))
        {
            dict["key1"] = currentValue + 50;
        }
    }
}
```

### Алгоритмические задачи с Dictionary

#### Задача 1: Подсчет частоты элементов (Frequency Counter)

```csharp
class FrequencyCounter
{
    public static Dictionary<T, int> CountFrequencies<T>(IEnumerable<T> items)
    {
        var frequencies = new Dictionary<T, int>();
        
        foreach (var item in items)
        {
            // Способ 1: с проверкой
            if (frequencies.ContainsKey(item))
                frequencies[item]++;
            else
                frequencies[item] = 1;
            
            // Способ 2: с TryGetValue (более эффективно)
            // frequencies.TryGetValue(item, out int count);
            // frequencies[item] = count + 1;
        }
        
        return frequencies;
    }
    
    // Найти наиболее частый элемент
    public static T FindMostFrequent<T>(IEnumerable<T> items)
    {
        var freq = CountFrequencies(items);
        
        T mostFrequent = default;
        int maxCount = 0;
        
        foreach (var pair in freq)
        {
            if (pair.Value > maxCount)
            {
                maxCount = pair.Value;
                mostFrequent = pair.Key;
            }
        }
        
        return mostFrequent;
    }
    
    static void Main()
    {
        string text = "hello world hello";
        var words = text.Split(' ');
        
        var freq = CountFrequencies(words);
        foreach (var pair in freq)
        {
            Console.WriteLine($"{pair.Key}: {pair.Value}");
        }
        // Результат:
        // hello: 2
        // world: 1
        
        var mostFrequent = FindMostFrequent(words);
        Console.WriteLine($"Most frequent: {mostFrequent}"); // hello
    }
}
```

#### Задача 2: Two Sum (классика собеседований)

```csharp
class TwoSum
{
    // Найти индексы двух чисел, сумма которых равна target
    public static int[] FindTwoSum(int[] nums, int target)
    {
        // Ключ: число, Значение: его индекс
        var dict = new Dictionary<int, int>();
        
        for (int i = 0; i < nums.Length; i++)
        {
            int complement = target - nums[i];
            
            // Проверяем, есть ли уже нужное число
            if (dict.TryGetValue(complement, out int index))
            {
                return new int[] { index, i };
            }
            
            // Запоминаем текущее число и его индекс
            // Важно: добавляем после проверки, чтобы не использовать один элемент дважды
            if (!dict.ContainsKey(nums[i]))
            {
                dict[nums[i]] = i;
            }
        }
        
        return Array.Empty<int>();
    }
    
    static void Main()
    {
        int[] nums = { 2, 7, 11, 15 };
        int target = 9;
        
        var result = FindTwoSum(nums, target);
        Console.WriteLine($"Indices: [{result[0]}, {result[1]}]"); // [0, 1]
        Console.WriteLine($"Values: {nums[result[0]]} + {nums[result[1]]} = {target}");
    }
}
```

## Часть 4. Best practices

```csharp
class BestPractices
{
    // ✅ ХОРОШО: Используем TryGetValue вместо ContainsKey + индексатор
    public static void GoodTryGetValue(Dictionary<string, int> dict, string key)
    {
        // Плохо
        if (dict.ContainsKey(key))
        {
            int value = dict[key]; // Два поиска!
        }
        
        // Хорошо
        if (dict.TryGetValue(key, out int value))
        {
            // Один поиск
        }
    }
    
    // ✅ ХОРОШО: Используем Add для проверки уникальности
    public static void GoodAddCheck(HashSet<int> set, int item)
    {
        // Плохо
        if (!set.Contains(item))
        {
            set.Add(item);
        }
        
        // Хорошо
        if (set.Add(item))
        {
            // Элемент был добавлен
        }
    }
    
    // ✅ ХОРОШО: Предварительное выделение памяти
    public static void GoodCapacityPlanning()
    {
        // Плохо - много перехеширований
        var dict = new Dictionary<int, string>();
        for (int i = 0; i < 100000; i++)
        {
            dict[i] = i.ToString();
        }
        
        // Хорошо - сразу выделяем достаточно места
        var dictGood = new Dictionary<int, string>(100000);
        for (int i = 0; i < 100000; i++)
        {
            dictGood[i] = i.ToString();
        }
    }
    
    // ❌ ПЛОХО: Изменяемый объект как ключ
    public static void BadMutableKey()
    {
        var point = new PointFixed(1, 2);
        var dict = new Dictionary<PointFixed, string>();
        dict[point] = "Value";
        
        point.X = 10; // Мутируем ключ!
        
        // Теперь словарь сломан - этот ключ не будет найден
        if (dict.ContainsKey(point))
        {
            Console.WriteLine("Not found!"); // Не выполнится
        }
        
        // Но объект все еще в словаре по старому хешу
    }
    
    // ✅ ХОРОШО: Неизменяемые типы для ключей
    public static void GoodImmutableKey()
    {
        var dict = new Dictionary<string, string>();
        string key = "immutable";
        dict[key] = "Value";
        
        // key = "changed"; - если изменить, это будет новый объект
        // Поэтому старый ключ все еще работает
        bool found = dict.ContainsKey(key);
        Console.WriteLine($"Found: {found}"); // True
    }
}
```

### Выбор правильной структуры данных

```csharp
class StructureSelection
{
    static void ChooseCorrectStructure()
    {
        // Когда использовать HashSet<T>:
        // ✅ Нужна уникальность элементов
        // ✅ Частые операции Contains
        // ✅ Не нужен порядок
        // ✅ Нет пар "ключ-значение"
        
        var uniqueItems = new HashSet<int>();
        
        // Когда использовать Dictionary<TKey, TValue>:
        // ✅ Нужны пары ключ-значение
        // ✅ Быстрый доступ по ключу
        // ✅ Ключи уникальны
        
        var keyValueMap = new Dictionary<string, int>();
        
        // Когда использовать SortedSet<T>:
        // ✅ Нужен отсортированный порядок
        // ✅ Уникальность элементов
        // ✅ Допустимо O(log n) вместо O(1)
        
        var sortedUnique = new SortedSet<int>();
        
        // Когда использовать SortedDictionary<TKey, TValue>:
        // ✅ Нужны пары ключ-значение в отсортированном порядке
        // ✅ Допустимо O(log n)
        
        var sortedMap = new SortedDictionary<string, int>();
        
        // Когда использовать List<T>:
        // ✅ Нужен порядок
        // ✅ Допустимо O(n) для поиска
        // ✅ Экономия памяти
        // ✅ Частая итерация по всем элементам
        
        var orderedList = new List<int>();
    }
}
```

---

## Часть 5. Заключение и практические задания

### Резюме

1. **HashSet<T>** и **Dictionary<TKey, TValue>** - основа для O(1) операций
2. Всегда помните о **GetHashCode** и **Equals** для кастомных типов
3. Выбирайте структуру данных исходя из требований:
   - Нужна уникальность → HashSet
   - Нужны пары ключ-значение → Dictionary
   - Нужен порядок → SortedSet/SortedDictionary или List
4. Оптимизируйте с помощью `TryGetValue`, предварительного выделения памяти и неизменяемых ключей

### Практические задания для самостоятельной работы

1. **Задача:** Реализуйте метод `FindUniqueNumbers`, который принимает массив чисел и возвращает только уникальные значения с сохранением порядка первого вхождения.

2. **Задача:** Реализуйте простой кэш с временем жизни (TTL) используя `Dictionary` и храня время создания каждого объекта.

3. **Задача:** Напишите метод `AreAnagrams`, который проверяет, являются ли две строки анаграммами, используя `Dictionary<char, int>`.

4. **Задача:** Реализуйте структуру данных "Мультимножество" (Multiset), которая позволяет хранить элементы с повторениями, используя `Dictionary<T, int>`.

### Рекомендуемые ресурсы

- Исходный код .NET: [HashSet.cs](https://referencesource.microsoft.com/#System.Core/System/Collections/Generic/HashSet.cs) и [Dictionary.cs](https://referencesource.microsoft.com/#mscorlib/system/collections/generic/dictionary.cs)
- LeetCode задачи: Two Sum, Contains Duplicate, Group Anagrams, Longest Consecutive Sequence
- Документация Microsoft: [HashSet](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1), [Dictionary](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2)
