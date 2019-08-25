class: center, middle

Llenguatges de Programació

# Python 3: elements bàsics

Gerard Escudero i Albert Rubio

<br>

![Python 3](img/python-logo.svg)

<br>

Universitat Politècnica de Catalunya, 2019

---


## Introducció

.cols5050[
.col1[
Paradigmes:

  - imperatiu,

  - orientat a objectes,

  - funcional.
]
.col2[
Característiques:

  - interpretat,

  - llegibilitat,

  - *lazyness*.
]]

Té una gran quantitat de llibreries disponibles.

---

## Entorns

### 'Hello world!'

```python3
print('Hello world!')
```

```python3
nom = input('Com et dius? ')
print('Hola', nom + '!')
```

### Línia de comandes: 

`python3 script.py`

### *idle* : entorn Python 3 

`idle` o `idle3`

### Codificació:

Utilitza 'utf-8'

---

## Blocs i comentaris

### Comentaris

```python3
# això és un comentari
```

### Blocs

Els blocs (*suites*) es marquen per l'identació. 

```python3
if condicio:
    print('ok!')
```

L'estil estàndard *PEP8* es marcar-ho amb 4 espais.

### Línies llarges

Podem tallar línies amb `\`:
```python3
total = x*100 + \
        y*10
```

---

## Variables i assignació

Declaració implícita (valor): 
```python3
a = 2
```

Assignació:
```python3
x = y = z = 0
x, y, z = 0, 5, "Llenguatges"
x, y = y, x
```

Assignació augmentada:
```python3
a += 3
```

⚠️ No té ni `--` ni `++`!


---

## Tipus estàndard

### Nombres

`int`, `float`, `complex`

Són tipus i funcions de conversió.

`int` no té rang. Pot tractar nombres arbitrariàment llargs.

Operadors usuals excepte: `**` (potència) i `//` (divisió entera)

.col5050[
.col1[
### Booleans

`True`, `False`

Operadors: `and`, `or`, `not`
]
.col2[
### Funcions de tipus
```python3
type(3)  👉  int 

isinstance(3, int)  👉  True

isinstance(3, (float, bool))  👉  False 

```
]]

---

## Condicionals

### Acció (*statement* ):

```python3
if x < 0:
    signe = -1
elif x > 0:
    signe = 1
else:
    signe = 0
```
Els `else` i `elif` són opcionals.

### Expressió:
```python3
x = 'parell' if 5 % 2 == 0 else 'senar'
```

---

## Iteracions

Taula de multiplicar:

### while

```python3
n, i = int(input('n? ')), 1
while i <= 10:
    print(n, 'x', i, '=', n * i)
```

### for

```python3
n = int(input('n? '))
for i in range(1, 11):
    print(n, 'x', i, '=', n * i)
```

El `for` funciona sobre tipus *iterables*.

També podem usar el `break` i el `continue`, amb la semàntica usual sobre tots dos bucles.

---

## Strings I

Tipus `str`.

Els *strings* són iterables.

### Operacions:
```python3
z = 'Llenguatges'

z[2]    👉  'e'        # posició 

z[3:6]  👉  'ngu'      # subcadena 

z[:4]   👉  'Llen'     # prefix 

z[8:]   👉  'ges'      # sufix 

z + ' Programació'   👉  'Llenguatges Programació'   # concatenar 

z * 3   👉  'LlenguatgesLlenguatgesLlenguatges'      # repetir 

len(z)  👉  11         # mida
```

---

## Strings II

### Altres operacions i mètodes

```python3
z = 'Llenguatges'

'ng' in z  👉  True 

'ng' not in z  👉  False

z.find('ng')  👉  3               # cerca i torna posició

z.count('e')  👉  2               # comptar  

'Hello world!\n'.strip()  👉  'Hello world!'   # treu el \n

'1,2,3'.split(' ')  👉  ['1', '2', '3']    # parteix un string

','.join(['1', '2', '3'])  👉  '1,2,3'     # operació inversa
```

Els *strings* són *immutables*:

```python3
z[0] = 'l'  ❌   # TypeError: 'str' object does not support item assignment
```

---

## Funcions I

Declaració:

```python3
def primer(n):
    for d in range(2, n // 2):
        if n % d == 0:
            return False
    return True
```

Crida:

```python3
primer(5)  👉  True
```

### Retorn de múltiples valors:

```python3
def divisio(a, b):
    return a // b, a % b

x, y = divisio(7, 2)  # x 👉 3, y 👉 1
```
Quan tornem més d'un valor ho fa internament en forma de *tupla*.

---

## Funcions II

### Valors per defecte:

```python3
from string import punctuation

def remPunc(s, tl=True):
    rt = ''
    for c in s.lower() if tl else s:
        if c not in punctuation:
            rt = rt + c
    return rt

remPunc('Hola, sóc un exemple!')
👉  'hola sóc un exemple'

remPunc('Hola, sóc un exemple!', tl=False)
👉  'Hola sóc un exemple'
```

---

## Llistes

Les llistes (`list`) són heterogènies:
```python3
z = ["hola",5,"llenguatge",6.63,2]
```

Tenen les mateixes operacions que els *strings* i també són *iterables*.

Per recorrer dos o més iterables podem utilitzar el `zip`:
```python3
def prodEscalar(v, w):
    res = 0
    for x, y in zip(v, w):
        res += x * y
    return res
```

Altres operacions predefinides de la classe `list`:

- `append`, `count`, `pop`, etc.

---

## Tuples

Les tuples (`tuple`) són:

- com les llistes
- *immutables* (de només de lectura)

```python3
z = ("hola",5,"llenguatge",6.63,2)
z = (5,)      # tupla d'un sol element
              # (5) és l'enter 5
```

## Conjunts

Els conjunts (`set`) admeten les operacions: 

- `len`, `in`, `not in`, issubset (`<=`), issuperset (`>=`),
- union (`|`), intersection (`&`), difference (`-`),
- `add`, `remove` ...

---

## Diccionaris

Els diccionaris (`dict`):

- contenen parells clau-valor 
- permeten accés directe

```python3
dic = {}                   # diccionari buit
dic["prim"] = "el primer"  # afegir o actualitzar un element
del(dic['prim`])           # esborrar-lo 
dic = {"nom": "albert","num":37899, "dept": "computer science"} 
                           # inicialitzar-lo amb dades
```

Els diccionaris són iterables (en iterar amb el for recorrem les claus):
```python3
def suma(d):
    s = 0
    for k in d:
        s += d[k]
    return s

suma({'a': 1, 'b': 2})  👉  3
```
---

## Classes I

Exemple de classe:

```python3
class Treballador:
    treCompt = 0                # atribut de classe: 
                                # un únic valor per classe
    def __init__(self, nom, salari):  # constructor
        self.nom = nom          # definició d'atribut  
        self.salari = salari    # self representa el objecte creat
        Treballador.treCompt += 1

    def getCompt(self):         # definició de mètode
        return Treballador.treCompt

    def getSalari(self):
        return self.salari
```

Exemple d'interacció:

```python3
tre1 = Treballador("Pep", 2000)
tre2 = Treballador("Joan", 2500)
tre1.getSalari()  👉  2000
tre1.salari  👉  2000 
tre1.getCompt()  👉  2
Treballador.treCompt  👉  2
```

---

## Classes II

Podem afegir, eliminar o modificar atributs de classes i objectes en qualsevol
moment:

```python3
tre1.edat = 8
tre1.edat  👉  8
hasattr(tre1, 'edat')  👉  True
del tre1.edat
hasattr(tre1, 'edat')  👉  False
```

La comanda `dir` ens retorna la llista d'atributs d'un objecte. Hi ha molts predefinits:
- `__dict__`, `__doc__`, `__name__`, `__module__`, `__bases__`.

### Atributs ocults

```python3
class Treballador:
    __treCompt = 0          # atribut ocult: començant per 2 _ 

tre1.__treCompt  ❌
tre1.tre1._Treballador__treCompt  👉  2
```

---

## Herència

Exemple:

```python3
class Fill(Treballador):    # pare entre ( )
    def fillMetode(self):
        print(’Cridem al metode del fill’)

tre3 = Fill('Manel', 1000)
tre2.fillMetode()
```

Podem tenir herència múltiple:

```python3
class A: # definim la classe A
.....
class B: # definim la calsse B
.....
class C(A, B): # subclasse de A i B
.....
```

Per fer comprovacions:

- `issubclass(sub, sup)`
- `isinstance(obj, Class)`

