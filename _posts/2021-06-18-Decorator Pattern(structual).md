--------
layout: post
published: true
tags: design_pattern structual
--------

- Dynamically adding behaviors to exist objects
- Aggregation(Containing old objects)

![The solution with the Decorator pattern](https://refactoring.guru/images/patterns/diagrams/decorator/solution2.png)





```python
def addSMS(cls):

    class AddSMS(cls):

        def send(self, message):
            super().send(message)
            print("SMS sent")

    return AddSMS

def addFackbook(cls):

    class AddFackbook(cls):

        def send(self, message):
            super().send(message)
            print("Fackbook sent")

    return AddFackbook

@addSMS
@addFackbook
class Notifier:
    def send(self, message):
        print("Email sent")

Notifier().send("123")
```







