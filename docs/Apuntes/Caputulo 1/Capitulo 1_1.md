# Capitulo 1. Creando abstracciones con procedimientos

Los procesos son una manipulación de datos. La evolución de estos procesos y sus reglas son dictaminadas por los programas. Las tareas de un programa se expresan mediante lo que denominamos lenguajes de programación.

Para lo abordado en estos presentes documentas usaremos LISP (LIST Processiong), para expresar los pensamientos procedurales. "Scheme" es el dialecto de LISP que usaremos: se selecciona debido a los conceptos y sus manejos sobre LISP. Por ejemplo los procedimientos sobre LISP pueden ser manipulados como datos. Permitiendo también que los programas sean manipulados con otros programas, siendo un programa datos para otro.

## 1.1. Elementos de un programa

Todo lenguaje poderoso debe contar con:

- Expresiones primitivas.
- Medios de combinación: elementos hechos de elementos simples.
- Medios de abstracción: los elementos son manipulables como unidades y pueden ser nombrados.

### 1.1.1. Expresiones

Las entradas (inputs), son datos a evaluar por el interprete, y generan una respuesta.

Veamos lo siguiente:

```

    (+  137 349)
```

Esta expresión construye una "S-Expressions", y consideramos que representa una combinación. Donde se presente un operador "+", cuyos argumentos u operandos son los números 137 y 349. Al insertar este código en un interprete de Schema obtenemos el resultado 486, cuyo significado atribuimos como algo trivial.

Debemos identificar lo siguiente: las operaciones se situan como prefijos, permitiendo que gozen de un numero arbitrario de argumentos. Al igual que los elementos de una combinación pueden ser elementos de una combinación per se.

```scheme
    (+  (*  3   5)  (-  10  6))
```

#### 1.1.1.1. Sintaxis recomendada

El pretty-printing sigue reglas como:

Cada subexpresión importante comienza en una nueva línea.
Las expresiones relacionadas se alinean verticalmente.
La indentación refleja la profundidad del árbol sintáctico (abordado más adelante).

```scheme
(* (+ 2 3)
   (- 8 1))

```
```
      *
     / \
    +   -
   / \ / \
  2  3 8  1
```

```scheme
    (+  (*  3
            (+  (*  2   4)
                (+  3   5)))
        (+  (-  10  7)
            6))
```

Esto se aborda con mayor profundidad en un apartado designado posteriormente, ya que existen ciertas particularidades con ciertas expresiones.

### 1.1.2. Nombrado (naming) y entorno
Las variables se referencian mediante nombres (son su identificar de manera abstracta), cuyo valor es el objeto que posee. El nombramiento de valores se hace mediante "define".
```scheme
    (define size    5)
    (*  5   size)
```
El resultado es 25.

Los programas requieren memoria para saber a que se refiere cada nombre junto a su variable. Esta memoria construye lo que denominamos un entorno.

### 1.1.3. Evaluado Combinaciones
1. Se evalúan las subexpresiones de la combinación.
2. Aplicar el operador a los operandos.
Esto es un proceso recursivo (el paso 1, requiere evaluar cada subpexpresión, es decir, requiere evaluar todo el proceso como paso).

Existen excepciones a esta manera de evaluar, procedimientos compuestos (Compound Procedures).

### 1.1.4. Procedimientos Compuestos
Encontramos que cualquier lenguaje de programación "potente" debe constatar de lo siguiente:

- Números y operaciones aritmeticas: donde los numeros son datos y las operaciones procedimientos.
- Combinaciones concatenadas.
- Definiciones que asocien nombres con valores.

#### 1.1.4.1. Definiciones de procedimientos

Consideremos que queremos definir elevar un número a su cuadrado:

```schema
    (define (square x)  (*  x   x))
    (square 21)
```
Obtenemos 441.

¡Tenemos aquí un procedimiento compuesto (así llamamos a los procedimientos que definimos nosotros)! Que en términos más formales, fue definido siguiendo esta estructura:

```schema
    (define (<name> <formal parameters>) <body>)
```

Dónde los parametros son variables en un entorno local (según el ejemplo, "x", no puede referenciar a un "x" fuera del procedimiento).

Es importantisimo conocer que dentro del body de un procedimiento, podemos llamar elementos a otros procedimientos. Y que los parámetros pueden ser combinaciones:

```schema
    (square (square (+  2   5)))
```

Obtenemos 49.

#### 1.1.5. Modelo de sustitución para aplicación de procedimientos

Según lo visto en 1.1.3, el interprete evalua combinaciones, y luego aplica los procedimientos sobre los resultados. Esto es completamente valido para los procedimientos primitivos, los compuestos se rigen bajo otra norma:

- Para los argumentos del procedimiento, se hace un remplazo directo en todo el cuerpo. Esto se denomina modelo de sustitución.

Este modelo es solo una abstracción del funcionamiento del interprete, no corresponde a una descripción exacta. Pero, con dicho modelo podemos tener presente el comportamiento que va a tener el procedimiento al momento de aplicarse.

##### 1.1.1.5.1. Orden de Aplicación vs Orden Normal
El Orden de Aplicación (applicative-order) podemos se encuentra presente en LISP y JavaScript. Es el presentado con anterioridad, y define que los argumentos de un procedimiento/función son evaluados al momento en que se llama a la función.

El Orden Normal, es un método de evaluación perezosa (lazy), en el que los argumentos de un procedimiento/función son evaluados al momento de requerirse.

Es fundamental reconocer que existen procedimientos que pueden fallar o no al aplicar cierto orden. Veamos un ejemplo (El ejercicio 1.5):

Teniendo estos procedimientos:

```schema
    (define (p) (p))

    (define (test   x   y))
        (if (=  x   0)
            0
            y))
```

Qué resultado obtenemos al evaluar:

```schema
    (test   0   (p))
```

Bajo el Orden de Aplicación, obtenemos lo siguiente:

```schema
    (test   0   (p))

```

Obtenemos un error en el interprete. "(p)" es una función recursiva que da como resultado sí misma, y obtenemos una recursión infinita, no da ningún valor.

Bajo el Orden de Normal, obtenemos lo siguiente:

```schema
    (test   0   (p))

    (if (=  0   0)
            0
            (p)))

```
Obtenemos 0. El valor de (p) no da problemas porque jamás fue requerido.

#### 1.1.6. Los Condicionales

La siguiente expresión constituye la definición de un procedimiento que representa el valor absoluto de una número.

```math
|x| =
\begin{cases}
x, & \text{si } x \ge 0, \\
-x, & \text{si } x < 0.
\end{cases}
```

```schema
    (define (abs    x)
            (cond   ((> x   0)  x)
                    ((=  x   0)  0)
                    ((<  x   0) (-x))))
```

El procedimiento "cond" tiene como argumentos un conjunto de predicados con sus respectiva expresión.

```schemaintroduction
    (cond   (<p1>   <e1>)
            (<p2>   <e2>)
            ...
            (<pn>   <en>))
```


#### 1.1.6.1. Operadores lógicos

Los predicados pueden emplear operadores lógicos, que se escribe como:
- and
- or
- not

Teniendo todos como argumentos una cantidad arbitraria de expresiones.

#### 1.1.6.2. IF

Existe otra manera de escribir la expresión abs:

```schema
    (define (abs    x)
            (if   (> x   0)
                x
                (-x)))
```

El procedimiento if, sigue esta estructura:

```schema
    (if <predicate> <consequence> <alternative>)
```






