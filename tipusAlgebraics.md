class: center, middle

Llenguatges de Programació

# Python 3: tipus enumerats i algebraics

Gerard Escudero

<br>

![Python 3](img/python-logo.svg)

<br>

Universitat Politècnica de Catalunya, 2023

---

# Tipus enumerats

Donen la llista de valors possibles dels objectes:

**Pedra, paper, tisores:**

```python3
class Pedra:
    pass

class Paper:
    pass

class Tisores:
    pass

Jugada = Pedra | Paper | Tisores

guanya = lambda a, b: (a, b) in [(Paper,Pedra),(Pedra,Tisores),(Tisores,Paper)]

```

**Exemple:**

Guanya la primera a la segona?

```python3
guanya(Paper, Pedra)  👉  True
```

---

# Tipus algebraics 

Defineixen constructors amb zero o més dades associades:

```python3
from dataclasses import dataclass

@dataclass
class Rectangle:
    amplada: float
    alçada: float

@dataclass
class Quadrat:
    mida: float

@dataclass
class Cercle:
    radi: float

class Punt:
    pass

Forma = Rectangle | Quadrat | Cercle | Punt
```

- El decorador `dataclass` defineix automàticament mètodes com `__init__`.

---

# Funcions amb tipus algebraics

**Declaració:**

```python3
from math import pi

def area(f):
    match f:
        case Rectangle(amplada, alçada):
            return amplada * alçada
        case Quadrat(mida):
            return area(Rectangle(mida, mida))
        case Cercle(radi):
            return pi * radi**2
        case Punt():
            return 0
```

- `match` permet reconèixer patrons

**Crida:**

```python3
area(Quadrat(2.0))  👉  4.0
```


---

# Tipus recursius

**Arbre binari:**

.cols5050[
.col1[
```python3
from __future__ import annotations
from dataclasses import dataclass

class Buit:
    pass

@dataclass
class Node:
    val: int
    esq: Arbre
    dre: Arbre

Arbre = Node | Buit
```
]
.col2[
```python3
def mida(a: Arbre) -> int:
    match a:
        case Buit():
            return 0
        case Node(x, e, d):
            return 1 + mida(e) + mida(d)
```

```python3
t = Node(1,Node(2,Buit(),Buit()),
           Node(3,Buit(),Buit()))

mida(t)  👉  3
```
]]

- `annotations` permet els tipus recursius (en aquest cas `Arbre`).






