---
layout: post
published: true
tags: design_pattern structual
---

![The structure of the app before integration with the analytics library](https://refactoring.guru/images/patterns/diagrams/adapter/problem-en.png)

![Adapter’s solution](https://refactoring.guru/images/patterns/diagrams/adapter/solution-en.png)

- Introducing a intermediate layer between two incompatiable parts.
- The adapter intereacts with both side.

```python
class Computer:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return 'the {} computer'.format(self.name)

    def execute(self):
        """ call by client code """
        return 'execute a program'


class Synthesizer:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return 'the {} synthesizer'.format(self.name)

    def play(self):
        return 'is playing an electroinc song'


class Human:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return 'the {} human'.format(self.name)

    def speak(self):
        return 'says hello'

class ClientInterface:
    def execute(self):
        pass

class Adapter(ClientInterface):
    def __init__(self, obj, adapted_method):
        """ 不使用继承，使用__dict__属性实现适配器模式 """
        self.obj = obj
        self._execute = adapted_method

    def __str__(self):
        return str(self.obj)
      
    def execute(self):
        return self._execute()

# 适配器使用示例
def main():
    objs = [Computer('Asus')]
    synth = Synthesizer('moog')
    objs.append(Adapter(synth, synth.play))
    human = Human('Wnn')
    objs.append(Adapter(human, human.speak))

    for o in objs:
        # 用统一的execute适配不同对象的方法，这样在无需修改源对象的情况下就实现了不同对象方法的适配
        print('{} {}'.format(str(o), o.execute()))


if __name__ == "__main__":
    main()
```

