# Método de Calculo de Raíces con el método de Newton

Según [Wikipedia](https://es.wikipedia.org/wiki/M%C3%A9todo_de_Newton): "En análisis numérico, el método de Newton (conocido también como el método de Newton-Raphson o el método de Newton-Fourier) es un algoritmo para encontrar aproximaciones de los ceros o raíces de una función real. También puede ser usado para encontrar el máximo o mínimo de una función, encontrando los ceros de su primera derivada".

Permitiendonos tener la siguiente función que aproxima a valores que generen cero dentro de una función:

```math
x_{n+1}
=
x_n
-
\frac{f(x_n)}{f'(x_n)}
```

Para calcular raíces cuadradas mediante el método de Newton, debemos construir una función cuyos ceros correspondan a la raíz buscada.

```math
    f(x)=\sqrt{x}\
```
```math
    y=\sqrt{x}\
```
Donde:
- x es el número cuya raíz cuadrada queremos calcular.
- y es la incógnita que buscamos (el resultado).

```math
    y^2 - x = 0
```

```math
    f(y) = y^2 - x
```

```math
    f'(y) = 2y
```

Con estas expresiones ya podemo emplear el método de Newton:

```math
y_{n+1}
=
y_n
-
\frac{f(y_n)}{f'(y_n)}
```

Definiremos una función recursiva para este cálculo, y debemos establecer una parada para dicha función. Establecemos una tolerancia (DIFF) y detenemos la iteración cuando el valor absoluto de $y²
− x$ es suficientemente pequeño. En ese punto consideramos que la aproximación es una raíz aceptable.

```schema

    (define (abs x)
        (if (< x 0)
            (- x)
            x))

    (define (fn y   x)
            (-  (*  y  y)
                x))

    (define (der_fn y)
        (*  2   y))

    (define (estimate   y    x)
            (-  y  
                (/  (fn y   x)
                    (der_fn y))))

    (define DIFF    0.01)

    (define (sqrt_1   x   avg_value)
            (if (< (abs  (- (*  avg_value   
                                avg_value)   
                            x))  
                    DIFF)
                avg_value
                (sqrt_1   x   (estimate   avg_value   x))))

    (define (sqrt   x)
            (sqrt_1 x   1.0))
                    
    (sqrt   2)

```

Nótese que usa como elemento pivote inicial el valor 1.0, esto es debido a que al usar un valor punto flotante, obtenemos el resultado en dicho tipo, sino obtendríamos fracciones.