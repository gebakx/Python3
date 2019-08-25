class: center, middle

Llenguatges de Programació

# Python 3: compiladors

Gerard Escudero 

<br>

<img src="img/python-logo.svg" width=300>
![ANTLR](img/antlr.png)

<br>

Universitat Politècnica de Catalunya, 2019

---

## El primer programa ANTLR

Arxiu de gramàtica `Expr.g`:

```
grammar Expr;

root : expr EOF ;

expr : expr MES expr
    | NUM
    ;

NUM : [0-9]+ ;
MES : ’+’ ;
WS : [ \n]+ -> skip ;
```

⚠️ Noteu que el nom de l’arxiu ha de concordar amb el de la gramàtica.

* `expr`: definició de la gramàtica per la suma de nombres naturals. 

* `skip`: indica a l’escàner que el token WS no ha d’arribar al parser.

* `root`: per processar el final d’arxiu.

---

## Compilació a Python3

La línia de comandes:

```
antlr4 -Dlanguage=Python3 -no-listener Expr.g
```

genera els arxius:

* `ExprLexer.py` i `ExprLexer.tokens`

* `ExprParser.py` i `Expr.tokens`

⚠️ Noteu que els arxius anteriors comencen pel nom de la gramàtica.

---

## Construcció de l’script principal

Script de test:

```python3
import sys
from antlr4 import *
from ExprLexer import ExprLexer
from ExprParser import ExprParser
from antlr4.InputStream import InputStream

if len(sys.argv) > 1:
    input_stream = FileStream(sys.argv[1])
else:
    input_stream = InputStream(input(’? ’))

lexer = ExprLexer(input_stream)
token_stream = CommonTokenStream(lexer)
parser = ExprParser(token_stream)
tree = parser.root()
print(tree.toStringTree(recog=parser))
```

Noteu que aquest script llegeix l’entrada estàndard o un arxiu de text.

Test: `3 + 4  👉  (root (expr (expr 3) + (expr 4)) <EOF>)`

Què passa amb: `3 + +`, `3 3` o `3 + 4 + 5`?

---

## Notes sobre gramàtiques

#### Recursivitat per l’esquerra:

Amb les versions anteriors no es podia afegir una regla de l’estil: <br>
`expr : expr ’*’ expr`

Per solucionar això s’afegien regles tipus 
`expr : NUM ’*’ expr`

.col5050[
.col1[
#### Precedència d’operadors:

Amb l'ordre d'escriptura:
```
expr : expr ’*’ expr
     | expr ’+’ expr
     | INT
     ;
```
]
.col2[
#### Associativitat:

L’associativitat com la potència <br> queda com:
```
expr : expr ’^’<assoc=right> expr
     | INT
     ;
```
]
]

---

## Exercici 1

Afegiu a la gramàtica els operadors de:
* resta

* multiplicació

* divisió 

* potència

Tingueu en compte:
* la precedència d’operadors 

* la associativitat a la dreta dela potència

---

## Visitors

Els *visitors* són *tree walkers*, un mecanisme per recòrrer els ASTs.

Amb la comanda:

`antlr4 -Dlanguage=Python3 -no-listener -visitor Expr.g`

compilarem la gramàtica i generarem la plantilla del visitor (`ExprVisitor.py`):

```python3
# Generated from Expr.g by ANTLR 4.5.1
from antlr4 import *
if __name__ is not None and "." in __name__:
    from .ExprParser import ExprParser
else:
    from ExprParser import ExprParser

# This class defines a complete generic visitor ...
class ExprVisitor(ParseTreeVisitor):

    # Visit a parse tree produced by ExprParser#expr.
    def visitExpr(self, ctx:ExprParser.ExprContext):
        return self.visitChildren(ctx)

del ExprParser
```

`visitExpr` és el *callback* associat a la regla `Expr` per visitar-la.

---

## Visitor per recorrer l'arbre

Codi d'un *visitor* per mostrar l'arbre:

```python3
...

class ExprVisitor(ParseTreeVisitor):
    def __init__(self):
        self.nivell = 0  # nivell de profunditat del node

    def visitExpr(self, ctx:ExprParser.ExprContext):
        if ctx.getChildCount() == 1:
            n = next(ctx.getChildren())
            print(" " * self.nivell + \
                  ExprParser.symbolicNames[n.getSymbol().type] + \
                  ’(’ +n.getText() + ’)’)
            self.nivell -= 1
        elif ctx.getChildCount() == 3:
            print(’ ’ * self.nivell + ’MES(+)’)
            self.nivell += 1
            self.visit(ctx.expr(0))
            self.nivell += 1
            self.visit(ctx.expr(1))
            self.nivell -= 1

#del ExprParser
```

---

## Informació per crear *visitors*

Accés als components de la part dreta de la regla:

* amb els fills: `ctx.getChildCount()` i `ctx.getChildren()` (és un generador)

* els propis components: `ctx.expr(0)` correspon al 1er node *expr* de la regla

Altres mètodes interessants:

* `n.getText()`: text del node

* `ExprParser.symbolicNames[n.getSymbol().type]`: token del node en format text

* `ExprParser.MES`: índex intern del token MES per al parser. Es
sol utilitzar junt amb `n.getSymbol().type`


---

## Arxiu de test

L'arxiu de test l'hem de modificar:

```python3
import sys
from antlr4 import *
from ExprLexer import ExprLexer
from ExprParser import ExprParser
from antlr4.InputStream import InputStream
from ExprVisitor import ExprVisitor

if len(sys.argv) > 1:
    input_stream = FileStream(sys.argv[1])
else:
    input_stream = InputStream(input(’? ’))

lexer = ExprLexer(input_stream)
token_stream = CommonTokenStream(lexer)
parser = ExprParser(token_stream)
tree = parser.root()

visitor = ExprVisitor()
visitor.visit(tree)
```

---

## Execució

Un exemple de resultat de l'script anterior:

```
3 + 4
👉
MES(+)
  NUM(3)
  NUM(4)
```

## Exercici 2

Afegiu el mecanisme per mostrar l’arbre generat a la gramàtica <br> de l’exercici 1.

---

## Avaluació i interpretació d’ASTs

*Visitor* per avaluar les expressions:

```python3
...
class ExprVisitor(ParseTreeVisitor):
    def visitRoot(self, ctx:ExprParser.RootContext):
        n = next(ctx.getChildren())
        print(self.visit(n))

    def visitExpr(self, ctx:ExprParser.ExprContext):
        if ctx.getChildCount() == 1:
            n = next(ctx.getChildren())
            return int(n.getText())
        elif ctx.getChildCount() == 3:
            g = ctx.getChildren()
            l = [next(g) for i in range(3)]
            return self.visit(l[0]) + self.visit(l[2])

del ExprParser
```

Exemple:

`3 + 4 + 5  👉  12`

---

## Exercici 3

Afegiu el tractament d’avaluació per la resta d’operadors de l'exercici 3.

## Exercici 4

Definiu una gramàtica i el seu mecanisme d’avaluació/execució per a quelcom tipus:
```
x := 3 + 5
write x
y := 3 + x + 5
write y
```

Nota: es pot utilitzar un diccionari com a taula de símbols.

## Exercici 5

Amplieu l’exercici anterior per a que tracti quelcom com el següent:
```
c := 0
b := c + 5
if c = 0 then
    write b
endif
```

---

## Exercici 6

Exploreu que passa si realitzem l’exercici anterior sense el token `endif`.

## Exercici 7

Amplieu l’exercici anterior per a que tracti l’estructura `while`:
```
i := 1
while i <> 11 do
    write i * 2
    i := i + 1
endwhile
```

---

# Referències

1. Terence Parr. *The Definitive ANTLR 4 Reference*, 2nd Edition. Pragmatic Bookshelf, 2013.

2. Alan Hohn. *ANTLR4 Python Example*. Últim accés: 26/1/2019.
https://github.com/AlanHohn/antlr4-python

3. Guillem Godoy i Ramón Ferrer. *Parsing and AST cosntruction with PCCTS*. Materials d’LP, 2011.
