# Безпека доступу до пам'яті

За замовчанням, Swift запобігає небезпечній поведінці вашого коду. Наприклад, Swift гарантує, що змінні є проініціалізованими перед їх використанням, що до пам'яті не звертаються після її деалокації, а індекси масивів перевіряються на вихід за межі масиву.

Swift також слідкує за тим, щоб кілька звернень до однієї зони пам'яті не конфліктували, вимагаючи, щоб код, який змінює регіон у пам'яті, мав ексклюзивний доступ до цієї пам'яті. Оскільки Swift керує пам'яттю автоматично, більшість часу вам взагалі не потрібно думати про доступ до пам'яті. Однак, важливо розуміти де можуть виникнути потенційні конфлікти, щоб мати можливість уникнути написання коду, що містить конфліктний доступ до пам'яті. Якщо ваш код містить конфлікти, ви отримаєте або помилку компіляції, або помилку часу виконання.

## Розуміння конфліктного доступу до пам'яті

Доступ до пам'яті відбувається тоді, коли ваш код робить речі на кшталт присвоєння значення змінній, або передачі аргументу до функції. Наприклад, наступний код містить як читання, так і запис пам'яті:

```swift
// Доступ до пам'яті (запис), де зберігається змінна one.
var one = 1

// Доступ до пам'яті (читання), де зберігається змінна one.
print("Ми – номер \(one)!")
```

Конфліктний доступ до пам'яті може статись, коли різні частини вашого коду намагаються доступитись до однієї й тієї ж зони пам'яті одночасно. Одночасні звернення до регіону пам'яті можуть призвести до непередбачуваної та непостійної поведінки. У Swift є способи змінити значення, що тривають кілька рядків коду, що робить можливими спроби доступу до значення під час його модифікації.

Аналогічну проблему можна побачити, розмірковуючи про оновлення бюджету, що записаний на папері. Оновлення бюджету є процесом із двох кроків: спершу слід дописати назви та ціни нових покупок, а потім - змінити суму вартостей усіх покупок, щоб відобразити зміни у списку. До і після оновлення, можна прочитати будь-яку інформацію з бюджету та отримати правильну відповідь, як показано на малюнку нижче.

![](../.gitbook/assets/memory_shopping_2x.png)

Під час додавання покупок до бюджету, він знаходиться у тимчасовому некоректному стані, оскільки суму бюджету ще не було оновлено для відображення вартості доданих покупок. Зчитування суми під час процесу додавання покупок дає некоректну інформацію.

Даний приклад демонструє виклик, з яким можна зустрітись при усуненні конфліктного доступу до пам'яті: іноді існує декілька способів усунути конфлікт, що приводять до кількох відповідей, і не завжди очевидно, яка з цих відповідей є правильною. У даному прикладі, в залежності від того, що нам потрібно: початкова сума чи оновлена, правильною відповіддю буде або ₴35, або ₴9305. Перед тим, як усунути конфліктний доступ, слід визначатись із тим, що взагалі потрібно робити.

> **Примітка**
>
> Якщо вам доводилось писати [рівночасний](https://uk.wikipedia.org/wiki/Рівночасні_обчислення) чи [багатопотоковий](https://uk.wikipedia.org/wiki/Багатонитковість) код, конфліктний доступ має бути знайомою проблемою. Однак, конфліктний доступ, що обговорюється в даному розділі, може статись і в одному потоці, без залучення рівночасного чи багатопотокового коду.
>
> Якщо у вас є конфліктний доступ в одному потоці, Swift гарантує, що ви отримаєте або помилку компіляції, або помилку часу виконання. Щодо багатопоточного коду, для виявлення конфліктного доступу між потоками слід використовувати [Thread Sanitizer](https://developer.apple.com/documentation/code_diagnostics/thread_sanitizer).

## Характеристики доступу до пам'яті

Існує три характеристики доступу до пам'яті з точки зору контексту конфліктного доступу: характер доступу \(на читання чи на запис\), тривалість доступу, та регіон у пам'яті. Конкретніше, конфлікт відбувається, якщо є два доступи, котрі задовольняють наступні умови:

* Хоча б один із доступів є записом.
* Обидва доступи стосуються одного й того ж регіону в пам'яті.
* Їх тривалості перетинаються. 

Різниця між доступом на читання та доступом на запис пам'яті зазвичай очевидна: запис змінює регіон у пам'яті, а читання – ні. Регіон у пам'яті означає те, до чого йде доступ: наприклад, змінна, константа чи властивість. Тривалість доступу до пам'яті буває як миттєва, так і довготривала.

Доступ є миттєвим, якщо після його початку і перед його закінченням неможливо запустити інший код. За своєю природою, два миттєвих доступи не можуть статись одночасно. Більшість доступів до пам'яті є миттєвими. Наприклад, всі доступи на читання й на запис у наступному фрагменті коду є миттєвими:

```swift
func oneMore(than number: Int) -> Int {
    return number + 1
}

var myNumber = 1
myNumber = oneMore(than: myNumber)
print(myNumber)
// Надрукує "2"
```

Однак, є кілька способів звертатись до пам'яті довготривалим чином, що перетинається із виконанням іншого коду. Різниця між миттєвим і довготривалим доступом полягає у тому, що після початку довготривалого доступу і перед його закінченням може виконуватись інший код; таку ситуацію називають перетином. Довготривалий доступ може перетинатись з іншими довготривалими чи миттєвими доступами.

Перетин доступів з'являється в першу чергу в коді, що використовує двонаправлені параметри у функціях та методах, або в мутуючих методах структури. Конкретні типи коду на Swift, що використовують довготривалий доступ, обговорюються у підрозділах нижче.

## Конфліктний доступ у двонаправлених параметрах

Функція має довготривалий доступ на запис до усіх її двонаправлених параметрів. Доступ на запис до двонаправлених параметрів розпочинається після того, як усі не двонаправлені параметри були обчислені та триває під час усього виклику функції. Якщо є кілька двонаправлених параметрів, доступ на запис починається в тому ж порядку, в якому оголошені двонаправлені параметри.

Одним наслідком із довготривалого доступу є неможливість доступу до оригінальної змінної, що передається двонаправленим чином, навіть якщо правила контролю доступу це дозволяють: будь-який доступ до оригіналу створює конфлікт. Наприклад:

```swift
var stepSize = 1

func increment(_ number: inout Int) {
    number += stepSize
}

increment(&stepSize)
// Помилка: конфліктний доступ до змінної stepSize
```

У коді вище, `stepSize` є глобальною змінною, і вона зазвичай є доступною всередині функції `increment(_:)`. Однак, доступ на читання `stepSize` перетинається із доступом на запис `number`. Як показано на ілюстрації нижче, змінні `number` та `stepSize` посилаються на одну й ту ж локацію в пам'яті. Доступи на читання і на запис однієї й тієї ж локації в пам'яті перетинаються, призводячи до конфлікту.

![](../.gitbook/assets/memory_increment_2x.png)

Одним зі способів вирішити конфлікт є створення явної копії `stepSize`:

```swift
// Створення явної копії.
var copyOfStepSize = stepSize
increment(&copyOfStepSize)

// Оновлення оригіналу.
stepSize = copyOfStepSize
// stepSize тепер дорівнює 2
```

При створенні копії `stepSize` перед викликом `increment(_:)`, зрозуміло, що значення `copyOfStepSize` збільшується на поточне значення `stepSize`. Доступ на читання закінчується до початку доступу на запис, тому конфлікту немає.

Іншим прикладом конфлікту у довготривалому доступі на запис двонаправлених параметрів є передача єдиної змінної як аргументу кількох двонаправлених параметрів однієї й тієї ж функції. Наприклад:

```swift
func balance(_ x: inout Int, _ y: inout Int) {
    let sum = x + y
    x = sum / 2
    y = sum - x
}
var playerOneScore = 42
var playerTwoScore = 30
balance(&playerOneScore, &playerTwoScore)  // OK
balance(&playerOneScore, &playerOneScore)
// Помилка: конфліктний доступ до playerOneScore
```

Функція `balance(_:_:)` вище змінює два параметри, розділяючи їх суму в порівну між ними. Виклик цієї функції зі змінними `playerOneScore` та `playerTwoScore` в якості аргументів не призводить до конфлікту: є два доступи на запис, що перетинаються в часі, але вони доступаються до різних регіонів у пам'яті. Одначе, якщо передати змінну `playerOneScore` як значення обох параметрів – це викличе конфлікт, тому що буде спроба доступу на запис одного й того ж регіону в пам'яті в один і той же час.

> **Примітка**
>
> Оскільки оператори є функціями, вони також мають довготривалий доступ до своїх двонаправлених параметрів. Наприклад, якби функція `balance(_:_:)` була би фунцією-оператором на ім'я `<^>`, запис `playerOneScore <^> playerOneScore` теж виливався би у той же само конфлікт, як і `balance(&playerOneScore, &playerOneScore)`.

## Конфліктний доступ до self у методах

Мутуючий метод структури під час виклику має доступ на запис `self`. Наприклад, розглянемо гру, в якій кожен гравець має кількість здоров'я \(`health`\), котра зменшується щоразу, коли гравець отримує пошкодження; а також кількість енергії \(`energy`\), котра зменшується при використанні особливих здібностей.

```swift
struct Player {
    var name: String
    var health: Int
    var energy: Int

    static let maxHealth = 10
    mutating func restoreHealth() {
        health = Player.maxHealth
    }
}
```

У методі `restoreHealth()` вище, доступ на запис до `self` розпочинається на початку методу, і триває до виходу з методу. У цьому випадку, всередині методу `restoreHealth()` немає іншого коду, котрий має конфліктний доступ до властивостей екземпляру `Player`. Метод `shareHealth(with:)` нижче приймає інший екземпляр `Player` як двонаправлений параметр, створюючи можливість доступу, що перетинається з доступом до `self`.

```swift
extension Player {
    mutating func shareHealth(with teammate: inout Player) {
        balance(&teammate.health, &health)
    }
}

var oscar = Player(name: "Oscar", health: 10, energy: 10)
var maria = Player(name: "Maria", health: 5, energy: 10)
oscar.shareHealth(with: &maria)  // OK
```

У прикладі вище, виклик методу `shareHealth(with:)` для гравця на ім'я Oscar, щоб поділитись здоров'ям із гравцем на ім'я Maria, не призводить до конфлікту. Під час виклику методу є доступ на запис екземпляру `oscar`, оскільки `oscar` є значенням `self` в мутуючому методі, і є доступ на запис екземпляру `maria` у цей же проміжок часу, оскільки екземпляр `maria` було передано як двонаправлений параметр. Як показано на ілюстрації нижче, йде доступ до різних регіонів пам'яті. Хоч два доступи на запис і перетинаються у часі, вони не конфліктують.

![](../.gitbook/assets/memory_share_health_maria_2x.png)

Однак, якщо передати `oscar` як аргумент методу `shareHealth(with:)`, отримаємо конфлікт:

```swift
oscar.shareHealth(with: &oscar)
// Помилка: конфліктний доступ до oscar
```

Мутуючий метод під час виконання потребує доступу на запис `self`, а двонаправлений параметр потребує доступу на запис `teammate` у той самий час. Всередині методу, `self` та `teammate` посилаються на один і той же регіон у пам'яті, як показано на ілюстрації нижче. Два доступи на запис одного й того ж регіону пам'яті, що перетинаються у часі, призводять до конфлікту.

![](../.gitbook/assets/memory_share_health_oscar_2x.png)

## Конфліктний доступ до властивостей

Такі типи як структури, кортежі, та перечислення складаються з окремих складових значень, таких як властивості структури чи елементи кортежу. Оскільки вони є типами-значеннями, зміна будь-якого зі складових значень змінює все значення; це означає, що читання чи запис однієї властивості потребує читання чи запису усього значення. Наприклад, перетин доступів на запис елементів кортежу призводять до конфлікту:

```swift
var playerInformation = (health: 10, energy: 20)
balance(&playerInformation.health, &playerInformation.energy)
// Помилка: конфліктний доступ до властивостей playerInformation
```

У прикладі вище, виклик функції `balance(_:_:)` на елементах кортежу призводить до конфлікту, адже там присутній перетин доступів на запис кортежу `playerInformation`. Як `playerInformation.health`, так і `playerInformation.energy` передаються як двонаправлені параметри, з чого слідує, що функція `balance(_:_:)` потребує доступу на їх запис під час виклику функції. В обох випадках, доступ на запис елементу кортежу потребує доступу на запис усього кортежу. Це означає, що є два доступи на запис `playerInformation`, які перетинаються у часі, спричиняючи конфлікт.

Код нижче показує, як та ж само помилка з'являється у випадку одночасних доступів на запис властивостей екземпляру структури, котра зберігається у глобальній змінній.

```swift
var holly = Player(name: "Holly", health: 10, energy: 10)
balance(&holly.health, &holly.energy)  // Помилка
```

На практиці, більшість доступів до властивостей структури можуть безпечно перетинатись. Наприклад, якщо змінну `holly` змінити на локальну змінну замість глобальної, компілятор зможе довести, що одночасний доступ до властивостей структури, що зберігаються, є безпечним:

```swift
func someFunction() {
    var oscar = Player(name: "Oscar", health: 10, energy: 10)
    balance(&oscar.health, &oscar.energy)  // OK
}
```

У прикладі вище, властивості `health` та `energy` екземпляру `oscar` передаються як двонаправлені параметри до методу `balance(_:_:)`. Компілятор може довести, що безпека доступу до пам'яті зберігається, оскільки дві властивості, що зберігаються, жодним чином не перетинаються.

Обмеження одночасного доступу до властивостей структури не є завжди необхідним для збереження безпеки доступу до пам'яті. Безпека пам'яті є бажаною гарантією, але взаємовиключний доступ є більш строгою вимогою, ніж безпека пам'яті. Це означає, що деякий код може зберігати безпеку доступу до пам'яті, хоч і порушувати взаємовиключний доступ до пам'яті. Swift дозволяє цей безпечний з точки зору пам'яті код, якщо компілятор може довести, що одночасний доступ до пам'яті все ще є безпечним. Конкретно, він може довести, що одночасний доступ до властивостей структури є безпечним, якщо виконуються наступні умови:

* Відбувається доступ лише до властивостей, що зберігаються, не до властивостей, що обчислюються, чи властивостей типу. 
* Структура є значенням локальної змінної, не глобальної змінної.
* Структура або не захоплюється жодним замиканням, або захоплюється лише неемігруючими замиканнями. 

Якщо компілятор не може довести безпечність доступу, він не дозволяє цей доступ.
