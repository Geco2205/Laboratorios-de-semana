# Práctica en clase 6: CUDA (suma de vectores, producto punto y softmax)




## Salidas
 
```
$ ./vector_add 2097152
vector-add n=2097152: OK
 
$ ./dot_product 4194304
dot-product n=4194304: gpu=-0.500000 cpu=-0.500000 error=0.000000 OK
 
$ ./softmax 256 2048
softmax rows=256 cols=2048: OK
```


## Respuesta a las preguntas

### Ejercicio A: suma de vectores

**A1.** Con la fórmula de

```cpp
int blocks = (n + threads_per_block - 1) / threads_per_block;
```

teniendo N = 1048576 y 256 hilos por bloque, tenemos que el resultado es 4096 bloques.

**A2.** Si N no es múltiplo del tamaño de bloques, va a suceder que vamos a tener un último bloque en el que en su totalidad no se van a usar todos sus hilos, pero igual esos hilos se ejecutan. Esto es un problema porque en la GPU un cálculo de un hilo no válido puede provocar que sustituya memoria fuera de la reservada, otra variable que tengamos por ahí. La condición i < n evita que, a pesar de tener un N que no es múltiplo, se provoque este problema.

**A3.** a y b van a la GPU, c va a la CPU.
a y b van a la GPU para calcular el dato y guardarlo en c, y c va de la GPU a la CPU para guardarlo ahí.

### Ejercicio B: producto punto

**B1.** Debido a que el producto punto consiste en la suma total de cada uno de los valores del vector A por el vector B, si cada hilo escribe su producto en una posición propia, se obtiene una cantidad N de productos pero no la suma.
Si manejamos que la GPU, hiciera tambien la suma del producto punto en cada uno de los hilos, causaría un problema que es que, como todos lo tratarían de hacer a la misma vez, es como que intentaran hacer la suma cuando esta vale 0, uno intenta hacer valorA + 0 y otro hilo ValorB + 0, intentando guardarlo en la misma variable, el resultado es el último que escribe, sustituyendo el anterior y perdiendo el total
Lo que hace el ejercicio es una reducción en cada bloque, para pasarle solo las sumas parciales al CPU, para que este las sume ahí.

**B2.** Los valores parciales que se terminan copiando de la GPU a la CPU es el mismo al número de bloques debido a que cada bloque termina aportando una suma parcial.
Para N= 1048576, se estarían copiando 4096 valores parciales y para N = 4194304  se estarían copiando 16384 valores parciales

**B3.** La reducción al final depende del resultado de todos los productos de ese bloque, y cada paso de la reducción depende de lo que se calculó en el paso anterior. Si no existiera la sincronización, un hilo podría leer un valor de `cache` que otro hilo todavía no ha terminado de escribir, porque hay varios hilos que no han terminado y otros sí. Entonces la reducción no tendría todos los valores del bloque y el resultado sería incorrecto. Además, qué hilos terminan primero depende del orden en que la GPU ejecuta los warps, y ese orden cambia en cada ejecución, por lo que el resultado no solo es incorrecto sino que es diferente cada vez.

### Ejercicio C: softmax

**C1.** Como se está trabajando con exponenciales, podemos llegar a tener el problema de que si es una fila con valores grandes, el resultado del exponencial puede llegar a ser infinito porque se sale del valor permitido por float, y cuando se realiza la suma también daría infinito, lo que provocaría que al sacar el porcentaje, estemos dividiendo infinito entre infinito, provocando un valor indefinido.
Se calcula el máximo de cada fila con el objetivo de disminuir el valor del exponencial para que sean valores que se mantengan en el rango del float. Restar el máximo no cambia el resultado, ya que el factor e^(−m) aparece tanto en el numerador como en el denominador y se cancela, por lo que el softmax solo depende de las diferencias entre los valores de la fila.

**C2.** Hay 2 reducciones que dependen de los hilos: una es la reducción del máximo de la fila y la otra es la reducción de la suma de exponenciales de la fila.
Son independientes los cálculos de cada una de las exponenciales y las normalizaciones, pero estas también dependen de las reducciones, siendo estos row_max y row_sum.

**C3.** Usando solo un bloque por fila cuando cols crece mucho, teniendo en cuenta que el limite de hilos es 1024, provoca que cada hilo llegue recorriendo una cantidad elevada de elementos, a mayor cols, mayor cantidad de elementos.
Esto también provoca un desaprovechamiento en el SM, si cada fila que es un bloque equivale a un SM, tomando en cuenta que mi GPU tiene  46 SM, se estaría desaprovechando un porcentaje del GPU debido a la limitante de una fila por bloque.



## Uso de IA

Nota sobre utilización de herramientas de IA

Se hace uso de herramientas de IA como apoyo para comprender conceptos, generar ideas y mejorar la redacción de la documentación. La implementación, la validación y los resultados son responsabilidad del estudiante, quien asume la responsabilidad por el uso indebido o no descrito anteriormente.

Se adjuntan los enlaces compartidos de las conversaciones como evidencia.

https://claude.ai/share/4afcf91c-0d73-417d-83b3-c70e35df5757

**Autor:** Gerson Adrián Cordero Zúñiga 

**Curso:** Introducción a la Computación Heterogénea

**Profesor:** Luis Gerardo León Vega