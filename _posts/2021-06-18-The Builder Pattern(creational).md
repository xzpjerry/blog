---
layout: post
published: true
tags:
- design_pattern
- creational
---

![Lots of subclasses create another problem](https://refactoring.guru/images/patterns/diagrams/builder/problem1.png)

![Applying the Builder pattern](https://refactoring.guru/images/patterns/diagrams/builder/solution1.png)

![Structure of the Builder design pattern](https://refactoring.guru/images/patterns/diagrams/builder/structure-indexed.png)

- creating complex objects with **different configurations** with the **same** code

- Optional Director class: where defines the building steps

```python
class House:
    def __init__(self):
        self.walls = None
        self.doors = None
        self.windows = None

class HouseDirector:
    def __init__(self, builder):
        self.builder = builder
        self.result = House()
    def changeBuilder(self, builder):
        self.builder = builder
    def make(self, *args):
        self.builder.buildWalls(*args)
        self.builder.buildDoors(*args)
        self.builder.buildWindows(*args)
        return self.result
        
class FancyHouseBuilder:
    def __init__(self, house):
        self.result = house
    def buildWalls(self, *args):
        pass
    def buildDoors(self, *args):
        pass
    def buildWindows(self, *args):
        pass

class MondernHouseBuilder:
    def __init__(self, house):
        self.result = house
    def buildWalls(self, *args):
        pass
    def buildDoors(self, *args):
        pass
    def buildWindows(self, *args):
        pass
```





