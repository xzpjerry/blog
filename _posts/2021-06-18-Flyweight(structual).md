---
layout: post
published: true
tags: design_pattern structual
---

- minimizing mem usage by data sharing between **similar** objects

```python
class Particle:
    def __init__(self, color) -> None:
        self.color = color
        # assume it needs 20KB for each particle
        # if we have 1,000,000 particle, mem usage 
        # would reach about 20GB
    
    def __str__(self) -> str:
        return "particle"
    
    def move(self, x, y):
        return f"moved {self} to ({x}, {y})"

class Bullet(Particle):
    def __str__(self) -> str:
        return f"a {self.color} bullet"

class Missile(Particle):
    def __str__(self) -> str:
        return f"a {self.color} missle"

class ParticlePool:
    pool = dict()

    def __new__(cls, partcle_type, *args):
        key = f"{partcle_type}_{args}"
        obj = cls.pool.get(key)
        if obj is None:
            cls.pool[key] = partcle_type(*args)
            obj = cls.pool[key]
        return obj
# now we only need one instance for each type of particle

for _ in range(1000000):
    ParticlePool(Bullet, "blue").move(2,0)
```

