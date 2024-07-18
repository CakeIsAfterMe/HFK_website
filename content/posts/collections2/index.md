---
title: "Дополнительные типы коллекций в Python"
date: 2024-07-17
draft: false
description: ""
tags: ["python", "datatypes", "modules", "collections"]
series: ["Python"]
series_order: 15
---

{{< lead >}}
к
{{< /lead >}}

## <center>Модуль collections</center>

Python содержит встроенный модуль `collections`, который содержит специализированные типы коллекций, альтернативных традиционным `list`, `tuple`, `dict`:
* `namedtuple`
* `defaultdict`
* `OrderedDict`
* `Counter`
* `ChainMap`
* `deque`

Начнём с разбора именованных кортежей.

---
## <center>Именованные кортежи</center>
Для использования: `from collectionis import namedtuple`

Именованные кортежи (тип `namedtuple`) — это подтип обычных кортежей в Python. У них те же функции, что и у обычных, но их значения можно получать как с помощью индекса (например, `[0]`), так и с помощью имени через точку (например, `.name`). Их основным назначением является улучшение читаемости кода.

Опишем точку на плоскости, имеющую две координаты `x` и `y` с помощью именованного кортежа:
```py
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])     # создаём подкласс кортежа Point (именованный кортеж)

point = Point(3, 7)                         # создаём экземпляр Point

print(point)
print(point.x, point.y)
print(point[0], point[1])
print(type(point))
```

```
# Вывод:
Point(x=3, y=7)
3 7
3 7
<class '__main__.Point'>
```

Как и обычный, именованный кортеж может содержать изменяемые значения, но стоит помнить, что такие кортежи не будут хэшируемы:
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'children'])

sveta = Person('Sveta Ivanova', ['Larisa', 'Keksik'])
print(sveta)

sveta.children.append('Romka')
print(sveta)
```

```
# Вывод:
Person(name='Sveta Ivanova', children=['Larisa', 'Keksik'])
Person(name='Sveta Ivanova', children=['Larisa', 'Keksik', 'Romka'])
```

{{< alert icon="fire" cardColor="#e63946" iconColor="#1d3557" textColor="#f1faee" >}}
Очевидно, что подобный код `sveta.children = ['Larisa', 'Keksik', 'Romka']` привёл бы к ошибке
{{< /alert >}}

Создавать кортежи можно также с помощью именованных аргументов:
```py
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
point1 = Point(2, 4)                     # позиционные аргументы
point2 = Point(y=10, x=3)                # именованные аргументы

print(point1)
print(point2)
```

```
# Вывод:
Point(x=2, y=4)
Point(x=3, y=10)
```

При работе с именованными кортежами можно пользоваться срезами (результатом будет обычный кортеж)

### Функция `namedtuple()`
Эта фабричная функция (понятие из ООП) позволяет создавать подклассы кортежа с именованными полями (классы именованных кортежей).

Сигнатура: `namedtuple(typename, field_names, *, rename=False, defaults=None, module=None)`. Разберёмся с каждым параметров по-отдельности.

#### `typename` и `field_names`
Параметр `typename` отвечает за имя создаваемого класса `namedtuple` (который и возвращает функция `namedtuple()`), а параметр `fieldnames` за название полей, которые мы будем использовать, чтобы получить доступ к значениям определённого экземпляра именованного кортежа. В качестве параметра `field_names` можно использовать:
* Список.
```py
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])  # передаем список
point =  Point(2, 4)
print(point)                             # выводит Point(x=2, y=4)
```

* Кортеж.
```py
from collections import namedtuple

Point = namedtuple('Point', ('x', 'y'))  # передаем кортеж
point =  Point(2, 4)
print(point)                             # выводит Point(x=2, y=4)
```

* Словарь. В этом случае для полей именованного кортежа используются ключи словаря, поэтому в качестве значений можно указать, все что угодно.
```py
from collections import namedtuple

Point = namedtuple('Point', {'x': 0, 'y': 69})  # передаем словарь
point =  Point(2, 4)
print(point)                                    # выводит Point(x=2, y=4)
```

* Строка. При создании именованного кортежа с помощью строки мы указываем поля либо через символ пробела, либо разделяя их символом `,`.
```py
from collections import namedtuple

Point = namedtuple('Point', 'x y')  # передаем строку (можно 'x,y')
point =  Point(2, 4)
print(point)                        # выводит Point(x=2, y=4)
```

* Множество. Можно создать именованный кортеж с помощью множества, но делать это не рекомендуется, так как множество — неупорядоченный набор данных, поэтому поля могут перемешаться. 
```py
from collections import namedtuple

Point = namedtuple('Point', {'x', 'y'})  # в качестве второго параметра передаем множество
point =  Point(2, 4)
print(point)                             # выводит Point(x=2, y=4) или Point(y=2, x=4)
```

{{< alert "circle-info" >}}
В качестве параметра `field_names` можно передавать любой итерируемый объект, например, результат вызова функций `map()` и `filter()`
{{< /alert >}}

В качестве названия полей для именованных кортежей мы можем использовать любое корректное название имени переменной, за исключением:
* имён, начинающихся с символа `_`;
* ключевых слов языка Python (`if`, `with`, `else`, `class`, ...).

#### `rename`
При `rename=True` названия полей, переданных в `field_names`, которые содержат ключевые слова Python, переименовываются в соответствии с их порядковыми номерами (начиная с нуля), перед которыми ставится символ `_`. Посмотрим на пример:
```py
from collections import namedtuple

headers = ('name', 'surname', 'age', 'class', 'with', 'color', 'name', 'class', 'if')

Student = namedtuple('Student', headers, rename=True)

stud = Student('Иван', 'Бережной', 19, 2, 'me', 'green', 'Cake', '2A', 'else')
print(stud)

# Вывод: Student(name='Иван', surname='Бережной', age=28, _3=11, _4='sister', color='green', _6='Tim', _7='11A', _8='else')
```

#### `defaults`
Параметр `defaults` (работает в Python 3.7+) используется для того, чтобы установить значения по умолчанию для полей именованного кортежа. Можно указать значение по умолчанию только для некоторых полей, при этом `defaults` присваивает значения по умолчанию с хвоста.
```py
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'], defaults=(0, 0))
point = Point()      # используем значения по умолчанию

print(point)

# Вывод: Point(x=0, y=0)
```

#### `module`
Посмотрим на вывод следующего кода:
```py
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])
point = Point(1, 2)
print(type(point))

# Вывод: <class '__main__.Point'>
```

Если мы укажем допустимое имя модуля для этого аргумента, тогда атрибуту `.__ module__` результирующего именованного кортежа будет присвоено это значение:
```py
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'], module='custom')
point = Point(1, 2)
print(point.__module__)

# Вывод: custom
```

{{< alert "circle-info" >}}
Параметр `module` был добавлен в Python 3.6 для того, чтобы появилась возможность сериализовать/десериализовать именованные кортежи с помощью модуля `pickle` в разных реализациях Python (IronPython, Jython и т.д.)
{{< /alert >}}

---
### Распаковка именованного кортежа
Именованный кортеж распаковывается также, как и обычный:
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person('Иван', 19, 179)

print(*ivan)

# Вывод: Иван 19 179
```

---
### Атрибуты `_fields` и `_field_defaults`
Именованные кортежи имеют два дополнительных атрибута: `_fields` и `_field_defaults`. Первый содержит кортеж строк, в котором перечислены имена полей. Второй атрибут содержит словарь, который сопоставляет имена полей с соответствующими значениями по умолчанию, если таковые имеются.
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person('Иван', 19, 179)

print(ivan)
print(ivan._fields)
print(Person._fields)
```

```
# Вывод:
Person(name='Иван', age=19, height=179)
('name', 'age', 'height')
('name', 'age', 'height')
```

Как видно из примера выше, можно обращаться к атрибуту `_fields` как через переменную (`ivan`), так и через сам тип именованного кортежа (`Person`).

С помощью атрибута `_fields` можно создавать новые именованные кортежи на основании уже существующих. В следующем примере создаётся новый именованный кортеж с именем `ExtendedPerson`, который расширяет старый `Person` новым полем `weight`:
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])

ExtendedPerson = namedtuple('ExtendedPerson', [*Person._fields, 'weight'])  # распаковка полей старого кортежа

ivan = ExtendedPerson('Иван', 19, 179, 63)

print(timur)
print(ExtendedPerson._fields)
```

```
# Вывод:
ExtendedPerson(name='Иван', age=19, height=179, weight=63)
('name', 'age', 'height', 'weight')
```

Мы также можем использовать атрибут `_fields` для перебора полей и их значений с помощью встроенной функции `zip()`:
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person('Иван', 19, 179)

for field, value in zip(Person._fields, ivan):
    print(field, '->', value)
```

```
# Вывод:
name -> Иван
age -> 19
height -> 179
```

С помощью атрибута `_field_defaults` мы можем выяснить, какие поля именованного кортежа имеют значения по умолчанию:
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height', 'country'], defaults=['Russia'])

ivan = Person('Иван', 19, 179)

print(ivan)
print(ivan._field_defaults)
print(Person._field_defaults)
```

```
# Вывод:
Person(name='Иван', age=19, height=179, country='Russia')
{'country': 'Russia'}
{'country': 'Russia'}
```

{{< alert "circle-info" >}}
Если именованный кортеж не предоставляет значений по умолчанию, тогда атрибут `_field_defaults` содержит пустой словарь.
{{< /alert >}}

---
### Методы `_make()`, `_replace()`, `_asdict()`
* `_make()` — метод класса (не экземпляра), работающий как альтернативный конструктор класса и возвращающий новый экземпляр именованного кортежа `typename`.
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person._make(['Ivan', 19, 179])

print(ivan)

# Вывод: Person(name='Ivan', age=19, height=179)
```

* `_asdict()` преобразует именованные кортежи в словари, в которых имена полей используются в качестве ключей. Ключи результирующего словаря находятся в том же порядке, что и поля в исходном именованном кортеже.
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person._make(['Иван', 19, 179])

print(ivan._asdict())

# Вывод: {'name': 'Иван', 'age': 19, 'height': 179}
```

* `_replace()` создаёт новый именованный кортеж на основе уже существующего с заменой некоторых значений. Потребность в данном методе вызвана тем, что именованные кортежи являются неизменяемыми.
```py
from collections import namedtuple

Person = namedtuple('Person', ['name', 'age', 'height', 'country'])

ivan1 = Person('Иван', 19, 179, 'Russia')
ivan2 = ivan1._replace(age=20, country='Germany')

print(ivan1)
print(ivan2)
```

```
# Вывод:
Person(name='Иван', age=19, height=179, country='Russia')
Person(name='Иван', age=20, height=179, country='Germany')
```

---
### Схожесть со словарями
Функционал именованных кортежей можно полностью заменить функционалом словарей, тогда зачем вообще нужен этот `namedtuple`? Если коротко, то он более быстрый и занимает меньше места в памяти. Сравним эти показатели с помощью нехитрых программ.

Начнём с потребления памяти:
```py
from collections import namedtuple
from pympler import asizeof

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person('Иван', 19, 179)
ivan_dct = {'name': 'Иван', 'age': 19, 'height': 179}

print(f'Именованный кортеж: {asizeof.asizeof(ivan)} байт')
print(f'Словарь: {asizeof.asizeof(ivan_dct)} байт')
```

```
# Вывод:
Именованный кортеж: 112 байт
Словарь: 304 байт
```

Теперь посмотрим на производительность:
```py
from collections import namedtuple
from time import perf_counter

def average_time(structure, test_func):
    time_measurements = []
    for _ in range(1_000_000):
        start = perf_counter()
        test_func(structure)
        end = perf_counter()
        time_measurements.append(end - start)
    return sum(time_measurements) / len(time_measurements) * int(10**9)

def time_dict(dictionary):
    'name' in dictionary
    'missing_key' in dictionary
    28 in dictionary.values()
    'missing_value' in dictionary.values()
    dictionary['age']

def time_namedtuple(named_tuple):
    'name' in named_tuple._fields
    'missing_field' in named_tuple._fields
    28 in named_tuple
    'missing_value' in named_tuple
    named_tuple.age

Person = namedtuple('Person', ['name', 'age', 'height'])

ivan = Person('Иван', 19, 179)
ivan_dct = {'name': 'Иван', 'age': 19, 'height': 179}

print(f'Именованный кортеж: {average_time(ivan, time_namedtuple)} наносекунд')
print(f'Словарь: {average_time(ivan_dct , time_dict)} наносекунд')
```

```
# Вывод:
Именованный кортеж: 382 наносекунд
Словарь: 508 наносекунд
```



















---

**Основной источник:** https://stepik.org/course/82541

Дополнительные источники:
* [Real Python](https://realpython.com/python-namedtuple/)
* [Python Docs](https://docs.python.org/3/library/collections.html)