# Наслідування

Клас можу _наслідувати_ методи, властивості та інші характеристики іншого класу. Коли один клас наслідується від іншого класу, клас, який наслідується, називають _класом-нащадком_, а той клас, від якого наслідуються, називають _батьківським класом_. Наслідування є фундаментальною поведінкою, котра відрізняє класи від інших типів у Swift.

Класи у Swift можуть викликати та звертатись до методів, властивостей та індексів, що належать їх батьківським класам, і можуть надавати свої власні версії цих методів, властивостей та індексів, заміщуючи при цьому батьківські версії, таким чином вдосконалюючи чи змінюючи їх поведінку. Swift допомагає бути певним щодо коректності заміщень шляхом перевірки, що заміщене оголошення має відповідне йому оголошення в батьківському класі.

Класи можуть також додавати спостерігачі за успадкованими властивостями, щоб отримувати сповіщення про зміни у властивостях. Спостерігачі за властивостями можна додавати до будь-яких успадкованих властивостей, незважаючи на те, чи були ці властивості визначені як властивості, що зберігаються, чи властивості, що обчислюються.

## Визначення базового класу

Будь-який клас, що не наслідується від іншого класу, називають _базовим класом_.

> **Примітка**
>
> Класи у Swift не наслідуються від універсального базового класу. Класи, що ви визначаєте без вказування батьківського класу, автоматично стають базовими класами, на яких ви можете будувати інші класи.

У прикладі нижче визначається базовий клас на ім'я `Vehicle`, котрий моделює транспортний засіб. Цей базовий клас визначає властивість, що зберігається, на ім'я `currentSpeed`, зі значенням за замовчанням `0.0` \(компілятор визначить тип цієї властивості як `Double`\). Значення властивості `currentSpeed` використовується у властивості тільки для читання, що обчислюється, на ім'я `description` типу `String`, котра створює опис транспортного засобу.

Базовий клас `Vehicle` також визначає метод на ім'я `makeNoise` \(пошуміти\). Даний метод фактично не робить нічого в екземплярах базового класу `Vehicle`, але він буде уточнений у нащадках класу `Vehicle` пізніше:

```swift
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "подорожує зі швидкістю \(currentSpeed) км/год"
    }
    func makeNoise() {
        // не робимо нічого – довільний транспортний засіб не обо'язково шумить
    }
}
```

Новий екземпляр класу `Vehicle` створюється за допомогою _синтаксису ініціалізації_, що записується як `Ім'яТипу`, після якого йдуть порожні круглі дужки:

```swift
let someVehicle = Vehicle()
```

Після створення нового екземпляру класу `Vehicle`, можна отримати значення його властивості `description`, щоб надрукувати зрозумілий для людини опис його поточної швидкості:

```swift
print("Vehicle: \(someVehicle.description)")
// Vehicle: подорожує зі швидкістю 0.0 км/год
```

Клас `Vehicle` визначає спільні характеристики для довільного транспортного засобу, однак сам по собі цей клас є не дуже корисним. Щоб зробити його кориснішим, слід вдосконалити його, описавши конкретніші типи транспортних засобів.

## Наслідування

Наслідування є актом побудови нового класу на базі існуючого класу. Клас-нащадок успадковує характеристики його батьківського класу, котрі потім можна удосконалювати. Можна також додавати нові характеристики класу-нащадку.

Щоб вказати, що клас є класом-нащадком батьківського класу, в оголошенні класу-нащадку після його імені слід записати двокрапку та ім'я батьківського класу:

```swift
class SomeSubclass: SomeSuperclass {
    // тут йдуть оголошення класу нащадку
}
```

В наступному прикладі оголошено клас нащадок на ім'я `Bicycle`, що наслідує базовий клас `Vehicle`:

```swift
class Bicycle: Vehicle {
    var hasBasket = false    // чи має велочипед кошик
}
```

Новий клас `Bicycle` автоматично отримує всі характеристики класу `Vehicle`, включаючи властивості `currentSpeed`, `description` та його метод `makeNoise()`.

Окрім успадкованих характеристик, клас `Bicycle` визначає нову властивість, що зберігається, на ім'я `hasBasket`, зі значенням за замовчанням `false` \(її тип визначається як `Bool`\).

За замовчанням, нові екземпляри класу `Bicycle` не матимуть кошика. Можна змінити властивість `hasBasket` на `true` для конкретного екземпляра `Bicycle` після створення цього екземпляру:

```swift
let bicycle = Bicycle()
bicycle.hasBasket = true
```

Можна також змінити успадковану властивість `currentSpeed` екземпляру `Bicycle`, і запитати його успадковану властивість `description`:

```swift
bicycle.currentSpeed = 15.0
print("Велосипед: \(bicycle.description)")
// Велосипед: подорожує зі швидкістю 15.0 км/год
```

Класи-нащадки можуть також мати своїх нащадків. У наступному прикладі створено клас-нащадок класу `Bicycle` для двомісного велосипеда, котрий називають “тандем”:

```swift
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0    // поточна кількість пасажирів
}
```

Клас `Tandem` наслідує всі властивості та методи класу `Bicycle`, котрий у свою чергу наслідує всі властивості та методи класу `Vehicle`. Клас-нащадок `Tandem` також додає нову властивість, що зберігається, на ім'я `currentNumberOfPassengers`, зі значенням за замовчанням `0`.

Якщо створити екземпляр класу `Tandem`, можна працювати з усіма його новими та успадкованими властивостями, та запитувати його властивість для читання `description`, успадковану від класу `Vehicle`:

```swift
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Тандем: \(tandem.description)")
// Тандем: подорожує зі швидкістю 22.0 км/год
```

## Заміщення

Клас-нащадок може мати свою власну реалізацію методу екземпляру, методу типу, властивості екземпляру, властивості типу, чи індексу, замість успадкованої від батьківського класу. Таку поведінку називають _заміщенням_.

Щоб замістити характеристику, котра в іншому випадку була б успадкована, слід додати префікс у вигляді ключового слова `override` до оголошення заміщеної характеристики. Це ключове слово дозволяє уточнити, що намір був саме у заміщенні методу батьківського класу, а не випадковим додаванням оголошення, що співпадає з батьківським. Заміщення через випадкову помилку може спричинити неочікувану поведінку, тому будь-які спроби заміщення методу без ключового слова `override` трактуються компілятором як помилка в коді.

Ключове слово `override` також підказує компілятору Swift, що слід перевірити, що батьківський клас \(або один з його батьків\) дійсно містить оголошення, котре заміщується у класі-нащадку. Ця перевірка дозволяє бути певним щодо коректності заміщення.

### Доступ до методів, властивостей та індексів батьківського класу

Коли клас-нащадок заміщує метод, властивість ти індекс, іноді буває корисним скористуватись існуючою реалізацією з базового класу в реалізації цього заміщення. Наприклад, можна вдосконалити поведінку існуючої реалізації, чи зберегти змінене значення в існуючій успадкованій змінній.

Коли це доречно, можна звертатись до версії методу, властивості чи індексу з базового класу, використовуючи префікс `super`:

* Заміщений метод на ім'я `someMethod()` може викликати версію методу `someMethod()` з батьківського класу шляхом виклику `super.someMethod()` у своїй реалізації.
* Заміщена властивість на ім'я `someProperty` може звертатись до версії властивості `someProperty` з батьківського класу як `super.someProperty` в реалізації свого сетера чи гетера.
* Заміщений індекс `someIndex` може викликати версію індексу з батьківського класу шляхом виклику `super[someIndex]` у своїй реалізації.

### Заміщення методів

Можна заміщувати успадковані методи екземпляру чи типу, щоб надати удосконалену чи альтернативну реалізацію цього методу в класі-нащадку.

У наступному прикладі визначено новий клас-нащадок класу `Vehicle` на ім'я `Train` \(потяг\), що заміщує успадкований від класу `Vehicle` метод `makeNoise()`:

```swift
class Train: Vehicle {
    override func makeNoise() {
        print("Ту-Ту!")
    }
}
```

Якщо створити новий екземпляр класу `Train`, та викликати метод `makeNoise()`, можна переконатись, що буде викликано версію цього методу із класу-нащадку `Train`:

```swift
let train = Train()
train.makeNoise()
// Надрукує "Ту-Ту!"
```

### Заміщення властивостей

Можна замістити успадковану властивість екземпляру чи типу, або для того, щоб надати свої власні сетер та гетер для цієї властивості, або для того, щоб додати спостерігачі за цією властивістю, і отримувати сповіщення про зміни значення властивості з батьківського класу.

### Заміщення гетеру та сетеру властивості

Заміщення успадкованої властивості може надавати їй новий гетер \(та сетер, якщо це потрібно\), незалежно від того, чи зберігається успадкована властивість, чи обчислюється у батьківському класі. Природа того, чи зберігається успадкована властивість, чи обчислюється, залишається невідомою для класу-нащадку: відомими є лише ім'я та тип батьківської властивості. Слід завжди зазначати ім'я та тип властивості, що заміщується, щоб компілятор мав змогу перевірити, що заміщення відповідає певній властивості батьківського класу з тим же іменем та типом.

Можна замістити успадковану властивість тільки для читання властивість для читання й запису, шляхом надання гетера та сетера у заміщеній властивості у класі-нащадку. Однак, не можна замістити успадковану властивість для читання й запису властивістю тільки для читання.

> **Примітка**
>
> Якщо у заміщенні властивості присутній сеттер, це заміщення обов'язково повинно містити також і геттер. Якщо вам не мотрібно змінювати успадкований геттер в заміщенні, ви можете просто передати успадковане значення, викликавши `super.someProperty` в геттері, де `someProperty` – ім'я властивості, що успадковується.

У наступному прикладі визначено новий клас на ім'я `Car` \(автомобіль\), що наслідується від класу `Vehicle`. Клас `Car` визначає нову властивість, що зберігається, на ім'я `gear` \(передача\), зі значенням за замовчанням `1`. У класі `Car` також заміщується успадкована з класу `Vehicle` властивість `description`, для того, щоб надати власний опис автомобіля, що містить також поточну передачу:

```swift
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " на передачі \(gear)"
    }
}
```

Заміщення властивості `description` починається викликом `super.description`, котрий повертає значення властивості `description` батьківського класу `Vehicle`. Версія властивості `description` класу `Car` після цього прикріплює додатковий текст до кінця опису, надаючи інформацію про поточну передачу.

Якщо створити екземпляр класу `Car`, присвоїти значення його властивостям `gear` та `currentSpeed`, можна переконатись, що властивість `description` повертає вдосконалену версію властивості `description` з класу `Car`:

```swift
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Автомобіль: \(car.description)")
// Автомобіль: подорожує зі швидкістю 25.0 км/год на передачі 3
```

### Заміщення спостерігачів за властивостями

Заміщення властивості можна використовувати для додавання спостерігачів за успадкованою властивістю. Це дозволяє отримувати сповіщення про зміни значення успадкованої властивості, незважаючи на те, як цю властивість реалізовано у батьківському класі. Детальніше зі спостерігачами за властивостями можна ознайомитись у підрозділі [Спостерігачі за властивостями](9_properties.md#Спостерігачі-за-властивостями).

> **Примітка**
>
> Не можна додати спостерігачі за успадкованою константною властивістю, що зберігається, та за успадкованою властивістю тільки для читання, що обчислюється. Значення цих властивостей не можна змінити, тому некоректно додавати реалізації `willSet` чи `didSet` у реалізації заміщення.
>
> Слід помітити, що в заміщенні властивості не можна одночасно мати сеттер та спостерігач за властивістю. Якщо потрібно відслідковувати зміни у значенні властивості, і заміщення властивості вже має сеттер, можна просто робити це прямо в сеттері.

У наступному прикладі визначено новий клас на ім'я `AutomaticCar`, котрий наслідується від класу `Car`. Клас `AutomaticCar` представляє автомобіль з автоматичною коробкою передач, котрий автоматично обирає потрібну передачу в залежності від поточної швидкості:

```swift
class AutomaticCar: Car {
    override var currentSpeed: Double {
        didSet {
            gear = Int(currentSpeed / 20.0) + 1
        }
    }
}
```

При будь-якому присвоєнні нового значення властивості `currentSpeed` екземпляру класу `AutomaticCar`, буде викликано спостерігач `didSet`, котрий задасть властивості `gear` значення, що відповідає поточній швидкості. Конкретно, спостерігач за властивістю обере передачу, що дорівнюватиме новому значенню `currentSpeed`, поділеному на `20`, округленому до найближчого цілого, плюс `1`. Швидкість `65.0` дасть передачу `4`:

```swift
let automatic = AutomaticCar()
automatic.currentSpeed = 65.0
print("AutomaticCar: \(automatic.description)")
// AutomaticCar: подорожує зі швидкістю 65.0 км/год на передачі 4
```

## Запобігання заміщенню

Щоб заборонити заміщення методу, властивості чи індексу, слід зробити його _фінальним_. Це робиться за допомогою ключового слова `final`, що передує оголошенню методу, властивості чи індексу \(наприклад, `final var`, `final func`, `final class func`, та `final subscript`\).

Будь-яка спроба замістити фінальний метод, властивість, чи індекс у класі-нащадку призведе до помилки часу компіляції. Методи, властивості та індекси, що додаються до класу у розширенні, також можуть бути фінальними в оголошенні розширення.

Можна зробити фінальним весь клас, додавши модифікатор `final` перед ключовим словом `class` в оголошенні класу \(`final class`\). Будь-яка спроба успадкувати фінальний клас призведе до помилки часу компіляції.
