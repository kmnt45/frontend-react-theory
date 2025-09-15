# TypeScript

## 1. Что такое TypeScript?

`TypeScript` — это язык программирования, разработанный Microsoft, который является надмножеством JavaScript. Он добавляет статическую типизацию, поддержку интерфейсов, дженериков и других возможностей. Код на TypeScript компилируется в обычный JavaScript и может выполняться в любом окружении, где работает JS.

Особенности:

- Статическая типизация и проверка типов на этапе компиляции

- Интерфейсы, типы и generics

- Поддержка ООП (классы, наследование, модификаторы доступа)

- Совместимость с современными стандартами ECMAScript

- Возможность документировать систему через типы

Преимущества:

- Более надёжный код за счёт раннего выявления ошибок

- Улучшенный автокомплит и рефакторинг в IDE

- Масштабируемость для больших проектов и команд

- Легко интегрируется в существующие проекты

Недостатки:

- Более крутая кривая обучения для JS-разработчиков

- Иногда усложняет код из-за обилия типов

- Требует настройки (tsconfig, типизация библиотек)

- Может замедлить прототипирование небольших проектов

## 2. Типы в TypeScript

Примитивные типы:

1. `number` – для чисел.

   ```ts
   let age: number = 25;
   ```

2. `string` – для строк.

   ```ts
   let name: string = "Anton";
   ```

3. `boolean` – для логических значений (истина/ложь).

   ```ts
   let isActive: boolean = true;
   ```

4. `null` – специальное значение для "ничего".

   ```ts
   let emptyValue: null = null;
   ```

5. `undefined` – переменная без присвоенного значения.

   ```ts
   let notAssigned: undefined = undefined;
   ```

6. `symbol` – уникальные и неизменяемые идентификаторы.

   ```ts
   let sym: symbol = Symbol();
   ```

7. `bigint` – для работы с большими целыми числами.

   ```ts
   let largeNumber: bigint = 9007199254740991n;
   ```

Объектные типы:

1. `Array` – массивы, могут быть определены через generic.

   ```ts
   let numbers: number[] = [1, 2, 3];
   let strings: Array<string> = ["a", "b", "c"];
   ```

2. `Tuple` – кортежи, фиксированная длина массива с разными типами данных.

   ```ts
   let person: [string, number] = ["Anton", 25];
   ```

3. `Object` – любой объект.

   ```ts
   let user: { name: string, age: number } = { name: "Anton", age: 25 };
   ```

4. `Function` – типизация функций.

   ```ts
   let add: (a: number, b: number) => number = (a, b) => a + b;
   ```

Специальные типы:

1. `any` – для переменных, тип которых заранее неизвестен.

   ```ts
   let randomValue: any = "Hello";
   ```

2. `unknown` – похож на `any`, но требует явной проверки типа.

   ```ts
   let someValue: unknown = "Hello";
   
   if (typeof someValue === "string") {
     console.log(someValue.toUpperCase());
   }
   ```

3. `void` – функция ничего не возвращает.

   ```ts
   function greet(): void {
     console.log("Hello!");
   }
   ```

4. `never` – функция не завершится (`throw` или бесконечный цикл), или значение, которое никогда не встречается (например, в `switch` для exhaustive check).

    ```ts
    function throwError(message: string): never {
        throw new Error(message);
    }
    
    // Пример с switch и exhaustive check
    type Shape = { kind: "circle"; radius: number } | { kind: "square"; size: number };
    
    function area(shape: Shape): number {
        switch (shape.kind) {
            case "circle":
                return Math.PI * shape.radius ** 2;
            case "square":
                return shape.size * shape.size;
            default:
                const _exhaustiveCheck: never = shape; // TS проверяет, что других вариантов нет
                return _exhaustiveCheck;
        }
    }
    ```

5. `Перечисление (enum)` — это специальный тип данных в TypeScript, который представляет набор именованных значений. Оно удобно, когда нужно работать с фиксированными множествами значений, например, статусами задач ("active", "completed", "pending").

```ts
enum Status {
  Active,
  Completed,
  Pending,
}

let taskStatus: Status = Status.Active;
```
| Характеристика     | `enum`                          | `const enum`                                                             |
| ------------------ | ------------------------------- | ------------------------------------------------------------------------ |
| Компиляция         | Преобразуется в объект JS       | Устраняется на этапе компиляции, используется напрямую числовое значение |
| Производительность | Немного медленнее из-за объекта | Быстрее, меньше кода в JS                                                |
| Использование      | Можно использовать динамически  | Только для статических значений                                          |

Пример const enum:

```ts
const enum Status {
  Active,
  Completed,
  Pending,
}

let taskStatus: Status = Status.Completed; // В JS станет просто: let taskStatus = 1;
```

💡 На собеседовании полезно упомянуть: const enum лучше использовать, когда значения фиксированы и не требуется динамическое обращение к объекту, а enum — когда нужны «обратные» ссылки или динамический доступ.

Обратные ссылки (reverse mapping):

```ts
enum Status {
  Active = 1,
  Completed = 2,
}

// По имени → значение
console.log(Status.Active); // 1

// По значению → имя (обратная ссылка)
console.log(Status[2]); // "Completed"
```

Динамический доступ:

```ts
enum Status {
  Active = 1,
  Completed = 2,
}

const key = "Active";
console.log(Status[key]); // 1

```

6. `literal` – литеральные типы для фиксированных значений.

   ```ts
   let direction: "left" | "right" = "left";
   ```

Унион и пересечения:

1. Union (`|`) – объединение нескольких типов.

   ```ts
   let id: string | number;
   ```

2. Intersection (`&`) – пересечение нескольких типов.

   ```ts
   let user: { name: string } & { age: number };
   ```

## 3. Что такое общие типы (generic) в TypeScript?

`Общие типы (generic)` позволяют создавать функции, классы и интерфейсы, которые работают с любыми типами данных, не зная их конкретного типа заранее. Например:

```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

## 4. Что такое предохранители типов?

`Предохранитель типов` — это способ сузить тип переменной внутри определённого блока кода. Он позволяет TypeScript понимать, какой тип у переменной в конкретной ветке, и безопасно использовать специфические свойства и методы.

```ts
function isString(value: any): value is string {
  return typeof value === "string";
}
```

`value is string` — это предикат типа, который сообщает TypeScript: если функция вернула true, value — строка.

```ts
function processValue(value: string | number) {
  if (isString(value)) {
    console.log(`Длина строки: ${value.length}`);
  } else {
    console.log(`Квадрат числа: ${value * value}`);
  }
}

processValue("hello"); // Длина строки: 5
processValue(42);      // Квадрат числа: 1764
```

💡 Идея: предохранители типов помогают работать с union-типами, не боясь ошибок типа, и позволяют TypeScript точно определить тип в блоке кода.

## 5. Что такое декораторы?

`Декораторы` — это специальные функции, которые можно навешивать на классы, методы, свойства или параметры для изменения или расширения их поведения. Они помогают добавлять функциональность, не меняя исходный код напрямую.

Типы декораторов:

- Классов — изменяют или дополняют сам класс.

- Методов — позволяют, например, логировать вызовы или кэшировать результаты.

- Свойств — контролируют работу со свойствами (валидация, преобразование значений).

- Параметров — дают доступ к информации о параметрах метода.

```ts
function log(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Метод ${key} вызван с аргументами:`, args);
    return original.apply(this, args);
  };
}

class User {
  @log
  sayHello(name: string) {
    return `Hello, ${name}!`;
  }
}

new User().sayHello("Anton");
```

Важные моменты

- Декораторы — экспериментальная функция. Для их использования нужно включить в tsconfig.json:
    ```ts
    { "compilerOptions": { "experimentalDecorators": true } }
    ```

- Они активно применяются в Angular, а в React встречаются реже (чаще в библиотеках или DI).

- Хорошо подходят для кросс-срезных задач: логирование, валидация, кеширование, контроль доступа.

## 6. Поддерживает ли TypeScript перегрузку функций?

Да, TypeScript поддерживает перегрузку, но только на уровне типов. В JavaScript остаётся одна реализация функции, а разные сигнатуры нужны для проверки типов и автодополнения.

```ts
function greet(name: string): string;
function greet(age: number): string;
function greet(value: string | number): string {
  if (typeof value === "string") {
    return `Hello, ${value}`;
  } else {
    return `You are ${value} years old`;
  }
}

greet("Anton"); // Hello, Anton
greet(25);      // You are 25 years old
```

Важные моменты

- Объявляется несколько сигнатур, но реализуется всегда одна функция.

- Перегрузка помогает IDE и компилятору корректно подсказывать типы.

- В рантайме работает обычная проверка параметров, так как в JS нет настоящей перегрузки.

## 7. Разница между типом (type) и интерфейсом (interface)?

И `type`, и `interface` позволяют описывать структуру данных. В простых случаях они взаимозаменяемы, но есть различия:

- `type` более универсален (можно объединять и пересекать типы),

- `interface` лучше подходит для описания контрактов объектов и классов, поддерживает декларативное расширение.

| Особенность               | `type`      | `interface`                          |
| ------------------------- |-------------|--------------------------------------|
| Объединение и пересечение | ✅ (`&`, `l`)  | ❌ |
| Наследование              | Через `&`   | Через `extends` |
| Подходит для классов      | ❌           | ✅ |
| Подходит для функций      | Ограниченно | ✅ |
| Декларативное слияние     | ❌           | ✅ (одноимённые интерфейсы объединяются) |

```ts
// type: удобно для объединений
type ID = string | number;

// interface: удобно для контрактов
interface User {
  id: number;
  name: string;
}
interface User {
  email: string; // декларативное слияние
}

const user: User = { id: 1, name: "Anton", email: "a@test.com" };
```

## 8. Какие режимы JSX поддерживает TypeScript?

- React: Для работы с React.
- Preserve: Сохранение JSX как есть, без преобразования.
- React Native: Для разработки приложений с React Native.

## 9. Как TypeScript поддерживает необязательные и дефолтные параметры в функции?

- `Необязательные параметры`: Указываются с помощью `?`, например `function foo(x?: number)`.
- `Дефолтные параметры`: Имеют значение по умолчанию, например `function foo(x: number = 5)`.

## 10. Для чего в TypeScript используется NoImplicitAny?

`NoImplicitAny` — это флаг компилятора TypeScript, который предотвращает автоматическое присвоение типа `any` переменным
и параметрам, если тип не указан явно. Это помогает предотвратить неявные ошибки типизации.

## 11. Какие области видимости доступны в TypeScript?

`Глобальная область видимости:`

Переменные и функции, объявленные вне любых блоков, функций или классов, находятся в глобальной области видимости. Они
доступны везде в коде.
Глобальные переменные могут привести к конфликтам имен, особенно в больших проектах.

`Область видимости функции:`

Переменные, объявленные внутри функции с помощью var, let, или const, доступны только внутри этой функции. Это
называется функциональной областью видимости.
Переменные не видны за пределами функции.

`Блочная область видимости:`

Введена в ES6 с ключевыми словами let и const. Переменные, объявленные внутри блока (например, в конструкции if, for,
while, и т.д.), доступны только внутри этого блока.
В отличие от var, который игнорирует блочную область, let и const обеспечивают блочную область видимости.

`Область видимости класса:`

Переменные и методы, объявленные внутри класса с использованием ключевого слова private, имеют область видимости только
в этом классе.
Свойства и методы могут также быть объявлены как protected, что делает их доступными только в классе и его подклассах.
Свойства и методы с модификатором доступа public доступны везде, где виден объект класса.

`Модульная область видимости:`

В TypeScript (и ES6) модули имеют свою собственную область видимости. Экспортируемые переменные и функции из одного
модуля доступны только в других модулях через import.
Это помогает изолировать код и предотвращает загрязнение глобального пространства имен.

## 12. Что такое .map файл, как и зачем его использовать?

`Файл .map` — это source map, который связывает скомпилированный JavaScript код с исходным TypeScript кодом. Это позволяет отлаживать TypeScript код в браузере, показывая исходный код вместо скомпилированного JavaScript.

## 13. Для чего в TypeScript используют ключевое слово declare?

`declare` позволяет сообщить TypeScript о существовании переменной, функции или модуля, чтобы компилятор знал их типы, но не генерировал код в JS.

Используется, когда:

- Есть глобальные переменные, которые объявлены вне TypeScript (например, из скриптов на JS).

- Нужно работать с внешними библиотеками без типов.

```ts
// Глобальная переменная из другого скрипта
declare const API_KEY: string;

// Функция из внешнего JS-файла
declare function greet(name: string): void;

// Модуль без типов
declare module "some-js-library";
```

💡 Важно: declare не создаёт реализацию, а только сообщает TypeScript о типах.

## 14. Зачем нужны .d.ts файлы?

`.d.ts файлы` - это файлы декларации типов. Они содержат описание типов без реализации. Например, сигнатуры функций без
тела, название и тип глобальных переменных без значений. В основном файлы декларации типов нужны для написания библиотек
или при их использовании.

```JavaScript
declare

class Vector {
    readonly
    x: number;
    readonly
    y: number;
    static readonly
    zero: Vector;

    constructor(x: number, y: number);

    add(vector: Vector): Vector;
}
```

## 15. Как реализовать const в классе?

В TypeScript при объявлении свойств классов `нельзя использовать ключевое слово const`.

Начиная со второй версии TS, можно использовать модификатор `readonly`, позволяющий объявлять свойства класса,
предназначенные только для чтения.

```ts
class Circle {
  readonly radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  area(): number {
    return Math.PI * this.radius ** 2;
  }
}

const c = new Circle(5);
c.radius = 10; // Ошибка: нельзя изменить readonly свойство
```

## 16. Что такое пространства имён? Разница между внутренним (Internal Module) и внешним модулями (External Module)?

`Пространства имен в TypeScript` — это способ организации и группировки связанного кода. Они помогают избежать
столкновения имен и способствуют модульности, инкапсулируя связанный код. Пространства имен могут содержать классы,
интерфейсы, функции, переменные и другие пространства имен.

Определение пространств имен

Чтобы определить пространство имен, используйте ключевое слово namespace, за которым следует имя пространства имен.
Затем можно добавить любой связанный код внутри фигурных скобок.

```JavaScript
namespace MyNamespace {
    export class MyClass {
        constructor(public value: number) {
    }

    displayValue() {
        console.log(`The value is: ${this.value}`);
    }
}
}
```

В этом примере определяем пространство имен MyNamespace и добавляем в него класс MyClass. Учтите, что ключевое слово
export используется, чтобы сделать класс доступным за пределами пространства имен.

Применение пространств имен

Чтобы применить код из пространства имен, можно либо использовать полностью определенное имя, либо импортировать код с
помощью импорта пространства имен.

```JavaScript
// Использование полностью определенного имени
const instance1 = new MyNamespace.MyClass(5);
instance1.displayValue(); // Вывод: Значение - 5

// Использование импорта пространства имен
import MyClass = MyNamespace.MyClass;

const instance2 = new MyClass(10);
instance2.displayValue(); // Вывод: Значение - 10
```

Этот пример демонстрирует два способа использования класса MyClass из пространства имен MyNamespace. В первом случае
используем полностью определенное имя MyNamespace.MyClass. Во втором — применяем оператор импорта пространства имен,
чтобы импортировать класс MyClass и использовать его с более коротким именем.

Вложенные пространства имен

Пространства имен могут быть вложенными для создания иерархии и дальнейшей организации кода.

```JavaScript 
namespace OuterNamespace {
export namespace InnerNamespace {
        export class MyClass {
            constructor(public value: number) {
        }

        displayValue() {
            console.log(`The value is: ${this.value}`);
        }
    }
    }
}

// Использование полностью определенного имени
const instance = new OuterNamespace.InnerNamespace.MyClass(15);
instance.displayValue(); // Вывод: Значение - 15
```

В этом примере определяем вложенное пространство имен InnerNamespace внутри OuterNamespace. Затем определяем класс
MyClass внутри вложенного пространства имен и используем его с полным именем OuterNamespace.InnerNamespace.MyClass.

В TypeScript различают `внутренние (Internal) и внешние (External) модули`. Вот основные различия между ними:

- `Внутренние модули (Internal Modules)`

  - Определение: Внутренние модули, также известные как пространства имен (namespaces), используются для организации
    кода внутри одного файла или группы файлов. Они помогают избежать конфликтов имен.
  - Синтаксис: Внутренние модули определяются с помощью ключевого слова `namespace`.

    ```typescript
    namespace MyNamespace {
        export class MyClass {
            // ...
        }
    }
    ```
  - Использование: Внутренние модули полезны для группировки связанных типов и функций, особенно в больших
    приложениях.


- `Внешние модули (External Modules)`

  - Определение: Внешние модули представляют собой отдельные файлы, которые могут экспортировать и импортировать
    функциональность. Они используют систему модулей ES6 (ES2015) или CommonJS.
  - Синтаксис: Внешние модули используют ключевые слова `import` и `export` для импорта и экспорта функциональности.

    ```typescript
    // файл myModule.ts
    export class MyClass {
        // ...
    }
    
    // другой файл
    import { MyClass } from './myModule';
    ```
  - Использование: Внешние модули позволяют разделять код на файлы, что упрощает его повторное использование и
    тестирование.

Краткое сравнение

- Структура: Внутренние модули — это логическая организация кода внутри одного файла, в то время как внешние
  модули — это физическое разделение кода на разные файлы.
- Импорт/Экспорт: Внутренние модули используют `namespace`, а внешние — `import` и `export`.
- Современные практики: Внешние модули предпочтительнее для новых проектов, так как они лучше соответствуют
  стандартам ES6 и упрощают работу с инструментами сборки.

## 17. **Зачем нужен keyof?**

Оператор keyof возвращает в качестве типа перечисление ключей объекта, по отношению к которому он применяется.

```JavaScript
  type
Point = {x: number; y: number};

type
P = keyof
Point;

// type P = “x” | “y”
```

## 18. Что такое утилитарные типы (Utility Types)?

`Утилитарные типы (Utility Types)` — это встроенные типы, которые помогают создавать новые типы на основе существующих, упрощая работу с типами и обеспечивая более чистый и поддерживаемый код.

Чтобы создать собственный утилитарный тип в TypeScript, вам нужно использовать обобщения(generics) и условные типы.

Что такое `Partial<>`?

`Утилитарный тип Partial<T>` позволяет сделать все свойства типа T необязательными. Можно сказать, что он добавит
отметку ? рядом с каждым полем.

```JavaScript
interface
Todo
{
    title: string;
    description: string;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
    return {...todo, ...fieldsToUpdate};
}
```

---

Что такое `Required<>`?

В отличие от Partial утилитарный тип `Required<T>` принудительно делает все свойства объекта T обязательными.

```javascript
interface
Props
{
    a ? : number;
    b ? : string;
}

const obj: Props = {a: 5};

const obj2: Required<Props> = {a: 5};

// Property 'b' is missing in type '{ a: number; }' but required in type 'Required<Props>'.
```

---

Что такое `Pick<>`?

Утилитарный тип `Pick<Type, Keys>` позволяет создать новый тип, взяв из Type свойства Keys.

```javascript
interface
Todo
{
    title: string;
    description: string;
    completed: boolean;
}

type
TodoPreview = Pick < Todo, "title" | "completed" >;

const todo: TodoPreview = {
    title: "Clean room",
    completed: false,
};
```

---

Что такое `Omit<>`?

Утилитарный тип `Omit<Type, Keys>` является противоположностью утилитарного типа Pick<Type, Keys>. С помощью Omit можно
создать тип на основе имеющегося, исключив перечисленные свойства.

```javascript
interface
Todo
{
    title: string;
    description: string;
    completed: boolean;
    createdAt: number;
}

type
TodoPreview = Omit < Todo, "description" >;

const todo: TodoPreview = {
    title: "Clean room",
    completed: false,
    createdAt: 1615544252770,
};
```

---

Что такое `Exclude<>`?

`Exclude<UnionType, ExcludedMembers>` похож на Omit с тем лишь отличием, что первым параметром принимает UnionType, а не
конкретный тип.

Он создает тип, исключая из UnionType указанные ExcludedMembers.

Обратите внимание на третий пример, это может работать даже так.

```javascript
type
T0 = Exclude < "a" | "b" | "c", "a" >;
// type T0 = "b" | "c"

type
T1 = Exclude < "a" | "b" | "c", "a" | "b" >;
// type T1 = "c"

type
T2 = Exclude < string | number | (() => void), Function >;
// type T2 = string | number
```

---

Что такое `Extract<>`?

`Extract<Type, Union>` создает тип на основе извлеченного Union из Type.

```javascript
type
T0 = Extract < "a" | "b" | "c", "a" | "f" >;
// type T0 = "a"

type
T1 = Extract < string | number | (() => void), Function >;
// type T1 = () => void
```

---

Что такое `NonNullable<>`?

`NonNullable<Type>` просто возвращает переданный тип, лишив его типов null и undefined.

```javascript
type
T0 = NonNullable < string | number | undefined >;
// type T0 = string | number

type
T1 = NonNullable < string[] | null | undefined >;
// type T1 = string[]
```

---

Что такое `Parameters<>`?

`Parameters<Type>` принимает на вход функцию и возвращает тип, описывающий её параметры.

```javascript
declare

function myFunc(arg: { a: number; b: string }): void;

type
Params = Parameters < typeof myFunc >;
//  type Params = [arg: {
//    a: number;
//    b: string;
//  }]
```

---

Что такое `ReturnType<>`?

`ReturnType<Type>` принимает на вход функцию и возвращает тип, описывающий возвращаемое ею значение.

```javascript
declare

function f1(): { a: number; b: string };

type
T1 = ReturnType < typeof f1 >;
//  type T1= {
//    a: number;
//    b: string;
//  }

type
T2 = ReturnType < ()
=>
string >;
// type T2 = string
```