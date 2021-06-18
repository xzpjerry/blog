# The Facade Pattern: An Interface to Subsystems

![Facade Design Pattern Sequence Diagram](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3d/Facade_Design_Pattern_Sequence_Diagram_UML.svg/350px-Facade_Design_Pattern_Sequence_Diagram_UML.svg.png)

- Facade is like a deputy, managing its complex sub-systems while exposing an entry point to users.
- Sub-systems should not have references of the Facade.

```python
/* Complex parts */
class CPU:
  def freeze():
    pass
  def jump(to_position):
    pass
  def execute():
    pass
  
class Memory:
    def load(to_position, with_data):
      pass

class HardDrive:
    def read(lba, size) -> bytes:
      pass

BOOT_SECTOR = ""
SECTOR_SIZE = 32
# Facade
class ComputerFacade {
    
    def __init__(self) {
        self.processor = CPU()
        self.ram = Memory()
        self.hd = HardDrive()
    }

    def start(self) {
        self.processor.freeze();
        self.ram.load(BOOT_ADDRESS, self.hd.read(BOOT_SECTOR, SECTOR_SIZE));
        self.processor.jump(BOOT_ADDRESS);
        self.processor.execute();
    }
}

/* Client */
computer = ComputerFacade();
computer.start();
  
```



## pros

- Simple interface, hiding complexities.
- Sub-systems can loose their coupling.



## Cons

- Sub-systems are tightly coupled with the Facade -> higher maintenance effort.

