## Порождающие (Creational)
Порождающие паттерны проектирования описывают, как создавать объекты. Обычно объект создается путем вызова констуктора, но иногда требуется большая гибкость именно для этого порождающие паттерны и полезны.
Предназначен для случаев, когда требуется создать сложный объект, состоящий из других объектов, причем все составляющие объекты принадлежат одному "семейству". Предоставляет интерфейс для создания семейств взаимосвязанных или взаимозависимых объектов, не специфицируя их конкретных классов.
### Абстрактная фабрика (Abstract factory)
Предназначен для случаев, когда требуется создать сложный объект, состоящий из других объектов, причем все составляющие объекты принадлежат одному "семейству". Предоставляет интерфейс для создания семейств взаимосвязанных или взаимозависимых объектов, не специфицируя их конкретных классов.

Например, в системе с GUI может быть абстрактная фабрика виджетов, которой наследуют три конкретных фабрики: MacWidgetFactory, XfceWidgetFactory, WindowsWidgetFactory, каждая из которых предоставляет методы для создания одних и тех же объектов (make_button(), make_spinbox() и т.д), стилизованных, однако, как принято на конкретной платформе. Это дает возможность создать обобщенную функцию create_dialog(), которая принимает экземпляр фабрики в качестве аргумента и создает диалоговое окно, выглядещее как в OS X, Xfce или Windows - в зависимости от того какую фабрику мы передали.

Классы абстрактной фабрики часто реализуются фабричными методами, но могут быть реализованы и с помощью паттерна прототип.
```python
import random


class PetShop:

    """A pet shop"""

    def __init__(self, animal_factory=None):
        """pet_factory is our abstract factory.  We can set it at will."""

        self.pet_factory = animal_factory

    def show_pet(self):
        """Creates and shows a pet using the abstract factory"""

        pet = self.pet_factory()
        print("We have a lovely {}".format(pet))
        print("It says {}".format(pet.speak()))


class Dog:
    def speak(self):
        return "woof"

    def __str__(self):
        return "Dog"


class Cat:
    def speak(self):
        return "meow"

    def __str__(self):
        return "Cat"


# Additional factories:

# Create a random animal
def random_animal():
    """Let's be dynamic!"""
    return random.choice([Dog, Cat])()


# Show pets with various factories
if __name__ == "__main__":

    # A Shop that sells only cats
    cat_shop = PetShop(Cat)
    cat_shop.show_pet()
    print("")

    # A shop that sells random animals
    shop = PetShop(random_animal)
    for i in range(3):
        shop.show_pet()
        print("=" * 20)

# OUTPUT #
# We have a lovely Cat
# It says meow
#
# We have a lovely Dog
# It says woof
# ====================
# We have a lovely Cat
# It says meow
# ====================
# We have a lovely Cat
# It says meow
# ====================
```
### Построитель (Builder)
Построитель аналогичен паттерну Абстрактная фабрика в том смысле, что оба предназначены для создания сложных объектов, составленных из других объектов. Но отличается тем, что не только предоставляет методы для построения сложного объекта, но и хранит внутри себя его полное представление.

Этот паттерн допускает такую же композиционную структуру как и Абстрактная фабрика (то есть сложные объекты, составленные из нескольких более простых) но особенно удобен в ситуациях, когда представление составного объета должно быть отделено от алгоритмов композиции

Отделяет конструирование сложного объекта от его представления, так что в результате одного и того же процесса конструирования могут получаться разные представления. От абстрактной фабрики отличается тем, что делает акцент на пошаговом конструировании объекта. Строитель возвращает объект на последнем шаге, тогда как абстрактная фабрика возвращает объект немедленно. Строитель часто используется для создания паттерна компоновщик.
```python
"""
*What is this pattern about?
It decouples the creation of a complex object and its representation,
so that the same process can be reused to build objects from the same
family.
This is useful when you must separate the specification of an object
from its actual representation (generally for abstraction).
*What does this example do?
The first example achieves this by using an abstract base
class for a building, where the initializer (__init__ method) specifies the
steps needed, and the concrete subclasses implement these steps.
In other programming languages, a more complex arrangement is sometimes
necessary. In particular, you cannot have polymorphic behaviour in a constructor in C++ -
see https://stackoverflow.com/questions/1453131/how-can-i-get-polymorphic-behavior-in-a-c-constructor
- which means this Python technique will not work. The polymorphism
required has to be provided by an external, already constructed
instance of a different class.
In general, in Python this won't be necessary, but a second example showing
this kind of arrangement is also included.
*Where is the pattern used practically?
*References:
https://sourcemaking.com/design_patterns/builder
*TL;DR
Decouples the creation of a complex object and its representation.
"""


# Abstract Building
class Building:
    def __init__(self):
        self.build_floor()
        self.build_size()

    def build_floor(self):
        raise NotImplementedError

    def build_size(self):
        raise NotImplementedError

    def __repr__(self):
        return 'Floor: {0.floor} | Size: {0.size}'.format(self)


# Concrete Buildings
class House(Building):
    def build_floor(self):
        self.floor = 'One'

    def build_size(self):
        self.size = 'Big'


class Flat(Building):
    def build_floor(self):
        self.floor = 'More than One'

    def build_size(self):
        self.size = 'Small'


# In some very complex cases, it might be desirable to pull out the building
# logic into another function (or a method on another class), rather than being
# in the base class '__init__'. (This leaves you in the strange situation where
# a concrete class does not have a useful constructor)


class ComplexBuilding:
    def __repr__(self):
        return 'Floor: {0.floor} | Size: {0.size}'.format(self)


class ComplexHouse(ComplexBuilding):
    def build_floor(self):
        self.floor = 'One'

    def build_size(self):
        self.size = 'Big and fancy'


def construct_building(cls):
    building = cls()
    building.build_floor()
    building.build_size()
    return building


def main():
    """
    >>> house = House()
    >>> house
    Floor: One | Size: Big
    >>> flat = Flat()
    >>> flat
    Floor: More than One | Size: Small
    # Using an external constructor function:
    >>> complex_house = construct_building(ComplexHouse)
    >>> complex_house
    Floor: One | Size: Big and fancy
    """


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```
### Фабричный метод (Factory method)
Паттерн Фабричный метод применяется, когда мы хотим, чтобы подклассы выбирали, какой класс инстанциировать, когда запрашивается объект. Это полезно само по себе, но можно пойти дальше и использовать в случае, когда класс заранее неизвестен (например, зависит от информации, прочитанной из файла или введенных пользователем данных).

Абстрактная фабрика часто реализуется с помощью фабричных методов. Фабричные методы часто вызываются внутри шаблонных методов.

```python
"""*What is this pattern about?
A Factory is an object for creating other objects.
*What does this example do?
The code shows a way to localize words in two languages: English and
Greek. "get_localizer" is the factory function that constructs a
localizer depending on the language chosen. The localizer object will
be an instance from a different class according to the language
localized. However, the main code does not have to worry about which
localizer will be instantiated, since the method "localize" will be called
in the same way independently of the language.
*Where can the pattern be used practically?
The Factory Method can be seen in the popular web framework Django:
http://django.wikispaces.asu.edu/*NEW*+Django+Design+Patterns For
example, in a contact form of a web page, the subject and the message
fields are created using the same form factory (CharField()), even
though they have different implementations according to their
purposes.
*References:
http://ginstrom.com/scribbles/2007/10/08/design-patterns-python-style/
*TL;DR
Creates objects without having to specify the exact class.
"""


class GreekLocalizer:
    """A simple localizer a la gettext"""

    def __init__(self):
        self.translations = {"dog": "σκύλος", "cat": "γάτα"}

    def localize(self, msg):
        """We'll punt if we don't have a translation"""
        return self.translations.get(msg, msg)


class EnglishLocalizer:
    """Simply echoes the message"""

    def localize(self, msg):
        return msg


def get_localizer(language="English"):
    """Factory"""
    localizers = {
        "English": EnglishLocalizer,
        "Greek": GreekLocalizer,
    }
    return localizers[language]()


def main():
    """
    # Create our localizers
    >>> e, g = get_localizer(language="English"), get_localizer(language="Greek")
    # Localize some text
    >>> for msg in "dog parrot cat bear".split():
    ...     print(e.localize(msg), g.localize(msg))
    dog σκύλος
    parrot parrot
    cat γάτα
    bear bear
    """


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```
### Прототип (Prototype)
Паттерн Прототип применяется для создания нового объекта путем клонирования исходного с последующей модификацией клона.

Способы создания нового объекта в Python:
```python
class Point:
    __slots__ = ('x', 'y',)

    def __init__(self, x, y):
        self.x = x
        self.y = y

# При таком классическом определении класса Point создать новую точку можно семью способами

def make_object(cls, *args, **kwargs):
    return cls(*args, **kwargs)

point1 = Point(1, 2)
point2 = eval('{}({}, {})'.format('Point', 2, 4)) # Опасно
point3 = getattr(sys.modules[__name__], 'Point')(3, 6)
point4 = globals()['Point'](4, 8)
point5 = make_object(Point, 5, 10)
point6 = copy.deepcopy(point5)
point6.x = 6
point6.y = 12
point7 = point1.__class__(7, 14)
```
При создании point6 используется классический подход на основе прототипа: сначала мы клонируем существующий объект, а затем инициализируем и конфигурируем его. Для создания point7 мы берем объект класса точки point1 и передаем ему другие аргументы.

На примере point6 мы видим, чтов Python уже встроена поддержка прототипов в виде функции copy.deepcopy(). Однако пример point7 показывает, что в Python есть средства и поулчше: вместо того чтобы клонировать существующий объект и модифицировать клон, Python предоставляет доступ к объекту класса имеющегося объекта и таким образом мы можем создать новый объект непосредственно, что гораздо эффективнее клонирования.

Прототип - паттерн, порождающий объекты. Задает виды создаваемых объектов с помощью экземпляра-прототипа и создает новые объекты путем копирования этого прототипа.
```python
"""
*What is this pattern about?
This patterns aims to reduce the number of classes required by an
application. Instead of relying on subclasses it creates objects by
copying a prototypical instance at run-time.
This is useful as it makes it easier to derive new kinds of objects,
when instances of the class have only a few different combinations of
state, and when instantiation is expensive.
*What does this example do?
When the number of prototypes in an application can vary, it can be
useful to keep a Dispatcher (aka, Registry or Manager). This allows
clients to query the Dispatcher for a prototype before cloning a new
instance.
Below provides an example of such Dispatcher, which contains three
copies of the prototype: 'default', 'objecta' and 'objectb'.
*TL;DR
Creates new object instances by cloning prototype.
"""


class Prototype:

    value = 'default'

    def clone(self, **attrs):
        """Clone a prototype and update inner attributes dictionary"""
        # Python in Practice, Mark Summerfield
        obj = self.__class__()
        obj.__dict__.update(attrs)
        return obj


class PrototypeDispatcher:
    def __init__(self):
        self._objects = {}

    def get_objects(self):
        """Get all objects"""
        return self._objects

    def register_object(self, name, obj):
        """Register an object"""
        self._objects[name] = obj

    def unregister_object(self, name):
        """Unregister an object"""
        del self._objects[name]


def main():
    """
    >>> dispatcher = PrototypeDispatcher()
    >>> prototype = Prototype()
    >>> d = prototype.clone()
    >>> a = prototype.clone(value='a-value', category='a')
    >>> b = prototype.clone(value='b-value', is_checked=True)
    >>> dispatcher.register_object('objecta', a)
    >>> dispatcher.register_object('objectb', b)
    >>> dispatcher.register_object('default', d)
    >>> [{n: p.value} for n, p in dispatcher.get_objects().items()]
    [{'objecta': 'a-value'}, {'objectb': 'b-value'}, {'default': 'default'}]
    """


if __name__ == '__main__':
    import doctest
    doctest.testmod()
```

### Одиночка (Singleton)
Паттерн Одиночка (Синглтон) применяется, когда необходим класс, у которого должен быть единственный экземпляр во всей программе.

С помощью паттерна одиночка могут быть реализованы многие паттерны (абстрактная фабрика, строитель, прототип).

В сборнике рецептов приводится простой класс Singleton, которому любой класс может унаследовать, чтобы стать одиночкой.
```python
"""
Yet one singleton realization on Python without metaclass. Singleton may has __init__ method which will call only when first object create.
http://code.activestate.com/recipes/577208-singletonsubclass-with-once-initialization/
"""


class Singleton(object):

    def __new__(cls,*dt,**mp):
        if not hasattr(cls,'_inst'):
            cls._inst = super(Singleton, cls).__new__(cls,dt,mp)
        else:
            def init_pass(self,*dt,**mp):pass
            cls.__init__ = init_pass

        return cls._inst

if __name__ == '__main__':


    class A(Singleton):

        def __init__(self):
            """Super constructor
                There is we can open file or create connection to the database
            """
            print "A init"


    a1 = A()
    a2 = A()
```
```python
И класс Borg, который даст тот же результат совсем другим способом.
"""
*What is this pattern about?
The Borg pattern (also known as the Monostate pattern) is a way to
implement singleton behavior, but instead of having only one instance
of a class, there are multiple instances that share the same state. In
other words, the focus is on sharing state instead of sharing instance
identity.
*What does this example do?
To understand the implementation of this pattern in Python, it is
important to know that, in Python, instance attributes are stored in a
attribute dictionary called __dict__. Usually, each instance will have
its own dictionary, but the Borg pattern modifies this so that all
instances have the same dictionary.
In this example, the __shared_state attribute will be the dictionary
shared between all instances, and this is ensured by assigining
__shared_state to the __dict__ variable when initializing a new
instance (i.e., in the __init__ method). Other attributes are usually
added to the instance's attribute dictionary, but, since the attribute
dictionary itself is shared (which is __shared_state), all other
attributes will also be shared.
*Where is the pattern used practically?
Sharing state is useful in applications like managing database connections:
https://github.com/onetwopunch/pythonDbTemplate/blob/master/database.py
*References:
https://fkromer.github.io/python-pattern-references/design/#singleton
*TL;DR
Provides singleton-like behavior sharing state between instances.
"""


class Borg:
    __shared_state = {}

    def __init__(self):
        self.__dict__ = self.__shared_state
        self.state = 'Init'

    def __str__(self):
        return self.state


class YourBorg(Borg):
    pass


def main():
    """
    >>> rm1 = Borg()
    >>> rm2 = Borg()
    >>> rm1.state = 'Idle'
    >>> rm2.state = 'Running'
    >>> print('rm1: {0}'.format(rm1))
    rm1: Running
    >>> print('rm2: {0}'.format(rm2))
    rm2: Running
    # When the `state` attribute is modified from instance `rm2`,
    # the value of `state` in instance `rm1` also changes
    >>> rm2.state = 'Zombie'
    >>> print('rm1: {0}'.format(rm1))
    rm1: Zombie
    >>> print('rm2: {0}'.format(rm2))
    rm2: Zombie
    # Even though `rm1` and `rm2` share attributes, the instances are not the same
    >>> rm1 is rm2
    False
    # Shared state is also modified from a subclass instance `rm3`
    >>> rm3 = YourBorg()
    >>> print('rm1: {0}'.format(rm1))
    rm1: Init
    >>> print('rm2: {0}'.format(rm2))
    rm2: Init
    >>> print('rm3: {0}'.format(rm3))
    rm3: Init
    """


if __name__ == "__main__":
    import doctest
    doctest.testmod()
```
Однако самый простой способ получить функциональность одиночки в Python - создать модуль с глобальным состоянием, которое хранится в закрытых переменных, и предоставить открытые функции для доступа к нему. Например, для получения курсов валют нам понадобится функция, которая будет возвращать словарь (ключ - название валюты, значение - обменный курс). Возможно, эту функцию понадобится вызывать несколько раз, но в большинстве случев извлекать откуда-то курсы нужно единожды. Реализовать это поможет паттерн Одиночка.
```python
#!/usr/bin/env python3
# Copyright © 2012-13 Qtrac Ltd. All rights reserved.
# This program or module is free software: you can redistribute it
# and/or modify it under the terms of the GNU General Public License as
# published by the Free Software Foundation, either version 3 of the
# License, or (at your option) any later version. It is provided for
# educational purposes and is distributed in the hope that it will be
# useful, but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
# General Public License for more details.

import re
import urllib.request


_URL = "http://www.bankofcanada.ca/stats/assets/csv/fx-seven-day.csv"


def get(refresh=False):
    if refresh:
        get.rates = {}
    if get.rates:
        return get.rates
    with urllib.request.urlopen(_URL) as file:
        for line in file:
            line = line.rstrip().decode("utf-8")
            if not line or line.startswith(("#", "Date")):
                continue
            name, currency, *rest = re.split(r"/s*,/s*", line)
            key = "{} ({})".format(name, currency)
            try:
                get.rates[key] = float(rest[-1])
            except ValueError as err:
                print("error {}: {}".format(err, line))
    return get.rates
get.rates = {}


if __name__ == "__main__":
    import sys
    if sys.stdout.isatty():
        print(get())
    else:
        print("Loaded OK")
```
Здесь мы создаем словарь rates в виде атрибута функции Rates.get() - это наше закрытое значение. Когда открытая функция get() вызывается в первый раз (а также при вызове с параметром refresh=True), мы загружаем список курсов; в противном случае просто возвращаем последние загруженные курсы.
### Порождающие паттерны. Итог
Все порождающие паттерны проектирования реализуются на Python тривиально. Паттерн Одиночка можно реализовать непосредственно с помощью модуля, а паттерн Прототип вообще ни к чему (хотя его можно реализовать с помощью модуля copy), так как Python дает динамический доступ к объектам классов. Из порождающих паттернов наиболее полезны Фабрика и Построитель, и реализовать их можно несколькими способами.

