class: center, middle

Llenguatges de Programació

# Python 3: memorització, clausures i decoradors

Gerard Escudero i Albert Rubio

<br>

![Python 3](img/python-logo.svg)

<br>

Universitat Politècnica de Catalunya, 2019

---

# Memorització amb valors per defecte

Aquesta característica ens permet fer programació dinàmica fàcilment:

### Fibonacci

.col5050[
.col1[
#### Versió recursiva:

```python3
def fib(n):
    if n in [0, 1]:
        return n
    return fib(n-1) + fib(n-2)
```

```python3
test(fib, 40)
👉
valor: 102334155
temps(s): 27.68606
```
]
.col2[
#### Programació dinàmica:

```python3
def efib (n, mem={0:0, 1:1}):
    if n not in mem:
        mem[n] = efib(n-1) + efib(n-2)
    return mem[n]
```

```python3
test(efib, 40)
👉
valor: 102334155
temps(s): 0.00013
```
]
]

---

# Funcions niuades

Python3 accepta funcions niuades.

Un exemple és la funció `test` de l'exemple anterior. Conté una funció `prec` a dins seu.

```python3
from ttictoc import TicToc

def test(f, n):

    def prec(x):
        return '{:.5f}'.format(x)

    t = TicToc() ## TicToc("name")
    t.tic();
    valor = f(n)
    t.toc();
    print('valor:', valor)
    print('temps(s):', prec(t.elapsed))
```

---

# Memorització amb funcions niuades

La solució passa per definir un diccionari i una funció imbricada:

```python3
def efib2(x):
    mem = {0:0, 1:1}       # la memòria

    def mfib(n):
        if n not in mem: 
            mem[n] = mfib(n-1) + mfib(n-2)
        return mem[n]

    return mfib(x)
```

```python3
test(efib2, 40)
👉
valor: 102334155
temps(s): 0.00012
```


---

# Clausures

Una clausura (*closure*) és una mena funció *callback*. 

```python3
def test(n):
    def prec(x):
        return '{:.5f}'.format(x)

    def clausura(f):
        t = TicToc() ## TicToc("name")
        t.tic();
        valor = f(n)
        t.toc();
        print('valor:', valor)
        print('temps(s):', prec(t.elapsed))

    return clausura
```

```python3
test40 = test(40)
test40(efib)
👉
valor: 102334155
temps(s): 0.00020
```

S'utilitzen per amagar dades (*data hiding*) i evitar així les variables globals.

---

# Memorització genèrica

Podem fer-ho amb una funció d'ordre superior o amb classes.

```python3
def memoritza (f):
    # Això es pot fer perquè els diccionaris són mutables.
    mem = {}       # la memòria

    def f2 (x):
        if x not in mem:
            mem[x] = f(x)
        return mem[x]
    return f2

def fib(n):
    if n in [0, 1]:
        return n
    return fib(n-1) + fib(n-2)
```

```python3
# Com que la funció és recursiva hem de redefinir la funció
test40 = test(40)
fib = memoritza(fib)
test40(fib, 40)
👉
valor: 102334155
temps(s): 0.00018
```

---

# Decoradors

Són un métode per alterar quelcom invocable (*callable*).

Ho podem fer mitjançant les clausures.

.col5050[
.col1[
```python3
def testDec(f):
    def wrapper(*args):
        valor = f(*args)
        print('fib(' + str(args[0]) + ') = ' + \
              str(valor))
        return valor

    return wrapper

@testDec
def efib (n, mem={0:0,1:1}):
    if n not in mem:
        mem[n] = efib (n-1) + efib (n-2)
    return mem[n]
```
]
.col2[
```python3
test4 = test(4)
test4(efib)
👉
fib(1) = 1
fib(0) = 0
fib(2) = 1
fib(1) = 1
fib(3) = 2
fib(2) = 1
fib(4) = 3
valor: 3
temps(s): 0.11166
```
]
]

---

# Decoradors parametritzats

Podem afegir arguments parametritzant els decoradors:

.col5050[
.col1[
```python3
def testInterval(inici, fi):
    def decorador(f):
        def wrapper(*args):
            valor = f(*args)
            n = args[0]
            if inici <= n <= fi:
                print('fib(' + str(n) + ') = ' + \
                      str(valor))
            return valor

        return wrapper
    return decorador

@testInterval(35, 40)
def efib (n, mem={0:0,1:1}):
    if n not in mem:
        mem[n] = efib (n-1) + efib (n-2)
    return mem[n]
```
]
.col2[
```python3
test40 = test(40)
test40(efib)
👉
fib(35) = 9227465
fib(36) = 14930352
fib(35) = 9227465
fib(37) = 24157817
fib(36) = 14930352
fib(38) = 39088169
fib(37) = 24157817
fib(39) = 63245986
fib(38) = 39088169
fib(40) = 102334155
valor: 102334155
temps(s): 0.13049
```
]
]



