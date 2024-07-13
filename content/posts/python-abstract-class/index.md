---
title: "[Python] 추상 클래스"
date: 2024-07-13T00:07:11+09:00
draft: false
authors: ["JoonShik"]
description: "추상 클래스는 하나 이상의 추상 메소드를 포함하는 클래스입니다. Python에서는 어떻게 추상 클래스를 생성할까요?"
categories: ["python"]
tags: ["AbstractClass", "package", "ABC"]
featuredImagePreview: "thumbnail.png"
---
<!--more-->
## 추상 메소드
추상 메소드(abstract method)는 선언만 존재하고 구현은 존재하지 않는 메소드를 말합니다.  
이는 하위 클래스에서 반드시 구현해야합니다.  

추상 메소드는 하위 클래스에서 반드시 구현해야 하므로 해당 메소드가 `강제적으로 구현`됨을 보장할 수 있습니다.  
또한 여러 클래스가 같은 이름의 메소드를 통해 동작하는 `다형성`을 제공하고 유연한 시스템을 제공합니다.  

## 추상 클래스
추상 클래스(abstract class)는 하나 이상의 추상 메소드를 포함하는 클래스를 말합니다.  

추상 클래스는 일반 클래스와 다르게 직접적으로 객체를 생성할 수 없고 반드시 상속받아서 하위 클래스에서 추상 메소드를 구현해야만 객체를 생성할 수 있습니다.

## Python의 추상 클래스
Python은 `ABC(Abstract Base Class)` 모듈을 통하여 추상 클래스 기능을 제공합니다.  

`ABC` 모듈은 클래스의 메타 클래스를 `ABCMeta`로 설정하여 추상 클래스를 선언할 수 있습니다.  

```python
from abc import ABC

class AbstractClass(ABC):
    pass
```

혹은 메타 클래스 키워드를 직접 전달하며 추상 클래스를 선언할 수도 있습니다.

```python
from abc import ABCMeta

class AbstractClass(metaclass=ABCMeta):
    pass
```

### 추상 메서드 선언
`ABC` 모듈의 `abstractmethod` 프러퍼티를 통해 추상 메서드를 선언할 수 있습니다.

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    @abstractmethod
    def move(self):
        pass

class Dog(AbstractAnimal):
    def move(self):
        print("Move Dog!")

dog = Dog()
dog.move()
```

다음과 같이 `classmethod`와 `staticmethod` 모두 마찬가지로 프러퍼티를 중첩하여 선언할 수 있습니다.

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    @classmethod
    @abstractmethod
    def abstract_class_method(cls):
        pass
    
    @staticmethod
    @abstractmethod
    def abstract_static_method():
        pass
    

class Dog(AbstractAnimal):
    @classmethod
    def abstract_class_method(cls):
        pass
    
    @staticmethod
    def abstract_static_method():
        pass
```

{{< admonition type=info title="Info" open=true >}}
`docstring`을 작성한다면 `pass`를 작성하지 않아도 동작이 없음(`no-op`)을 표현할 수 있습니다.
{{< /admonition >}}

### 추상 프로퍼티
`ABC` 모듈의 `abstractmethod`와 `property` 데코레이터를 중첩하는 방식을 통해 추상 프러퍼티를 선언할 수 있습니다.  
이때, `property` 데코레이터가 `abstractmethod` 위에 존재해야 합니다.  

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    @property
    @abstractmethod
    def name(self):
        pass
    

class Dog(AbstractAnimal):
    @property
    def name(self):
        return "Dog"
```

### 추상 변수
만약 프러퍼티가 아닌 변수 형태로 선언해야 한다면 `ABC` 모듈에서 직접적으로 제공하는 방법은 없지만 몇 가지 방법이 존재합니다.  

다음은 클래스에서 타입 어노테이션을 통해 변수를 초기화하지 않고 선언하는 것을 사용하는 방법입니다.

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    name: str
    
    @abstractmethod
    def move(self):
        pass


class Dog(AbstractAnimal):
    def move(self):
        pass
```

하지만 해당 방법은 인스턴스 선언 시에 하위 클래스의 클래스 변수가 선언되어 있지 않아도 이를 파악할 수 없고 사용 시점에만 파악할 수 있다는 문제가 있습니다.  

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    name: str
    
    @abstractmethod
    def move(self):
        pass


class Dog(AbstractAnimal):
    def move(self):
        pass

dog = Dog()
dog.name
# AttributeError: 'Dog' object has no attribute 'name'
```

위의 코드를 보면 dog 객체의 생성 시점에는 에러가 발생하지 않고 name을 호출하는 시점에 에러가 발생하는 것을 확인할 수 있습니다.  

다른 방법은 추상 프러퍼티로 선언한 후 클래스 변수로 대체하는 방법이 존재합니다.  

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    @property
    @abstractmethod
    def name(self):
        pass


class Dog(AbstractAnimal):
    name = "Dog"
```

### 추상 클래스 객체화
추상 클래스는 직접적으로 객체화하는 것이 불가능합니다.  

```python
from abc import ABC, abstractmethod

class AbstractAnimal(ABC):
    @abstractmethod
    def move(self):
        pass

animal = AbstractAnimal()
# TypeError: Can't instantiate abstract class AbstractAnimal with abstract method move
```
