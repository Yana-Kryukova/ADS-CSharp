# Пособие по теме: Синтаксис и возможности Dictionary и HashSet


## Часть 1. Проблема, которую решают Dictionary и HashSet

До сих пор вы работали с **массивами** — простыми списками элементов, где каждый элемент имеет **индекс** (0, 1, 2...).

```csharp
string[] students = new string[100];
students[0] = "Анна";  // обратились по индексу
```

**Но что, если нужен быстрый поиск не по индексу, а по значению?**

### Пример: телефонная книга

Представьте, что вы храните телефонную книгу в массиве:

```csharp
string[] names = new string[1000];
string[] phones = new string[1000];

// Чтобы найти телефон Анны, нужно перебрать весь массив!
for (int i = 0; i < names.Length; i++)
{
    if (names[i] == "Анна")
    {
        Console.WriteLine(phones[i]);
        break;
    }
}
```

**Проблема:** если в книге 1 000 000 записей, в худшем случае придётся проверить все миллион!

**Решение:** `Dictionary<TKey, TValue>` — структура данных, где можно искать по **ключу** мгновенно.

---

## Часть 2. Знакомство с Dictionary

### Что такое Dictionary?

**Dictionary** — это коллекция **пар "ключ-значение"**.  
Каждый ключ уникален и позволяет быстро найти связанное с ним значение.

**Аналогия из жизни:**
- **Телефонная книга:** ключ = имя человека, значение = номер телефона
- **Словарь:** ключ = слово, значение = его определение
- **Студенческий билет:** ключ = номер студенческого, значение = студент

### Создание Dictionary

```csharp
// Синтаксис: Dictionary<ТипКлюча, ТипЗначения> имя = new Dictionary<ТипКлюча, ТипЗначения>();

// Телефонная книга: ключ - string (имя), значение - string (номер)
Dictionary<string, string> phoneBook = new Dictionary<string, string>();

// Оценки студентов: ключ - string (имя), значение - int (балл)
Dictionary<string, int> grades = new Dictionary<string, int>();

// ID пользователя и его возраст: ключ - int (ID), значение - int (возраст)
Dictionary<int, int> userAges = new Dictionary<int, int>();
```

### Добавление элементов

```csharp
Dictionary<string, string> phoneBook = new Dictionary<string, string>();

// Способ 1: метод Add (ключ не должен существовать)
phoneBook.Add("Анна", "+7-123-456-78-90");
phoneBook.Add("Петр", "+7-234-567-89-01");

// Способ 2: через индексатор (если ключ есть - перезапишет, если нет - добавит)
phoneBook["Мария"] = "+7-345-678-90-12";
phoneBook["Анна"] = "+7-999-999-99-99";  // изменит телефон Анны
```

### Получение значений

```csharp
// Через индексатор (если ключа нет - будет ошибка!)
string annaPhone = phoneBook["Анна"];  // "+7-999-999-99-99"

// Безопасный способ через TryGetValue (ошибки не будет)
if (phoneBook.TryGetValue("Иван", out string ivanPhone))
{
    Console.WriteLine($"Телефон Ивана: {ivanPhone}");
}
else
{
    Console.WriteLine("Иван не найден в телефонной книге");
}
```

### Проверка наличия ключа

```csharp
// ContainsKey - проверяет, есть ли такой ключ
if (phoneBook.ContainsKey("Петр"))
{
    Console.WriteLine("Петр есть в книге");
}

// ContainsValue - проверяет, есть ли такое значение (медленно!)
if (phoneBook.ContainsValue("+7-123-456-78-90"))
{
    Console.WriteLine("Этот номер уже используется");
}
```

### Удаление элементов

```csharp
// Удаление по ключу
bool removed = phoneBook.Remove("Анна");

// Удаление всех элементов
phoneBook.Clear();

// Количество элементов
int count = phoneBook.Count;
```

### Перебор Dictionary

```csharp
Dictionary<string, int> grades = new Dictionary<string, int>();
grades["Анна"] = 95;
grades["Петр"] = 87;
grades["Мария"] = 92;

// Перебор по парам KeyValuePair
foreach (KeyValuePair<string, int> pair in grades)
{
    Console.WriteLine($"Студент: {pair.Key}, Балл: {pair.Value}");
}

// Перебор только ключей
foreach (string name in grades.Keys)
{
    Console.WriteLine(name);
}

// Перебор только значений
foreach (int score in grades.Values)
{
    Console.WriteLine(score);
}
```

---

## Часть 3. Когда использовать Dictionary (и когда нет)

### ✅ Хорошо подходит для:

1. **Поиск по уникальному ключу** (телефонная книга, словарь)
2. **Подсчёт количества вхождений** (сколько раз встречается слово)
3. **Кэширование результатов** (запомнить результат дорогой операции)
4. **Группировка данных** (студенты по группам)

### ❌ Не подходит для:

1. **Хранения данных, где порядок важен** (Dictionary не гарантирует порядок)
2. **Поиска по значению** (для этого нужен другой подход)
3. **Хранения небольших коллекций** (массив будет быстрее из-за накладных расходов)

### Пример: подсчёт частоты слов

```csharp
// Задача: посчитать, сколько раз каждое слово встречается в тексте
string text = "яблоко банан яблоко апельсин банан банан";
string[] words = text.Split(' ');

Dictionary<string, int> wordCount = new Dictionary<string, int>();

foreach (string word in words)
{
    if (wordCount.ContainsKey(word))
    {
        wordCount[word]++;  // увеличиваем счётчик
    }
    else
    {
        wordCount[word] = 1;  // добавляем новое слово
    }
}

// Вывод: яблоко -> 2, банан -> 3, апельсин -> 1
foreach (var pair in wordCount)
{
    Console.WriteLine($"{pair.Key}: {pair.Value}");
}
```

---

## Часть 4. Знакомство с HashSet

### Что такое HashSet?

**HashSet** — это коллекция **уникальных элементов**. В ней нет пар "ключ-значение", только сами элементы, но каждый элемент может встречаться только один раз.

**Аналогия из жизни:**
- **Список приглашённых на вечеринку** — один человек не может быть в списке дважды
- **Набор уникальных чисел** — множество неповторяющихся значений
- **Черный список** — каждый элемент уникален

### Создание HashSet

```csharp
// Синтаксис: HashSet<ТипЭлемента> имя = new HashSet<ТипЭлемента>();

// Множество уникальных имён
HashSet<string> uniqueNames = new HashSet<string>();

// Множество уникальных чисел
HashSet<int> uniqueNumbers = new HashSet<int>();
```

### Добавление элементов

```csharp
HashSet<string> invitedGuests = new HashSet<string>();

// Add возвращает true, если элемент добавился, и false, если уже был
bool added1 = invitedGuests.Add("Анна");   // true - добавилась
bool added2 = invitedGuests.Add("Петр");   // true - добавился
bool added3 = invitedGuests.Add("Анна");   // false - уже есть!

Console.WriteLine(invitedGuests.Count);    // 2
```

### Проверка наличия элемента

```csharp
HashSet<string> blackList = new HashSet<string>();
blackList.Add("spammer@mail.com");
blackList.Add("bot@fake.com");

// Contains - быстрая проверка (мгновенно!)
if (blackList.Contains("spammer@mail.com"))
{
    Console.WriteLine("Этот пользователь в чёрном списке");
}
```

### Удаление элементов

```csharp
HashSet<int> numbers = new HashSet<int> { 1, 2, 3, 4, 5 };

// Удаление конкретного элемента
bool removed = numbers.Remove(3);   // true - удалили

// Удаление всех элементов
numbers.Clear();

// Количество элементов
int count = numbers.Count;
```

### Перебор HashSet

```csharp
HashSet<string> fruits = new HashSet<string>();
fruits.Add("яблоко");
fruits.Add("банан");
fruits.Add("апельсин");

foreach (string fruit in fruits)
{
    Console.WriteLine(fruit);
}
// Порядок не гарантируется!
```

---

## Часть 5. Операции с множествами (HashSet)

HashSet поддерживает мощные операции над множествами, знакомые из математики.

### Объединение (Union)

```csharp
HashSet<int> setA = new HashSet<int> { 1, 2, 3 };
HashSet<int> setB = new HashSet<int> { 3, 4, 5 };

// UnionWith: добавляем все элементы из B в A (изменяет A)
setA.UnionWith(setB);  // setA стал { 1, 2, 3, 4, 5 }
```

### Пересечение (Intersection)

```csharp
HashSet<int> setA = new HashSet<int> { 1, 2, 3, 4 };
HashSet<int> setB = new HashSet<int> { 3, 4, 5, 6 };

// IntersectWith: оставляем только элементы, которые есть в обоих множествах
setA.IntersectWith(setB);  // setA стал { 3, 4 }
```

### Разность (Except)

```csharp
HashSet<int> setA = new HashSet<int> { 1, 2, 3, 4 };
HashSet<int> setB = new HashSet<int> { 3, 4, 5, 6 };

// ExceptWith: удаляем из A элементы, которые есть в B
setA.ExceptWith(setB);  // setA стал { 1, 2 }
```

### Симметрическая разность (SymmetricExcept)

```csharp
HashSet<int> setA = new HashSet<int> { 1, 2, 3, 4 };
HashSet<int> setB = new HashSet<int> { 3, 4, 5, 6 };

// SymmetricExceptWith: оставляем элементы, которые есть только в одном из множеств
setA.SymmetricExceptWith(setB);  // setA стал { 1, 2, 5, 6 }
```

### Проверка подмножества/надмножества

```csharp
HashSet<int> setA = new HashSet<int> { 1, 2, 3 };
HashSet<int> setB = new HashSet<int> { 1, 2, 3, 4, 5 };

// IsSubsetOf: все ли элементы A есть в B?
bool isSubset = setA.IsSubsetOf(setB);  // true

// IsSupersetOf: все ли элементы B есть в A?
bool isSuperset = setA.IsSupersetOf(setB);  // false

// Overlaps: есть ли хотя бы один общий элемент?
bool overlaps = setA.Overlaps(new HashSet<int> { 4, 5, 6 });  // false

// SetEquals: множества равны?
bool equal = setA.SetEquals(new HashSet<int> { 1, 2, 3 });  // true
```

---

## Часть 6. Сравнение Dictionary и HashSet

| Характеристика | Dictionary | HashSet |
|----------------|------------|---------|
| **Хранит** | Пары "ключ-значение" | Только уникальные элементы |
| **Доступ** | По ключу | Проверка наличия элемента |
| **Когда использовать** | Нужно связать ключ со значением | Нужно хранить множество уникальных значений |
| **Пример** | Телефонная книга, словарь | Чёрный список, список уникальных ID |

### Пример выбора:

```csharp
// Ситуация 1: нужно найти возраст по имени → Dictionary
Dictionary<string, int> ageByName = new Dictionary<string, int>();
ageByName["Анна"] = 25;
int age = ageByName["Анна"];

// Ситуация 2: нужно проверить, был ли пользователь уже на сайте → HashSet
HashSet<int> visitedUserIds = new HashSet<int>();
visitedUserIds.Add(123);
if (visitedUserIds.Contains(123))
{
    Console.WriteLine("Пользователь уже был");
}
```

---

## Часть 7. Практические примеры

### Пример 1: Уникальные слова в тексте

```csharp
string text = "кот собака кот рыба птица собака";
string[] words = text.Split(' ');

HashSet<string> uniqueWords = new HashSet<string>();

foreach (string word in words)
{
    uniqueWords.Add(word);
}

Console.WriteLine($"Уникальных слов: {uniqueWords.Count}");  // 4
// Вывод слов: кот, собака, рыба, птица
```

### Пример 2: Пересечение интересов (общие друзья)

```csharp
HashSet<string> annaFriends = new HashSet<string> { "Петр", "Иван", "Мария" };
HashSet<string> petrFriends = new HashSet<string> { "Иван", "Мария", "Сергей" };

// Общие друзья Анны и Петра
HashSet<string> commonFriends = new HashSet<string>(annaFriends);
commonFriends.IntersectWith(petrFriends);

foreach (string friend in commonFriends)
{
    Console.WriteLine(friend);  // Иван, Мария
}
```

### Пример 3: Телефонная книга с несколькими номерами

```csharp
// Храним несколько телефонов для одного человека
Dictionary<string, List<string>> phoneBook = new Dictionary<string, List<string>>();

// Добавляем номер
void AddPhone(string name, string phone)
{
    if (!phoneBook.ContainsKey(name))
    {
        phoneBook[name] = new List<string>();
    }
    phoneBook[name].Add(phone);
}

AddPhone("Анна", "+7-123-456-78-90");
AddPhone("Анна", "+7-234-567-89-01");
AddPhone("Петр", "+7-345-678-90-12");

// Выводим все номера Анны
if (phoneBook.TryGetValue("Анна", out List<string> phones))
{
    Console.WriteLine("Телефоны Анны:");
    foreach (string phone in phones)
    {
        Console.WriteLine(phone);
    }
}
```

### Пример 4: Проверка дубликатов в массиве

```csharp
int[] numbers = { 1, 2, 3, 4, 2, 5, 6, 3 };
HashSet<int> seen = new HashSet<int>();
List<int> duplicates = new List<int>();

foreach (int num in numbers)
{
    if (!seen.Add(num))  // если Add вернул false, значит элемент уже был
    {
        duplicates.Add(num);
    }
}

Console.WriteLine("Дубликаты: " + string.Join(", ", duplicates));  // 2, 3
```

---

## Часть 8. Быстродействие: почему Dictionary и HashSet быстрые?

### Сравнение с массивами

| Операция | Массив (поиск по значению) | Dictionary/HashSet |
|----------|---------------------------|---------------------|
| Поиск элемента | O(n) — нужно проверить все | O(1) — мгновенно |
| Добавление | O(1) в конец | O(1) в среднем |
| Удаление | O(n) — нужно сдвигать | O(1) в среднем |

**O(n)** означает, что время растёт пропорционально количеству элементов.  
**O(1)** означает, что время не зависит от количества элементов.

### Почему это важно?

```csharp
// С массивом: 1 000 000 элементов = 1 000 000 проверок
string[] names = new string[1_000_000];
// ... заполняем ...
string searchName = "Анна";
for (int i = 0; i < names.Length; i++)  // до миллиона итераций!
{
    if (names[i] == searchName) { /* нашли */ }
}

// С Dictionary: 1 000 000 элементов = 1 проверка
Dictionary<string, string> phoneBook = new Dictionary<string, string>();
// ... заполняем ...
string phone = phoneBook["Анна"];  // мгновенно!
```

**Вывод:** когда нужен частый поиск, Dictionary и HashSet незаменимы.

---

## Часть 9. Распространённые ошибки и как их избежать

### Ошибка 1: Обращение к несуществующему ключу в Dictionary

```csharp
Dictionary<string, int> scores = new Dictionary<string, int>();
// scores["Анна"] = 95;  // забыли добавить

// Ошибка! KeyNotFoundException
int annaScore = scores["Анна"];
```

**Исправление:**
```csharp
// Способ 1: проверка ContainsKey
if (scores.ContainsKey("Анна"))
{
    int score = scores["Анна"];
}

// Способ 2: TryGetValue
if (scores.TryGetValue("Анна", out int score))
{
    Console.WriteLine(score);
}
else
{
    Console.WriteLine("Анна не найдена");
}
```

### Ошибка 2: Добавление дубликата через Add

```csharp
Dictionary<string, string> dict = new Dictionary<string, string>();
dict.Add("ключ", "значение");
dict.Add("ключ", "другое значение");  // Ошибка! KeyAlreadyExistsException
```

**Исправление:**
```csharp
// Способ 1: проверка перед добавлением
if (!dict.ContainsKey("ключ"))
{
    dict.Add("ключ", "значение");
}

// Способ 2: использовать индексатор (перезапишет)
dict["ключ"] = "значение";  // если был - перезапишет, нет - добавит
```

### Ошибка 3: Изменение коллекции во время перебора

```csharp
Dictionary<string, int> dict = new Dictionary<string, int>();
dict["A"] = 1;
dict["B"] = 2;
dict["C"] = 3;

// Ошибка! InvalidOperationException
foreach (var pair in dict)
{
    if (pair.Value == 2)
    {
        dict.Remove(pair.Key);  // нельзя изменять коллекцию во время перебора
    }
}
```

**Исправление:**
```csharp
// Способ 1: собрать ключи для удаления
List<string> keysToRemove = new List<string>();
foreach (var pair in dict)
{
    if (pair.Value == 2)
    {
        keysToRemove.Add(pair.Key);
    }
}
foreach (string key in keysToRemove)
{
    dict.Remove(key);
}

// Способ 2: использовать новый словарь
var newDict = new Dictionary<string, int>();
foreach (var pair in dict)
{
    if (pair.Value != 2)
    {
        newDict[pair.Key] = pair.Value;
    }
}
dict = newDict;
```

### Ошибка 4: Надежда на порядок элементов

```csharp
Dictionary<string, int> dict = new Dictionary<string, int>();
dict["B"] = 2;
dict["A"] = 1;
dict["C"] = 3;

foreach (var pair in dict)
{
    Console.WriteLine(pair.Key);
}
// Может вывести: B, A, C или A, B, C или C, B, A
// Порядок не гарантируется!
```

**Исправление:** если нужен порядок, используйте `SortedDictionary` или `List`.

---

## Часть 10. Задачи для самостоятельного решения

### Задача 1. Подсчёт букв в строке

Дана строка: `"программирование"`.  
Используя `Dictionary<char, int>`, посчитайте, сколько раз встречается каждая буква.

**Ожидаемый результат (для примера):**  
п → 1, р → 2, о → 2, г → 1, а → 1, м → 2, и → 2, в → 1, н → 1, е → 1

---

### Задача 2. Уникальные элементы массива

Дан массив: `[5, 2, 8, 2, 9, 5, 1, 8, 3]`.  
Используя `HashSet<int>`, выведите все уникальные числа.

**Ожидаемый результат:** `5, 2, 8, 9, 1, 3`

---

### Задача 3. Пересечение массивов

Даны два массива:  
`[1, 2, 3, 4, 5, 6]`  
`[4, 5, 6, 7, 8, 9]`

Используя `HashSet`, найдите числа, которые есть в обоих массивах.

**Ожидаемый результат:** `4, 5, 6`

---

### Задача 4. Телефонная книга (поиск по имени)

Реализуйте программу, которая:
1. Хранит телефонную книгу в `Dictionary<string, string>`
2. Позволяет добавить контакт (имя → телефон)
3. Позволяет найти телефон по имени (с проверкой существования)
4. Позволяет удалить контакт

**Пример работы:**
```
1 - Добавить контакт
2 - Найти телефон
3 - Удалить контакт
4 - Выйти
Выберите действие: 1
Введите имя: Анна
Введите телефон: +7-123-456-78-90
Контакт добавлен

Выберите действие: 2
Введите имя: Анна
Телефон Анны: +7-123-456-78-90
```

---

### Задача 5. Проверка на дубликаты

Напишите функцию `bool HasDuplicates(int[] array)`, которая использует `HashSet<int>` для проверки, есть ли в массиве повторяющиеся элементы.

**Примеры:**
- `[1, 2, 3, 4, 5]` → `false`
- `[1, 2, 3, 2, 4]` → `true`

---

### Задача 6. Группировка слов по первой букве

Дан массив слов:  
`["яблоко", "банан", "апельсин", "арбуз", "баклажан", "вишня"]`

Используя `Dictionary<char, List<string>>`, сгруппируйте слова по первой букве.

**Ожидаемый результат:**
```
я: яблоко
б: банан, баклажан
а: апельсин, арбуз
в: вишня
```

---

### Задача 7*. Частота символов с игнорированием регистра

Дана строка: `"Мама мыла раму"`.  
Подсчитайте частоту каждого символа, игнорируя регистр (считайте 'М' и 'м' одинаковыми) и игнорируя пробелы.

**Подсказка:** используйте `char.ToLower()` для приведения к нижнему регистру.

---

### Задача 8*. Разность массивов

Даны два массива:  
`[1, 2, 3, 4, 5, 6]`  
`[4, 5, 6, 7, 8, 9]`

Используя `HashSet`, найдите:
1. Элементы, которые есть в первом массиве, но нет во втором
2. Элементы, которые есть во втором массиве, но нет в первом

**Ожидаемый результат:**
1. `1, 2, 3`
2. `7, 8, 9`

---

### Задача 9*. Студенческая ведомость

Создайте `Dictionary<string, int>`, где ключ — фамилия студента, значение — оценка.  
Напишите программу, которая:
1. Добавляет 5 студентов с разными оценками
2. Находит средний балл всех студентов
3. Находит студента с максимальной оценкой
4. Находит всех студентов, у которых оценка выше среднего

**Подсказка:** для поиска максимума можно перебрать все пары в цикле.

---

### Задача 10*. Анаграммы

Два слова называются анаграммами, если они состоят из одинаковых букв (например, "кот" и "ток").  
Напишите функцию `bool AreAnagrams(string a, string b)`, которая использует `Dictionary<char, int>` для подсчёта букв и сравнивает два слова.

**Примеры:**
- `"кот", "ток"` → `true`
- `"кот", "тот"` → `false`

---

## Шпаргалка (можно распечатать)

### Dictionary
```csharp
// Создание
Dictionary<string, int> dict = new Dictionary<string, int>();

// Добавление
dict.Add("ключ", 123);      // если ключ есть - ошибка
dict["ключ"] = 123;          // если есть - перезапишет

// Получение
int value = dict["ключ"];    // если нет - ошибка
dict.TryGetValue("ключ", out int val);  // безопасно

// Проверка
bool hasKey = dict.ContainsKey("ключ");
bool hasValue = dict.ContainsValue(123);

// Удаление
dict.Remove("ключ");
dict.Clear();

// Количество
int count = dict.Count;

// Перебор
foreach (KeyValuePair<string, int> pair in dict)
{
    Console.WriteLine($"{pair.Key}: {pair.Value}");
}
```

### HashSet
```csharp
// Создание
HashSet<int> set = new HashSet<int>();

// Добавление
bool added = set.Add(5);  // true - добавился, false - уже был

// Проверка
bool has = set.Contains(5);

// Удаление
bool removed = set.Remove(5);
set.Clear();

// Количество
int count = set.Count;

// Перебор
foreach (int item in set)
{
    Console.WriteLine(item);
}

// Операции с множествами
setA.UnionWith(setB);           // объединение
setA.IntersectWith(setB);       // пересечение
setA.ExceptWith(setB);          // разность
setA.SymmetricExceptWith(setB); // симметрическая разность

setA.IsSubsetOf(setB);          // подмножество?
setA.IsSupersetOf(setB);        // надмножество?
setA.Overlaps(setB);            // есть общие?
setA.SetEquals(setB);           // равны?
```
