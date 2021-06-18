---
layout: post
published: true
tags:
- design_pattern
- creational
---

![Class diagram example The method createButton on the GUIFactory interface returns objects of type Button. What implementation of Button is returned depends on which implementation of GUIFactory is handling the method call.](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/Abstract_factory.svg/1024px-Abstract_factory.svg.png)

- provide an interface for creating a **family** of **dependent** products

```python
from abc import abstractmethod

# the client only need to know
# how to handle abstract classess
class AbstractLetter:
  	pass
class AbstractResume:
  	pass
class AbstractDocumentCreator: # Abstract Factory
    @abstractmethod
    def createLetter() -> AbstractLetter:
      	pass
    @abstractmethod
    def createResume() -> AbstractResume:
      	pass

class FancyLetter(AbstractLetter):
  	pass
class FancyResume(AbstractResume):
  	pass
class FancyDocumentCreator(AbstractDocumentCreator): # Concrete Factory
  	def createLetter() -> FancyLetter: # a factory: where concrete objects are created
      	pass
    def createResume() -> FancyResume: # a factory: where concrete objects are created
      	pass

class ModernLetter(AbstractLetter):
  	pass
class ModernResume(AbstractResume):
  	pass
class ModernDocumentCreator(AbstractDocumentCreator):
  	def createLetter() -> ModernLetter: # a factory: where concrete objects are created
      	pass
    def createResume() -> ModernResume: # a factory: where concrete objects are created
      	pass
```



## Pros

- Changing the concrete implementation would not affect the code using it. (e.g. updating the button without modifying the GUI main code)

## Cons

- readability
