# Колекції

Мова Swift надає три основні _типи колекцій_, відомих як масиви \(`Array`\), множини \(`Set`\) та словники \(`Dictionary`\), для зберігання колекцій значень. Масиви - це впорядковані колекції значень. Множини - це невпорядковані колекції унікальних значень. Словники - це невпорядковані колекції асоціацій ключ-значення.

![](../.gitbook/assets/CollectionTypes_intro_2x.png) ￼ При роботі із масивами, множинами та словниками у Swift завжди зрозуміло, які типи значень та ключів вони можуть зберігати. Це означає, що ви не можете помилково вставити значення невідповідного типу в колекцію. Це також означає, що ви можете бути впевнені щодо типів значень, котрі ви отримуєте з колекції.

> **Примітка**
>
> У Swift масиви, множини та словники реалізовано як _узагальнені колекції_. Щоб дізнатись більше про узагальнені типи й колекції, дивіться розділ [Узагальнення](21_generics.md).

## Змінюваність колекцій

Якщо створити масив, множину чи словник, та присвоїти їх змінній, створена колекція буде _змінюваною_. Це значить, що після її створення її вміст можна змінити додаючи, видаляючи чи міняючи елементи колекції. Якщо присвоїти масив, множину чи словник константі, колекція буде _незмінюваною_, і її розмір чи вміст не можна буде змінити.

> **Примітка**
>
> Гарною практикою є створення незмінюваних колекцій в усіх випадках, де колекція не потребує змін. Цей підхід полегшує вам міркування про ваш код та дозволяє компілятору Swift оптимізувати швидкодію колекцій, що ви створюєте.

## Масиви

_Масив_ зберігає значення однакового типу в упорядкованому списку. Одне й те ж значення може з'являтись у масиві кілька разів у різних позиціях.

> **Примітка**
>
> Існує міст між типом `Array` у Swift та класом `NSArray` у Foundation.
>
> За детальнішою інформацією про використання типу `Array` із Foundation та Cocoa, дивіться розділ "Working with Cocoa Data Types" в книзі _Using Swift with Cocoa and Objective-C \(Swift 3.0.1\)_

### Скорочений синтаксис масивів

Тип масиву у Swift у повній формі записується як `Array<Element>`, де `Element` - це тип значень, що можуть зберігатись в масиві. Також тип масиву можна записати у скороченій формі як `[Element]`. Хоч обидві форми і є функціонально ідентичними, бажано вживати коротку форму, і саме вона переважно використовується в цій книзі для опису типу масивів.

### Створення порожнього масиву

Щоб створити порожній масив певного типу, можна використати синтаксис ініціалізації:

```swift
var someInts = [Int]()
print("someInts має тип [Int] та містить \(someInts.count) елементів.")
// Надрукує "someInts має тип [Int] та містить 0 елементів."
```

Слід зазначити, що тип змінної `someInts` визначено як `[Int]` із типу ініціалізатора.

Іншим способом ініціалізації, за умови що контекст вже надає інформацію про тип, наприклад як аргумент функції чи заздалегідь типізована змінна чи константа, є створення порожнього масиву за допомогою літерала порожнього масиву, що записується як `[]` \(порожня пара квадратних дужок\).

```swift
someInts.append(3)
// someInts тепер містить 1 значення типу Int
someInts = []
// someInts тепер порожній масив, проте досі має тип [Int]
```

### Створення масиву зі значенням за замовчанням

Тип `Array` у Swift також надає ініціалізатор для створення масиву певного розміру зі всіма його значеннями рівними одному значенню за замовчанням. В цей ініціалізатор передається значення за замовчанням відповідного типу \(називається `repeating:`\) та кількість повторів цього значення в новому масиві \(називається `count:`\):

```swift
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles тепер має тип [Double], і дорівнює [0.0, 0.0, 0.0]
```

### Створення масиву складаючи два масиви

Нові масиви можна створювати складаючи два масиви, що вже існують і мають сумісні типи, за допомогою оператору додавання \(`+`\). Новий масив має тип, визначений із типів двох масивів, що складаються:

```swift
var anotherThreeDoubles = Array(repeating: 2.5, count: 3)
// anotherThreeDoubles має тип [Double], та дорівнює [2.5, 2.5, 2.5]

var sixDoubles = threeDoubles + anotherThreeDoubles
// тип змінної sixDoubles визначено як [Double], і вона дорівнює [0.0, 0.0, 0.0, 2.5, 2.5, 2.5]
```

### Створення масиву за допомогою літералу масиву

Можна також створити масив за допомогою _літералу масиву_, що є скороченим способом записати одне чи більше значень як масив. Літерал масиву записується як список значень, розділений комами, оточений парою квадратних дужок:

\[значення 1, значення 2, значення 3\]&lt;/span&gt;

У прикладі нижче створено масив з назвою `shoppingList`, котрий зберігає значення типу `String`:

```swift
var shoppingList: [String] = ["Яйця", "Молоко"]
// shoppingList було проініціалізовано двома початковими елементами
```

Змінну `shoppingList` оголошено як "масив рядкових значень", що записано як `[String]`. Оскільки цей конкретний масив визначає тип своїх значень як `String`, у ньому можуть міститись тільки значення типу `String`. Тут, масив `shoppingList` ініціалізовано двома значеннями типу `String` \(`"Eggs"` та `"Milk"`\), що записані всередині літералу масиву.

> **Примітка**
>
> Масив `shoppingList` оголошено як змінну \(за допомогою інструкції `var`\), а не константою \(за допомогою інструкції `let`\), бо у наступних прикладах у нього будуть додаватись нові елементи.

У цьому випадку, літерал масиву містить два рядки й більше нічого. Це відповідає типу змінної `shoppingList` \(масив, що можна містити тільки значення `String`\), і тому присвоєння літералу масиву дозволяється як спосіб проініціалізувати `shoppingList` двома початковими елементами.

Дякуючи Богу та визначенню типів у Swift, не вам потрібно завжди писати тип масиву, якщо ви ініціалізуєте його літералом масиву, що містить значення одного й того ж типу. Ініціалізацію масиву `shoppingList` можна було записати у коротшій формі:

```swift
var shoppingList = ["Яйця", "Молоко"]
```

Оскільки всі значення літералу масиву мають однаковий тип, Swift може визначити, що `[String]` є коректним типом для змінної `shoppingList`.

### Доступ до елементів масиву та його модифікація

Щоб отримати доступ до елементів масиву чи змінити їх, слід користуватись методами та властивостями масиву, або синтаксисом його індексації.

Щоб дізнатись кількість елементів у масиві, слід перевірити його властивість `count`:

```swift
print("Список покупок містить \(shoppingList.count) елементи.")
// Надрукує "Список покупок містить 2 елементи."
```

Булева властивість `isEmpty` є скороченим записом перевірки, чи дорівнює властивість `count` нулю:

```swift
if shoppingList.isEmpty {
    print("Список покупок порожній.")
} else {
    print("Список покупок не порожній.")
}
// Надрукує "Список покупок не порожній."
```

Можна додавати нові елементи в кінець масиву, викликаючи метод `append(_:)`:

```text
shoppingList.append("Борошно")
// shoppingList тепер містить 3 елементи, і, схоже, хтось готує млинці
```

Також можна додавати в кінець масив з одного чи кількох сумісних елементів за допомогою оператора додавання з присвоєнням \(`+=`\):

```swift
shoppingList += ["Пекарний порошок"]
// shoppingList тепер містить 4 елементів
shoppingList += ["Шоколадна паста", "Сир", "Вершкове масло"]
// shoppingList тепер містить 7 елементів
```

Отримати значення із масиву можна за допомогою _синтаксису індексації_, передаючи індекс значення, котре слід отримати, у квадратних дужках одразу після імені масиву:

```swift
var firstItem = shoppingList[0]
// firstItem тепер дорівнює "Яйця"
```

> **Примітка**
>
> Перший елемент у масиві має індекс `0`, а не `1`. Масиви у Swift індексуються, починаючи з нуля.

Синтаксис індексації також можна використовувати для зміни наявного значення за заданим індексом:

```text
shoppingList[0] = "Шість яєць"
// перший елемент у масиві тепер дорівнює "Шість яєць", а не "Яйця"
```

Також можна використовувати синтаксис індексації для зміни одразу кількох значень у заданому діапазоні, навіть якщо довжина нового діапазону значень відрізняється. Наступний приклад замінює `"Шоколадна паста"`, `"Сир"`, та `"Вершкове масло"` на `"Банани"` та `"Яблука"`:

```swift
shoppingList[4...6] = ["Банани", "Яблука"]
// shoppingList тепер містить 6 елементів
```

> **Примітка**
>
> Синтаксис індексації не може використовуватись для додавання нових елементів у кінець масиву.

Щоб вставити елемент до масиву за вказаним індексом, слід викликати метод `insert(_:at:)`:

```swift
shoppingList.insert("Кленовий сироп", at: 0)
// shoppingList тепер містить 7 елементів
// "Кленовий сироп" тепер є першим елементом у масиві
```

Цей виклик методу `insert(_:at:)` вставляє новий елемент зі значенням `"Кленовий сироп"` в самий початок списку покупок, що вказується за допомогою індексу `0`.

Аналогічно, щоб видалити елемент із масиву, слід викликати метод `remove(at:)`. Цей метод видаляє елемент за вказаним індексом і повертає видалений елемент \(хоча ви можете проігнорувати повернене значення, якщо воно вам не потрібне\):

```swift
let mapleSyrup = shoppingList.remove(at: 0)
// елемент, що був за індексом 0, щойно було видалено.
// shoppingList тепер містить 6 елементів, і не містить "Кленовий сироп"
// константа mapleSyrup тепер дорівнює видаленому рядку "Кленовий сироп"
```

> **Примітка**
>
> Якщо ви спробуєте отримати чи змінити елемент за індексом, що не входить в існуючі межі масиву, ви спровокуєте помилку часу виконання. Ви можете перевірити, чи є коректним індекс перед його використанням, порівнявши його із властивістю масиву `count`. Окрім випадку, коли `count` дорівнює `0` \(що означає, що масив порожній\), найбільший коректний індекс у масиві завжди буде дорівнювати `count - 1`, оскільки масиви індексуються, починаючи із нуля.

При видаленні елементу будь-які прогалини у масиві закриваються, тому значення за індексом `0` знову дорівнює `"Шість яєць"`:

```swift
firstItem = shoppingList[0]
// firstItem тепер дорівнює "Шість яєць"
```

Якщо потрібно видалити останній елемент у масиві, слід використовувати метод `removeLast()` замість методу `remove(at:)`, щоб уникнути зайвого звертання до властивості масиву `count`. Як і метод `remove(at:)`, метод `removeLast()` повертає видалений елемент:

```swift
let apples = shoppingList.removeLast()
// останній елемент у масиві було щойно видалено
// shoppingList тепер містить 5 елементів, і жодного яблука
// константа apples тепер дорівнює видаленому рядку "Яблука"
```

### Ітерування масиву

Щоб проітерувати весь набір елементів масиву, слід використовувати цикл `for`-`in`:

```swift
for item in shoppingList {
    print(item)
}
// Шість яєць
// Молоко
// Борошно
// Пекарний порошок
// Банани
```

Якщо вам потрібен цілочисельний індекс кожного елемента разом із його значенням, слід використовувати метод `enumerated()` та ітерувати його результат. Для кожного елементу масиву, метод `enumerated()` повертає кортеж, що складається із цілого індексу та елемента. Індекси починаються із нуля і збільшуються на одиницю із кожним елементом. Під час ітерації, ви можете розбити кортеж на тимчасові константи чи змінні:

```swift
for (index, value) in shoppingList.enumerated() {
    print("Елемент \(index + 1): \(value)")
}
// Елемент 1: Шість яєць
// Елемент 2: Молоко
// Елемент 3: Борошно
// Елемент 4: Пекарний порошок
// Елемент 5: Банани
```

Більше інформаціє про цикл `for`-`in` можна отримати у підрозділі [Цикл For-In](4_control_flow.md#Цикл-For-In).

## Множини

_Множини_ зберігають неповторні значення одного типу в невпорядкованій колекції. Слід користуватись множинами замість масивів у випадках, коли порядок елементів не є важливим, чи коли слід бути певних у тому, що елементи в колекції не повторюються.

> **Примітка**
>
> Існує міст між типом `Set` у Swift та класом `NSSet` у Foundation. За детальнішою інформацією про використання типу `Set` із Foundation та Cocoa, дивіться розділ "Working with Cocoa Data Types" в книзі _Using Swift with Cocoa and Objective-C \(Swift 3.0.1\)_.

### Значення хешу для множинних типів

Для типу, що може зберігатись у множині, повинна бути визначена операція _хешування_. Іншими словами, тип повинен давати можливість обчислювати значення свого хешу. Значенням хешу є цілочисельне значення, котре збігається для усіх однакових об'єктів, тобто із `a == b`, слідує, що `a.hashValue == b.hashValue`.

Усі базові типи у Swift \(такі як `String`, `Int`, `Double`, та `Bool`\), мають визначену операцію хешування за замовчанням, і можуть використовуватись як типи значень у множинах, чи як ключі у словниках. Випадки у перечисленнях без асоційованих значень \(як описано в розділі [Перечислення](7_enumerations.md)\) також визначають операцію хешування за замовчанням.

> **Примітка**
>
> Ви можете використовувати ваші власні типи як значення у множинах чи як ключі у словниках, реалізувавши у них протокол `Hashable` зі стандартної бібліотеки Swift. Типи, що реалізовують протокол `Hashable`, повинні надати властивість для читання типу `Int` на ім'я `hashValue`. Значення, що повертає `hashValue` не обов'язково повинно бути однаковим під час різних запувків програми, чи у різних програмах.
>
> Оскільки протокол `Hashable` наслідує протокол `Equatable`, типи, що реалізовують протокол `Hashable`, повинні також реалізовувати прокотол `Equatable`, тобто надавати реалізацію оператору рівності \(`==`\). Протокол `Equatable` вимагає, щоб реалізація оператору `==` була відношенням рівності. Це означає, що реалізація `==` повинна задовольняти три наступні умови для будь-яких значень `a`, `b` і `с`:
>
> * `a == a` \(Рефлексивність\)
> * Із `a == b` слідує `b == a` \(Симетричність\)
> * Із `a == b && b == c` слідує `a == c` \(Транзитивність\)
>
> За детальнішою інформацією про реалізацію протоколів, дивіться розділ [Протоколи](20_protocols.md).

### Синтаксис множин

Типи множин у Swift записуються як `Set<Element>`, де `Element` є типом, котрий можна зберігати у множині. На відміну від масивів, множини не мають еквівалентної скороченої форми запису.

### Створення та ініціалізація порожньої множини

Щоб створити порожню множину певного типу, слід використовувати наступний синтаксис:

```swift
var letters = Set<Character>()
print("letters має тип Set<Character> і містить \(letters.count) елементів.")
// Надрукує "letters має тип  Set<Character> і містить 0 елементів."
```

> **Примітка**
>
> Тип змінної `letters` визначається як `Set<Character>`, за типом ініціалізатора.

Іншим способом ініціалізації, за умови що контекст вже надає інформацію про тип, наприклад як аргумент функції чи заздалегідь типізована змінна чи константа, можна створити порожню множину за допомогою літералу порожнього масиву:

```swift
letters.insert("a")
// letters тепер містить 1 одне значення типу Character
letters = []
// letters тепер порожня множина, і все ще має тип Set<Character>
```

### Створення множини за допомогою літералу масиву

Також можна ініціалізувати множину літералом масиву, як скорочений спосіб записати одне або кілька значень як множину.

У наступному прикладі створено множину з іменем `favoriteGenres` для зберігання значень типу `String`:

```swift
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
// змінну favoriteGenres було ініціалізовано як множину із трьома початковими елементами
```

Змінну `favoriteGenres` визначено як “множина значень типу `String`”, що пишеться як `Set<String>`. Оскільки ця конкретна множина визначає тип своїх значень як `String`, у ній можуть зберігатися _тільки_ значення типу `String`. Тут, множину `favoriteGenres` ініціалізовано трьома значеннями типу `String` \(`"Rock"`, `"Classical"`, та `"Hip hop"`\), що записані у формі літералу масиву.

> **Примітка**
>
> Множину `favoriteGenres` оголошено як змінну \(за допомогою інструкції `var`\), а не константою \(за допомогою інструкції `let`\), бо у наступних прикладах у неї будуть додаватись нові елементи.

Множинний тип не може бути визначено із самого по собі літералу масиву, тому тип `Set` оголошується явно. Однак, завдяки виведенню типів у Swift, не потрібно вказувати тип множини у випадку ініціалізації її за допомогою літералу масиву, що містить значення однакового типу. Ініціалізацію змінної `favoriteGenres` можна було б записати коротше наступним чином:

```swift
var favoriteGenres: Set = ["Поп", "Класика", "Хіп-хоп"]
```

Оскільки всі значення в літералі масиву мають однаковий тип, Swift визначає, що єдиним коректним типом змінної `favoriteGenres` має бути `Set<String>`.

### Доступ до елементів множини та її модифікація

Щоб отримати доступ до елементів множини чи змінити їх, слід користуватись її методами та властивостями.

Щоб дізнатись кількість елементів у множині, слід перевірити її властивість `count`:

```swift
print("У мене є \(favoriteGenres.count) улюблених жанрів музики.")
// Надрукує "У мене є 3 улюблених жанрів музики."
```

Булева властивість `isEmpty` є скороченим записом перевірки, чи дорівнює властивість `count` нулю:

```swift
if favoriteGenres.isEmpty {
    print("Що стосується музики, я не вибагливий.")
} else {
    print("У мене є певні музичні вподобання.")
}
// Надрукує "У мене є певні музичні вподобання."
```

Можна додавати нові елементи у множину, викликаючи метод `insert(_:)`:

```swift
favoriteGenres.insert("Джаз")
// favoriteGenres тепер містить 4 елементи
```

Щоб видалити елемент із множини, слід викликати метод множини `remove(_:)`, котрий видаляє елемент, якщо він присутній у множині, ти повертає видалене значення, або повертає `nil` у випадку, якщо множина не містила даного елементу. Також, можна видалити всі елементи множини одразу за допомогою методу `removeAll()`.

```swift
if let removedGenre = favoriteGenres.remove("Поп") {
    print("\(removedGenre)? Я з цим покінчив.")
} else {
    print("Я ніколи цим не переймався.")
}
// Надрукує "Поп? Я з цим покінчив."
```

Щоб перевірити, чи містить множина певний елемент, слів використовувати метод `contains(_:)`.

```swift
if favoriteGenres.contains("Кобзон") {
    print("Гоп-стоп, ми падашлі із-за уґла...")
} else {
    print("Чотири роки, ..., без кобзона!")
}
// Надрукує "Чотири роки, ..., без кобзона!"
```

### Ітерування множини

Елементи множини можна ітерувати за допомогою циклу `for`-`in`:

```swift
for genre in favoriteGenres {
    print("\(genre)")
}
// Хіп-хоп
// Джаз
// Класика
```

Більше інформаціє про цикл `for`-`in` можна отримати у підрозділі [Цикл For-In](4_control_flow.md#Цикл-For-In).

Тип `Set` у Swift не має визначеного порядку. Щоб ітерувати елементи множини у певному порядку, слід використовувати метод `sorted()`, котрий повертає елементи множини у вигляді масиву, впорядкованого за допомогою оператору `<`:

```swift
for genre in favoriteGenres.sorted() {
    print("\(genre)")
}
// Джаз
// Класика
// Хіп-хоп
```

## Операції над множинами

У Swift ефективно реалізовані операції над множинами, такі як об'єднання двох множин, знаходження спільних елементів двох множин, та визначення, чи містять дві множини всі, деякі чи жодного спільного елемента.

### Фундаментальні операції над множинами

Наступна ілюстрація зображує дві множини – `a` та `b` – та результати різних операцій над ними, відображених у вигляді зафарбованих ділянок.

![](../.gitbook/assets/setVennDiagram_2x.png) ￼

* Метод `intersection(_:)` створює нову множину зі значень, спільних для обох множин
* Метод `symmetricDifference(_:)` створює нову множину зі значень, що містяться в одній із множин, але не в обидвох одночасно.
* Метод `union(_:)` створює нову множину зі всіх значень з обох множин.
* Метод `subtracting(_:)` створює нову множину зі значень, що не містяться у вказаній множині.

```swift
let oddDigits: Set = [1, 3, 5, 7, 9]
let evenDigits: Set = [0, 2, 4, 6, 8]
let singleDigitPrimeNumbers: Set = [2, 3, 5, 7]

oddDigits.union(evenDigits).sorted()
// [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
oddDigits.intersection(evenDigits).sorted()
// []
oddDigits.subtracting(singleDigitPrimeNumbers).sorted()
// [1, 9]
oddDigits.symmetricDifference(singleDigitPrimeNumbers).sorted()
// [1, 2, 9]
```

### Входження в множину та рівність множин

Наступна ілюстрація зображує три множини – `a`, `b` and `c` – де ділянки, що перетинаються, відображають спільні елементи множин. Множина `a` є _надмножиною_ множини `b`, бо вона містить усі елементи множини `b`. І навпаки, множина `b` є _підмножиною_ множини `a`, бо всі елементи множини `b` також містяться у множині `a`. Множина `b` _не перетинається_ із множиною `c`, бо вони не містять жодного спільного елементу.

![](../.gitbook/assets/setEulerDiagram_2x.png) ￼

* Оператор “дорівнює” \(`==`\) визначає, чи всі елементи двох множин співпадають.
* Метод `isSubset(of:)` визначає, чи є дана множина підмножиною вказаної, тобто чи всі значення даної множини містяться у вказаній множині.
* Метод `isSuperset(of:)` визначає, чи є дана множина надмножиною вказаної, тобто чи всі значення вказаної множини містяться у даній множині.
* Методи `isStrictSubset(of:)` та `isStrictSuperset(of:)` визначають, чи є дана множина підмножиною/надмножиною вказаної, яка при цьому не дорівнює вказаній множині.
* Метод `isDisjoint(with:)` визначає, чи перетинаються множини, тобто чи мають вони спільні елементи. 

```swift
let houseAnimals: Set = ["🐶", "🐱"]
let farmAnimals: Set = ["🐮", "🐔", "🐑", "🐶", "🐱"]
let cityAnimals: Set = ["🐦", "🐭"]

houseAnimals.isSubset(of: farmAnimals)
// true
farmAnimals.isSuperset(of: houseAnimals)
// true
farmAnimals.isDisjoint(with: cityAnimals)
// true
```

## Словники

_Словники_ зберігають асоціації між ключами одного типу та значеннями одного типу у колекції без визначеного впорядкування. Кожне значення асоціюється з унікальним ключем `key`, що діє як ідентифікатор для цього значення у словнику. На відміну від масивів, елементи у словнику не мають вказаного порядку. Слід користуватись словниками, коли потрібно шукати значення на основі їх ідентифікатора, майже так само як у реальному світі словники використовуються для пошуку тлумачення певного слова.

> **Примітка**
>
> > Існує міст між типом `Dictionary` у Swift та класом `NSDictionary` у Foundation.
>
> За детальнішою інформацією про використання типу `Dictionary` із Foundation та Cocoa, дивіться розділ "Working with Cocoa Data Types" в книзі _Using Swift with Cocoa and Objective-C \(Swift 3.0.1\)_.

### Скорочений синтаксис словників

У Swift тип словнику у повній формі записується як `Dictionary<Key, Value>`, де `Key` є типом значень, що можуть використовуватись як ключ словнику, а `Value` є типом значень, що можуть зберігатись за цими ключами.

> **Примітка**
>
> Ключі `Key` у словниках повинні реалізовувати протокол `Hashable`, так само як значення в множинах.

Тип словнику також можна записувати у скороченій формі як `[Key: Value]`. Хоч обидві форми і є функціонально ідентичними, бажано вживати коротку форму, і саме вона переважно використовується в цій книзі для опису типу словників.

### Створення порожнього словника

Аналогічно до масивів, порожній словник певного типу можна створити за допомогою синтаксису ініціалізації:

```swift
var namesOfIntegers = [Int: String]()
// namesOfIntegers є порожнім словником типу [Int: String]
```

У цьому прикладі створено порожній словник типу `[Int: String]` для зберігання текстових назв цілих чисел. Його ключі мають тип `Int`, а значення – тип `String`.

Якщо в контексті вже є інформація про тип, можна створити порожній словник за допомогою літерала порожнього словника, котрий записується як `[:]` \(двокрапка всередині пари квадратних дужок\):

```swift
namesOfIntegers[16] = "sixteen"
// namesOfIntegers тепер містить 1 пару ключ-значення
namesOfIntegers = [:]
// namesOfIntegers тепер є знову порожнім словником типу [Int: String]
```

### Створення словнику за допомогою літералу словнику

Словники можна ініціалізувати за допомогою _літералу словнику_, котрий має синтаксис, схожий на описаний вище синтаксис літералу масиву. Літерал словнику є скороченим способом записати одну чи більше пару ключ-значення у вигляді словника.

_Пара ключ-значення_ є комбінацією ключа та значення. У літералі словника, ключ та значення в кожній парі ключ-значення розділені двокрапкою. Пари ключ-значення записуються у вигляді списку, розділяються комами, і оточені парою квадратних дужок:

\[ключ 1: значення 1, ключ 2: значення 2, ключ 3: значення 3\]&lt;/span&gt;

У зразку нижче створюється словник, у якому зберігаються імена міжнародних аеропортів. У цьому словнику, ключами є трьохбуквенні коди Міжнародної асоціації повітряного транспорту, а значеннями – імена аеропортів:

```swift
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

Словник `airports` оголошено як словник типу `[String: String]`, що означає “`Dictionary` із ключами типу `String`, і значеннями також типу `String`”.

> **Примітка**
>
> Словник `airports` оголошено як змінну \(за допомогою інструкції `var`\), а не константою \(за допомогою інструкції `let`\), бо у наступних прикладах у нього будуть додаватись нові елементи.

Словник `airports` ініціалізовано з літерала словника, що містить дві пари ключ-значення. Перша пара має ключ `"YYZ"` та значення `"Toronto Pearson"`. Друга пара має ключ `"DUB"` та значення `"Dublin"`.

Літерал словника містить дві пари `String: String`. Ці пари ключ-значення співпадають із типом, визначеним оголошенням змінної `airports` \(словник із ключами тільки типу `String` та значенням тільки типу `String`\), і тому присвоєння літералу словника дозволено як спосіб ініціалізації словника `airports` двома початковими елементами.

Як і з масивами, не потрібно вказувати тип словника, якщо його ініціалізовано літералом словника, чиї ключі та значення мають консистентні типи. Ініціалізацію змінної `airports` можна було б записати у скороченій формі наступним чином:

```swift
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
```

Оскільки всі ключі в літералі мають однаковий тип, так само як і всі змінні мають однаковий тип, Swift може визначити, що єдиним коректним типом для змінної `airports` може бути словник `[String: String]`.

### Доступ до елементів словника та його модифікація

Щоб отримати доступ до елементів словника чи змінити їх, слід користуватись методами та властивостями словника, або синтаксисом його індексації.

Як і з масивом, щоб дізнатись кількість елементів у словнику, слід перевірити його властивість для читання `count`:

```swift
print("Словник airports містить \(airports.count) елементи.")
// Надрукує "Словник airports містить 2 елементи."
```

Булева властивість `isEmpty` є скороченим записом перевірки, чи дорівнює властивість `count` нулю:

```swift
if airports.isEmpty {
    print("Словник airports порожній.")и
} else {
    print("Словник airports не порожній.")
}
// Надрукує "Словник airports не порожній."
```

Щоб додати новий елемент у словник, можна використовувати синтаксис індексації. Для цього потрібно використати новий ключ відповідного типу як індекс, і присвоїти нове значення відповідного типу:

```swift
airports["LHR"] = "London"
// Словник airports тепер містить 3 елементи
```

Синтаксис індексації також можна використовувати для зміни значення, що асоційовано із певним ключем:

```swift
airports["LHR"] = "London Heathrow"
// значення за ключем "LHR" було змінено на "London Heathrow"
```

Можна використовувати метод словника `updateValue(_:forKey:)` як альтернативу синтаксису індексації для додавання значень у словник за певним ключем, чи їх зміни. Як і індекси у прикладах вище, метод `updateValue(_:forKey:)` встановлює значення для ключа якщо його не існує, чи оновлює значення, якщо ключ уже існує. На відміну від індексації, однак, метод `updateValue(_:forKey:)` повертає старе значення після виконання оновлення. Це дозволяє перевірити, відбулась вставка нового значення чи оновлення старого.

Метод `updateValue(_:forKey:)` повертає опціональне значення типу, що відповідає типу значень словника. Для словника, що зберігає значення `String`, наприклад, метод повертає значення типу `String?`, або “опціональний `String`”. Цей опціонал буде містити попереднє значення, якщо воно існувало до виклику методу, або `nil`, якщо його не існувало:

```swift
if let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB") {
    print("Попереднє значення під ключем DUB було \(oldValue).")
}
// Надрукує "Попереднє значення під ключем DUB було Dublin."
```

За допомогою синтаксису індексації також можна повертати значення зі словника за певним ключем. Оскільки можливо запитати ключ, під яким не лежить жодного значення, індекс словника повертає опціональне значення типу, що відповідає значенню словника. Якщо словник містить значення під вказаним ключем, індекс поверне опціональне значення, що містить значення під цим ключем. Інакше, індекс поверне `nil`:

```swift
if let airportName = airports["DUB"] {
    print("Ім'я аеропорту: \(airportName).")
} else {
    print("Цей аеропорт не входить у словник airports.")
}
// Надрукує "Ім'я аеропорту: Dublin Airport."
```

За допомогою синтаксису індексації можна також видаляти пари ключ-значення зі словника, присвоївши значення `nil` для заданого ключа:

```swift
airports["APL"] = "Apple International"
// "Apple International" не є справжнім аеропортом для коду APL, тому видалимо його:
airports["APL"] = nil
// ключ APL та значення під ним було видалено із словника
```

Іншим способом видалити пару ключ-значення зі словника є метод `removeValue(forKey:)`. Цей метод видаляє пару ключ-значення, якщо вона існує, і повертає видалене значення, або `nil` у випадку, коли значення під заданим ключем не існує:

```swift
if let removedValue = airports.removeValue(forKey: "DUB") {
    print("Назва видаленого аеропорту: \(removedValue).")
} else {
    print("Словник airports не містить значення під ключем DUB.")
}
// Надрукує "Назва видаленого аеропорту: Dublin Airport."
```

### Ітерування словника

Щоб проітерувати всі пари ключ-значення словника, слід використовувати цикл `for`-`in`. Кожен елемент у словнику при цьому буде повертатись як кортеж `(key, value)`, і можна описати декомпозицію його елементів у тимчасові константи чи змінні як частину ітерації:

```swift
for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}
// YYZ: Toronto Pearson
// LHR: London Heathrow
```

Більше інформаціє про цикл `for`-`in` можна отримати у підрозділі [Цикл For-In](4_control_flow.md#Цикл-For-In).

Також можна отримати колекції окремо ключів та значень словника за допомогою його властивостей `keys` та `values`, для подальшої їх ітерації:

```swift
for airportCode in airports.keys {
    print("Код аеропорту: \(airportCode)")
}
// Код аеропорту: YYZ
// Код аеропорту: LHR

for airportName in airports.values {
    print("Ім'я аеропорту: \(airportName)")
}
// Ім'я аеропорту: Toronto Pearson
// Ім'я аеропорту: London Heathrow
```

Якщо потрібно використовувати ключі чи значення словника з API, що приймає на вхід масив \(`Array`\), слід проініціалізувати новий масив значенням властивості `keys` чи `values` відповідно:

```swift
let airportCodes = [String](airports.keys)
// airportCodes дорівнює ["YYZ", "LHR"]

let airportNames = [String](airports.values)
// airportNames дорівнює ["Toronto Pearson", "London Heathrow"]
```

У Swift тип `Dictionary` не має визначеного впорядкування. Щоб ітерувати ключі чи значення словнику у визначеному порядку, слід використовувати метод `sorted()` властивостей `keys` чи `values` відповідно.
