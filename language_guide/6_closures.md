# Замикання

_Замикання_ – це самодостатні блоки функціональності, що можуть передаватись та використовуватись у коді. Замикання у Swift є аналогічними блокам в C та Objective-C, і лямбдам в інших мовах програмування.

Замикання можуть захоплювати та зберігати посилання на константи та змінні з контексту, в якому вони оголошені. Цю поведінку називають _замиканням на_ константи та змінні. Swift бере на себе все управління пам'яттю при захопленні змінних.

> **Примітка**
>
> Не слід переживати, якщо ви не знайомі з концепцією захоплення. Вона детально описана нижче у підрозділі [Захоплення значень](6_closures.md#Захоплення-значень).

Глобальні та вкладені функції, які представлені в попередньому розділі [Функції](5_functions.md), є особливими випадками замикань. Загалом, замикання можуть приймати одну із трьох форм:

* Глобальні функції є замиканнями, що мають ім'я та не захоплюють значень.
* Вкладені функції є замиканнями, що мають ім'я та захоплюють значення із функції, де їх оголошено.
* Вирази замикань є безіменними замиканнями, що записуються за допомогою лаконічного синтаксису та можуть захоплювати значення із довколишнього контексту.

У Swift вирази замикань мають чистий, зрозумілий стиль, з оптимізаціями, котрі заохочують короткий, не захаращений синтаксис у найчастіших сценаріях. Ці оптимізації включають:

* Виведення типів параметрів та значення, що повертається, з контексту
* Неявний `return` у замиканнях, що складаються з одного рядка
* Скорочений запис імен аргументів
* Синтаксис прикінцевих замикань

## Вирази замикань

Вкладені функції, як показано у підрозділі [Вкладені функції](5_functions.md#Вкладені-функції), є зручним засобом визначення та іменування самодостатніх блоків коду як частини більшої функції. Однак, іноді корисно писати коротші версії подібних до функцій конструктів без повного оголошення та імені. Це найбільш доречно при роботі із функціями чи методами, що приймають інші функції в якості одного чи кількох аргументів.

Вирази замикань є способом запису замикання по місцю використання за допомогою короткого, зосередженого синтаксису. Синтаксис виразів замикань має кілька оптимізацій, потрібних для запису замикань у найкоротшій формі без втрати ясності чи наміру. Приклади виразів замикань нижче ілюструють ці оптимізації шляхом удосконалення одного прикладу із методом `sorted(by:)` протягом кількох ітерацій, у кожній з яких одна й та ж функціональність буде записана у більш лаконічний спосіб.

### Метод Sorted

Стандартна бібліотека Swift надає метод на ім'я `sorted(by:)`, котрий сортує масив значень відомого типу, в залежності від результату сортуючого замикання, що ви надаєте. Після завершення процесу сортування, метод `sorted(by:)` повертає новий масив такого ж типу і розміру, як і старий, з елементами, котрі відсортовані у відповідному порядку. Початковий масив методом `sorted(by:)` не змінюється.

У прикладі нижче використовується метод `sorted(by:)` для сортування масиву значень `String` у зворотньому алфавітному порядку. Ось початковий масив для сортування:

The closure expression examples below use the `sorted(by:)` method to sort an array of `String` values in reverse alphabetical order. Here’s the initial array to be sorted:

```swift
let names = ["В'ячеслав", "Сергій", "Анна", "Ярослав", "Святослав"]
```

Метод `sorted(by:)` приймає замикання, що бере два аргументи того ж типу, що й вміст масиву, та повертає булеве значення, котре вказує, чи є повинно йти перше значення перед другим після сортування масиву. Замикання, що сортує, повинно повертати `true`, якщо перше значення повинно йти _перед_ другим значенням, і `false` в інакшому випадку.

У цьому прикладі відсортовується масив значень типу `String`, і тому типом замикання, що сортує, має бути функція типу `(String, String) -> Bool`.

Одним зі способів надати замикання, що сортує, є написання звичайної функції коректного типу, і передача її як аргумент в метод `sorted(by:)`:

```swift
func backward(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2
}
var reversedNames = names.sorted(by: backward)
// reversedNames дорівнює ["Ярослав", "Сергій", "Святослав", "В'ячеслав", "Анна"]
```

Якщо перший рядок \(`s1`\) більший за другий рядок \(`s2`\), функція `backward(_:_:)` поверне `true`, вказуючи, що у відсортованому масиві рядок `s1` повинен іти перед рядком `s2`. Для символів у рядку, “більше ніж” означає “з'являється в алфавіті після”. Це означає, що літера `"Б"` є “більшою ніж” літера `"А"`, а рядок `"віл"` є більшим за рядок `"вал"`. Це й призводить до зворотнього алфавітного сортування, де `"Богдан"` знаходиться перед `"Анна"`, і так далі.

Однак, це досить довготривалий шлях для запису того, що є по суті функцією з одного виразу \(`a > b`\). В цьому прикладі було би доречніше записати замикання, що сортує, одразу в місці використання, використовуючи синтаксис виразів замикань.

### Синтаксис виразів замикань

Синтаксис виразів замикань має наступний загальний вигляд:

```swift
{ (<параметри>) -> <тип, що повертається> in
    <інструкції>
}
```

_Параметри_ у виразах замикань можуть бути двонаправленими, але не можуть мати значень за замовчанням. Варіативні параметри також можуть використовуватись у виразах замикань. Кортежі можуть використовуватись і як параметри, і як значення, що повертається.

У наступному прикладі продемонстровано вираз замикання замість функції `backward(_:_:)` з попереднього прикладу:

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
    return s1 > s2
})
```

Слід помітити, що оголошення параметрів та типу, що повертається, для цього замикання є ідентичним до такого ж оголошення в функції `backward(_:_:)`. В обох випадках, це записується як `(s1: String, s2: String) -> Bool`. Однак, для виразів замикань, оголошених по місцю використання, параметри та значення, що повертається, записуються _всередині_ фігурних дужок, а не поза ними.

Початок тіла замикання вводиться ключовим словом `in`. Це ключове слово розмежовує кінець оголошенню параметрів замикання та типу, що воно повертає, та початок тіла замикання.

Оскільки тіло цього замикання є досить коротке, його можна навіть записати в один рядок:

```swift
reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )
```

Приклад вище ілюструє, що весь виклик методу `sorted(by:)` залишився без змін. Круглі дужки досі огортають весь аргумент цього методу. Однак, цей аргумент тепер є замиканням, оголошеним по місцю використання.

### Визначення типу з контексту

Оскільки замикання, що сортує, передається як аргумент в метод, Swift може визначити типи його параметрів та тип, що повертається. Метод `sorted(by:)` викликається на масиві рядків, тому його аргумент повинен бути функцією типу `(String, String) -> Bool`. Це означає, що типи `(String, String)` та `Bool` не обов'язково треба записувати в оголошенні виразу замикання. Оскільки всі ці типи можуть бути визначеними, стрілка повертання та дужки довкола імен параметрів теж можна пропустити:

```swift
reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )
```

При передачі замикання до функції чи методу у вигляді виразу замикання, оголошеного по місцю використання, завжди можна визначити типи його параметрів та тип, що ним повертається. В результаті цього, при передачі замикання до функції чи методу як аргументу, ніколи немає потреби записувати замикання, оголошене по місця використання, у найповнішій його формі.

Проте, якщо ви бажаєте вказувати типи явно, ви завжди можете це зробити, і у випадках, коли це допоможе читачу вашого коду уникнути неоднозначності – це дійсно варто робити. У випадку методу `sorted(by:)`, мета замикання випливає з факту, що відбувається сортування, і тому читач може спокійно припустити, що дане замикання буде працювати зі значеннями типу `String`, оскільки це замикання допомагає сортувати масив рядків.

### Неявний `return` у замиканнях, що складаються з одного рядка

Однорядкові замикання можуть повертати результат свого єдиного виразу неявно, пропускаючи ключове слово `return` у їх оголошенні, як видно у наступній версії попереднього прикладу:

```swift
reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )
```

Тут, функціональний тип аргументу метода `sorted(by:)` дає зрозуміти, що замикання повинно повернути значення типу `Bool`. Оскільки тіло замикання складається з єдиного виразу \(`s1 > s2`\), що повертає значення типу `Bool`, тут відсутня неоднозначність і ключове слово `return` можна пропустити.

### Скорочений запис імен аргументів

Swift автоматично надає імена аргументів замиканням, оголошеним по місцю використання. Ці імена виглядають як `$0`, `$1`, `$2`, і так далі, і їх можна використовувати для посилання на значення аргументів замикання.

При використанні скороченого запису імен аргументів у виразах замикань, можна пропускати список аргументів у їх оголошеннях. Кількість аргументів та їх типи будуть виведені компілятором з відповідного функціонального типу. Ключове слово `in` в таких випадках теж можна пропустити, оскільки вираз замикання складатиметься лише з його тіла:

```swift
reversedNames = names.sorted(by: { $0 > $1 } )
```

В даному випадку, `$0` та `$1` посилаються на перший та другий аргументи типу `String`.

### Методи-оператори

Насправді, є навіть _коротший_ спосіб записати вираз замикання з прикладу вище. У Swift, тип `String` визначає специфічну для рядків реалізацію оператору більше \(`>`\) як метод, що має два параметри типу `String`, та повертає значення типу `Bool`. Це точно співпадає з типом, котрий потребує метод `sorted(by:)`. Таким чином, можна просто передати оператор більше, і Swift автоматично визначить, що потрібно використати саме специфічну для рядків його реалізацію:

```swift
reversedNames = names.sorted(by: >)
```

Детальніше з методами-операторами можна ознайомитись у розділі [Методи-оператори](26_advanced_operators.md#Методи-оператори).

## Синтаксис прикінцевих замикань

Якщо потрібно передати вираз замикання до функції її останнім аргументом, а вираз замикання досить довгий, зручно використовувати синтаксис прикінцевих замикань. Прикінцеве замикання записується після дужок виклику функції, хоч воно і є фактично аргументом функції. При використанні синтаксису прикінцевих замикань, не потрібно вказувати мітку аргументу для цього замикання у виклику функції.

```swift
func someFunctionThatTakesAClosure(closure: () -> Void) {
    // Тут йде тіло функції
}

// Ось як виглядає виклик функції без використання синтаксису прикінцевого замикання:

someFunctionThatTakesAClosure(closure: {
    // Тут йде тіло замикання
})

// Ось як виглядає виклик функції із використанням синтаксису прикінцевого замикання:

someFunctionThatTakesAClosure() {
    // Тут йде тіло прикінцевого замикання
}
```

Замикання, що сортує, з прикладу із підрозділу [Синтаксис виразів замикань](6_closures.md#Синтаксис-виразів-замикань), можна записати зовні дужок методу `sorted(by:)` як прикінцеве замикання:

```swift
reversedNames = names.sorted() { $0 > $1 }
```

Якщо вираз замикання є єдиним аргументом, що передається до функції чи методу, і воно записується у вигляді прикінцевого замикання, то й навіть дужки `()` після імені функції чи методу вказувати необов'язково:

```swift
reversedNames = names.sorted { $0 > $1 }
```

Прикінцеві замикання є найбільш доречними, коли тіло замикання є досить довгим, і його не можна записати одним рядком. Наприклад, у масивах у Swift є метод `map(_:)`, що приймає вираз замикання як єдиний аргумент. Замикання викликається по одному разу для кожного елементу масиву, і повертає відображене значення \(можливо, іншого типу\) для цього елемента. Природа цього відображення та тип, що повертається, має визначатись самим замиканням.

Після застосування замикання до кожного елемента масиву, метод `map(_:)` повертає новий масив, що містить всі нові відображені значення, в тому ж порядку, що й відповідні значення в початковому масиві.

Ось приклад того, як використовуючи метод `map(_:)` з прикінцевими замиканнями, можна перетворити масив цілих чисел у масив рядків таким чином: масив `[16, 58, 510]` використовується для створення нового масиву `["ОдинШість", "П'ятьВісім", "П'ятьОдинНуль"]`:

```swift
let digitNames = [
    0: "Нуль",  1: "Один",  2: "Два", 3: "Три",   4: "Чотири",
    5: "П'ять", 6: "Шість", 7: "Сім", 8: "Вісім", 9: "Дев'ять"
]
let numbers = [16, 58, 510]
```

У коді вище створено словник відображень між цифрами та україномовними версіями їх назв. Також створено масив чисел, готовий для конвертації в рядки.

Тепер з масиву `numbers` можна створити масив значень типу `String`, передаючи вираз замикання до методу масиву `map(_:)` як прикінцеве замикання:

```swift
let strings = numbers.map { 
    (number) -> String in
    var number = number
    var output = ""
    repeat {
        output = digitNames[number % 10]! + output
        number /= 10
    } while number > 0
    return output
}
// тип масиву strings визначено як [String]
// його значення - ["ОдинШість", "П'ятьВісім", "П'ятьОдинНуль"]
```

Метод `map(_:)` викликає замикання по одному разу для кожного елементу масиву. Немає потреби вказувати тип вхідного параметра замикання \(`number`\), бо його можна визначити з типу значень масиву, елементи якого відображаються.

В даному прикладі, змінна `number` ініціалізується значенням параметру замикання `number`, для того, щоб це значення можна було змінити в тілі замикання. \(Параметри функції та замикань є завжди константами\). В оголошенні виразу замикання також вказується тип, що повертається – `String` – по ньому визначається тип значень, що зберігатимуться в новому масиві.

Вираз замикання будує рядок на ім'я `output` при кожному виклику. Він обчислює останню цифру числа `number` за допомогою оператора остачі від ділення \(`number % 10`\), і використовує цю цифру для знаходження у словнику `digitNames` рядка, що їй відповідає. Дане замикання можна використовувати для створення текстового представлення будь-якого додатного цілого числа.

> **Примітка**
>
> Після виклику індексу словника `digitNames` вказано знак оклику \(`!`\), бо індекси словників повертають опціональні значення, щоб вказати, що пошук у словнику може не вдатись, коли у ньому не існує даний ключ. У прикладі вище, значення `number % 10` буде гарантовано коректним індексом для словника `digitNames`, тому знак оклику використовується для примусового розгортання значення `String`, що зберігається в опціоналі, котрий повертає індекс.

Рядок, що дістається зі словника `digitNames`, вставляється в _початок_ змінної `output`, таким чином рядкова версія числа будується задом наперед. \(Вираз `number % 10` дає значення `6` для `16`, `8` для `58`, та `0` для `510`\).

Далі, змінна `number` ділиться на `10`. Оскільки це ціле число, воно округлюється вниз після ділення, тому `16` стає `1`, `58` стає `5`, і `510` стає `51`.

Процес повторюється допоки значення `number` не досягне `0`, після чого рядок `output` повертається із замикання, і додається до нового масиву всередині методу `map(_:)`.

Використання синтаксису прикінцевих замикань у прикладі вище акуратно інкапсулює функціональність замикання одразу після функції, що використовує це замикання, без необхідності обгортати все замикання дужками виклику методу `map(_:)`.

## Захоплення значень

Замикання можуть _захоплювати_ константи та змінні із довколишнього контексту. Замикання можуть посилатись на значення цих констант та змінних, змінювати їх, навіть якщо більше не існує початкового контексту, де було оголошено ці константи чи змінні.

У Swift, найпростішою формою замикання, що може захоплювати значення, є вкладена функція, записана в тілі іншої функції. Вкладена функція може захоплювати будь-який з аргументів зовнішньої функції, а також будь-які змінні та константи всередині зовнішньої функції.

Ось приклад функції `makeIncrementer`, котра містить вкладену функцію `incrementer` \(збільшувач\). Вкладена функція `incrementer` захоплює два значення, `runningTotal` та `amount`, із зовнішнього для неї контексту. Після захоплення цих значень, функція `incrementer` повертається функцією `makeIncrementer` як замикання, що при кожному виклику збільшує значення змінної `runningTotal` на `amount`.

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    return incrementer
}
```

Типом, що повертає функція `makeIncrementer` є `() -> Int`. Це означає, що повертається не просте значення, а _функція_. Функція, що повертається, не має параметрів, та повертає значення `Int` при кожному виклику. Детальніше із функціями, що повертають інші функції, можна ознайомитись в підрозділі [Функціональні типи як типи, що повертаються](5_functions.md#Функціональні-типи-як-типи,-що-повертаються).

У функції `makeIncrementer(forIncrement:)` оголошено цілочисельну змінну на ім'я `runningTotal` для зберігання поточного значення лічильника, що повертається функцією `incrementer()`. Цю змінну ініціалізовано значенням `0`.

Функція `makeIncrementer(forIncrement:)` має єдиний параметр типу `Int` з міткою аргументу `forIncrement`, та ім'ям параметра `amount`. Значення аргументу, що передається в якості цього параметру, визначає, на скільки буде збільшуватись значення `runningTotal` при кожному виклику функції, що повертається. Функція `makeIncrementer` визначає вкладену функцію `incrementer`, котра власне й виконує збільшення. Ця функція просто додає `amount` до `runningTotal`, і повертає результат.

Якщо вкладену функцію `incrementer()` розглядати ізольовано, вона може виглядати незвично:

```swift
func incrementer() -> Int {
    runningTotal += amount
    return runningTotal
}
```

Функція `incrementer()` не має жодного параметра, але посилається на значення `runningTotal` та `amount` з поза меж свого тіла. Функція робить це шляхом захоплення _посилань_ на `runningTotal` та `amount` із зовнішньої функції та використання їх всередині власного тіла. Захоплення посилань гарантує, що `runningTotal` та `amount` не зникнуть після того, як завершиться виконання функції `makeIncrementer`, і також гарантує, що змінна `runningTotal` буде доступною при наступному виклику функції `incrementer`.

> **Примітка**
>
> В якості оптимізації, Swift може настравді захоплювати і зберігати _копію_ значення, якщо воно не змінюється в замиканні, і якщо значення не змінюється після створення замикання.
>
> Swift також виконує все управління пам'яттю, що задіяне у звільненні змінних, коли вони більше не потрібні.

Ось приклад функції `makeIncrementer` в дії:

```swift
let incrementByTen = makeIncrementer(forIncrement: 10)
```

У цьому прикладі константі `incrementByTen` присвоєно посилання на функцію-збільшувач, що при кожному виклику додає `10` до змінної `runningTotal`. Продемонструємо дану поведінку, викликавши цю функцію кілька разів:

```swift
incrementByTen()
// поверне значення 10
incrementByTen()
// поверне значення 20
incrementByTen()
// поверне значення 30
```

Якщо створити іншу функцію-збільшувач, вона зберігатиме своє власне посилання на нову, окрему змінну `runningTotal`:

```swift
let incrementBySeven = makeIncrementer(forIncrement: 7)
incrementBySeven()
// поверне значення 7
```

Новий виклик початкового збільшувача \(`incrementByTen`\) продовжить збільшувати його власну змінну `runningTotal`, не впливаючи на значення, захоплене функцією `incrementBySeven`:

```swift
incrementByTen()
// поверне значення 40
```

> **Примітка**
>
> Якщо присвоїти замикання властивості екземпляру класу, і замикання захоплює цей екземпляр через посилання на його членів, буде створено циклічне посилання між замиканням та екземпляром. Більше інформації можна знайти у підрозділі [Сильні циклічні посилання в замиканнях](23_automatic_reference_counting.md#Сильні-циклічні-посилання-в-замиканнях).

## Замикання є типами-посиланнями

У прикладах вище, `incrementBySeven` та `incrementByTen` є константами, але замикання, на які посилаються ці константи, все ж таки можуть збільшувати змінні `runningTotal`, котрі вони захопили. Це тому, що функції та замикання є _типами-посиланнями_.

Щоразу при присвоєнні функції чи замикання змінній чи константі, фактично цій змінній чи константі присвоюється _посилання_ на функцію чи замикання. У прикладі вище, константа `incrementByTen` є константним _посиланням_ на замикання, при цьому саме замикання не є константним.

Це також означає наступне: якщо присвоїти замикання двом різним константам чи змінним, обидві ці константи чи змінні будуть посилатись на одне й теж замикання:

```swift
let alsoIncrementByTen = incrementByTen
alsoIncrementByTen()
// поверне значення 50
```

## Замикання-емігранти

Кажуть, що замикання _емігрують_ з функції, якщо замикання передається до функції як аргумент, але викликається після завершення функції. Під час оголошення функції, що приймає замикання як один з параметрів, для того, щоб це замикання могло емігрувати з функції, потрібно позначати його атрибутом `@escaping`.

Одним зі способів еміграції замикання є збереження його у змінній, котру оголошено за межами функції. Наприклад, багато функцій, що розпочинають асинхронну операцію, приймають своїм аргументом замикання як обробник завершення цієї операції. Функція завершується одразу після того, як вона розпочне операцію, але замикання не буде викликано до завершення цієї операції: таким чином замикання повинно емігрувати, щоб бути викликаним пізніше. Наприклад:

```swift
var completionHandlers: [() -> Void] = []
func someFunctionWithEscapingClosure(completionHandler: @escaping () -> Void) {
    completionHandlers.append(completionHandler)
}
```

Функція `someFunctionWithEscapingClosure(_:)` приймає замикання як аргумент та додає його до масиву, що оголошено за її межами. Якщо не позначити параметр цієї функції атрибутом `@escaping`, відбудеться помилка компіляції.

Позначення замикання атрибутом `@escaping` вимагає, щоб всередині замикання до `self` звертались явно. Наприклад, у коді нижче, замикання, що передається до функції `someFunctionWithEscapingClosure(_:)`, є замиканням-емігрантом, тому всередині його потрібно звертатись до `self` явно. На противагу до цього, замикання, що передається до функції `someFunctionWithNonescapingClosure(_:)` не є замиканням-емігрантом, тому всередині його можна звертатись до `self` неявно.

```swift
func someFunctionWithNonescapingClosure(closure: () -> Void) {
    closure()
}

class SomeClass {
    var x = 10
    func doSomething() {
        someFunctionWithEscapingClosure { self.x = 100 }
        someFunctionWithNonescapingClosure { x = 200 }
    }
}

let instance = SomeClass()
instance.doSomething()
print(instance.x)
// Надрукує "200"

completionHandlers.first?()
print(instance.x)
// Надрукує "100"
```

## Автозамикання

_Автозамиканням_ є замикання, що автоматично створюється для загортання виразу, що передається до функції як аргумент. Автозамикання ніколи не приймають вхідних аргументів, і при виклику повертають значення виразу, котрий вони в собі загортають. Ця синтаксична зручність дозволяє пропускати фігурні дужки довкола параметру функції, записуючи звичайний вираз замість явного замикання.

Зазвичай, доводиться часто _викликати_ функції, що приймають автозамикання як аргумент, але рідко доводиться _реалізовувати_ такий різновид функцій. Наприклад, функція `assert(condition:message:file:line:)` приймає автозамикання як параметри `condition` та `message`; її параметр `condition` обчислюється тільки в зневаджувальних конфігураціях збірки, а її параметр `message` викликається тільки тоді, коли `condition` обчислюється як `false`.

Автозамикання дозволяють відкласти виконання коду, бо код всередині автозамикання не буде виконуватись до запуску самого замикання. Це відкладання може бути корисним для коду, що має побічні ефекти чи є обчислювально дорогим, бо воно дозволяє контролювати момент, коли виконується цей код. Приклад нижче показує, як замикання відкладають виконання коду:

```swift
var customersInLine = ["Віра", "Катя", "Ольга", "Галя", "Сергій"]
print(customersInLine.count)
// Надрукує "5"

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Надрукує "5"

print("Тепер обслуговуємо вас, \(customerProvider())!")
// Надрукує "Тепер обслуговуємо вас, Віра!"
print(customersInLine.count)
// Надрукує "4"
```

Попри те, що перший елемент масиву `customersInLine` видаляється з масиву в коді всередині замикання, елемент масиву не видаляється до власне виклику самого замикання. Якщо замикання ніколи не викликати, вираз всередині замикання ніколи не буди виконуватись, що означає, що ніколи не видалиться елемент з масиву. Слід зазначити, що типом `customerProvider` є не `String`, а `() -> String` – функція, що не має параметрів та повертає рядок.

Цю ж само поведінку відкладеного виконання можна отримати, передавши замикання як аргумент до функції.

```swift
// customersInLine дорівнює ["Катя", "Ольга", "Галя", "Сергій"]
func serve(customer customerProvider: () -> String) {
    print("Тепер обслуговуємо вас, \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) } )
// Надрукує "Тепер обслуговуємо вас, Катя!"
```

Функція `serve(customer:)` у прикладі вище приймає замикання у явному вигляді, котре повертає ім'я покупця з черги. Версія функції `serve(customer:)` у прикладі нижче виконує ту ж саму операцію, але замість явного замикання, вона приймає автозамикання шляхом позначення типу її параметра атрибутом `@autoclosure`. Тепер можна викликати функцію так, неначе вона приймає аргумент типу `String` замість замикання. Оскільки тип параметра позначено атрибутом `@autoclosure`, аргумент буде автоматично перетворено на замикання.

```swift
// customersInLine is ["Ольга", "Галя", "Сергій"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Тепер обслуговуємо вас, \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))
// Надрукує "Тепер обслуговуємо вас, Ольга!"
```

> **Примітка**
>
> Зловживання автозамиканнями може зробити ваш код складним для розуміння. Контекст та ім'я функції повинні давати зрозуміти, що виконання буде відкладено.

Якщо автозамиканню дозволено емігрувати, слід використовувати одночасно обидва атрибути, `@autoclosure` та `@escaping`. Атрибут `@escaping` описано вище в підрозділі [Замикання-емігранти](6_closures.md#Замикання-емігранти).

```swift
// customersInLine is ["Галя", "Сергій"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.remove(at: 0))
collectCustomerProviders(customersInLine.remove(at: 0))

print("Отримано \(customerProviders.count) замикання.")
// Надрукує "Отримано 2 замикання."
for customerProvider in customerProviders {
    print("Тепер обслуговуємо вас, \(customerProvider())!")
}
// Надрукує "Тепер обслуговуємо вас, Галя!"
// Надрукує "Тепер обслуговуємо вас, Сергій!"
```

У коді вище, функція `collectCustomerProviders(_:)`замість того, щоб викликати передане як аргумент `customerProvider` замикання, додає його до масиву `customerProviders`. Масив оголошено поза межами функції; це означає, що замикання в масиві можна викликати після виходу з функції. Тому значення аргументу `customerProvider` повинно мати дозвіл на еміграцію з тіла функції.
