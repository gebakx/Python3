class: center, middle

Llenguatges de Programació

# Python 3: *lazyness* i part funcional

Gerard Escudero

<br>

![Python 3](img/python-logo.svg)

<br>

Universitat Politècnica de Catalunya, 2019

---

# Iteradors

En python existeixen mols tipus que són iterables: 
- strings, llistes, diccionaris i conjunts
- definits per l'usuari.

El protocol d'iteració el defineixen els mètodes:
- `__iter__` i `next`

i llença l'excepció `StopIteration` en acabar:

.col5050[
.col1[
```python3
s = 'abc'
for c in s:
    print(c)
👉
a
b
c
```
]
.col2[
```python3
s = 'abc'
it = iter(s)
while True:
    try:
        print(it.__next__())
    except StopIteration:
        break
```
]]


---

# Generadors I

Són el mecanisme que permeten l'avaluació *lazy* en python3.

Exemple: sèrie de fibonacci fins a un nombre determinat.

.col5050[
.col1[
```python3
def fib(n):
    a = 0
    yield a
    b = 1
    while True:
        if b <= n:
            yield b
            a, b = b, a + b
        else:
            raise StopIteration
```
]
.col2[
```python3
f = fib(1)
next(f)  👉  0
next(f)  👉  1
next(f)  👉  1
next(f)  ❌  StopIteration

## La comanda yield para l'execució 
## de la funció fins a la següent 
## invocació de next.


## amb list comprehension
[x for x in fib(25)]
👉
[0, 1, 1, 2, 3, 5, 8, 13, 21]
```
]
]
 
---

# Generadors II

Els generadors poden ser infinits:

```python3
def fib2():
    a = 0
    yield a
    b = 1
    while True:
        yield b
        a, b = b, a + b
```

```python3
f = fib2()
[next(f) for _ in range(8)]
👉
[0, 1, 1, 2, 3, 5, 8, 13]
```

---

# Generadors III

i classes:

```python3
class fib3:
    def __init__(self, n):
        self.final = n
        
    def __iter__(self):
        a = 0
        yield a
        b = 1
        while True:
            if b <= self.final:
                yield b
                a, b = b, a + b
            else:
                raise StopIteration
```

```python3
[x for x in fibo(8)]
👉
[0, 1, 1, 2, 3, 5, 8]
```

Són útils quan volem tenir varies instàncies del generador funcionant a l'hora.

---



# Funcions anònimes

Les funcions són un tipus intern en python (*function*). Poden ser tractades com a dades i, per tant, com a paràmetres d'una funció.

Disposem de funcions anònimes tipus *lambda*:
```python3
lambda parametres: expressió
```
on els `parametres` són zero o més paràmetres separats per comes.

```python3
doble = lambda x: 2 * x      # una altra forma de definir funcions

doble(3)  👉  6

type(doble)  👉  <class 'function'>
```

Una aplicació pràctica és el paràmetre `key` de les funcions `max`, `min` i `sort`:

```python3
d = {'a': 2, 'b': 1}

max(d, key = lambda x: d[x])  👉  'a'      # clau amb valor màxim d'un diccionari
```

---

# Funcions d'ordre superior I

### map
`map(funció, iterable)  👉  generador`: aplica la funció a cadascun dels elements de l'iterable.
```python3
list(map(lambda x: x * 2, [1, 2, 3]))  👉  [2, 4, 6]
```

### filter
`filter(funció, iterable)  👉  generador`: és el subiterable amb els elements que fan certa la funció booleana.
```python3
mg3 = lambda x: x > 3

list(filter(mg3, [3, 6, 8, 1]))  👉  [6, 8]
```

---

# Funcions d'ordre superior II

### reduce (fold)
`reduce(funció, iterable[, valor_inicial])  👉  valor`: desplega una funció per l'esquerra.
```python3
from functools import reduce

reduce(lambda acc,y: acc+y, [3,6,8,1])  👉  18

reduce(lambda acc,y: acc+y, [3,6,8,1], 0)  👉  18
```

---

# Llistes per comprensió I

### amb llistes

`[expressió for variable in iterable if expressió]`
```python3
[x ** 2 for x in range(4)]  👉  [0, 1, 4, 9]

[x for x in [0, 1, 4, 9] if x % 2 == 0]  👉  [0, 4]

[(x, y) for x in [1, 2] for y in 'ab']
👉  [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]
```

### amb conjunts
```python3
{x for x in range(4) if x % 2 == 0}
👉  {0, 2}
```

---

# Llistes per comprensió II

### amb diccionaris
```python3
{x: x % 2 == 0 for x in range(4)}
👉  {0: True, 1: False, 2: True, 3: False}
```

### amb generadors

```python3
from itertools import count      # count és un generador infinit

g = (x**2 for x in count(1))     # g és un generador
                                 # dels quadrats dels naturals

next(g)  👉  1

[next(g) for _ in range(4)]  👉  [4, 9, 16, 25]
```
---

# Mòdul *operator*

La llibreria *operator* conté tots els operadors estàndard en forma de funcions, per a ser usades en funcions d'ordre superior.

```python3
from operator import mul
from functools import reduce

factorial = lambda n: reduce(mul, range(1, n+1))

factorial(5)  👉  120
```

Alguns exemples de funcions que conté són:

- `iadd(a, b)`: equivalent a `a += b`

- `attrgetter(attr)`:
```python3
f = attrgetter('name')
f(b)  👉  b.name
```

---

# Mòdul *itertools*

Aquesta llibreria conté moltes funcions relacionades amb les iteracions.

Té moltes funcions equivalents a Haskell:
```python3
from operator import mul
from itertools import accumulate

factorials = lambda n: accumulate(range(1, n + 1), mul)

[x for x in factorials(5)]  👉  [1, 2, 6, 24, 120]
```

Altres funcions amb equivalents Haskell són: `dropwhile`, `islice` (`take`),`repeat` o `takewhile`.

Té algunes funcions que fan d'iteradors combinatòrics: `product`, `permutations`, `combinations` o `combinations_with_replacement`.

És interessant fer un repàs a la documentacio d'aquesta llibreria:
https://docs.python.org/3.7/library/itertools.html

---

# Exercicis

* Feu aquests problemes de Jutge.org:

  - [P80049](https://jutge.org/problems/P80049_ca) Ús d'iterables

  - [P66679](https://jutge.org/problems/P66679_ca) Llistes per comprensió

  - [P73993](https://jutge.org/problems/P73993_ca) Funcions d'ordre superior

  - [P45231](https://jutge.org/problems/P45231_ca) Generadors

  - [P53498](https://jutge.org/problems/P53498_ca) Definició d'un iterable

