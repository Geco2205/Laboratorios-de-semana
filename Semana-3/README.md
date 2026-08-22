# Lab 2 - EVALUACION DEL RENDIMIENTO Y LA EFICIENCIA

## Contenido de la carpeta

    -Makefile
    -matmul_avx2.c
    -matmul_scalar.c

## Datos obtenidos con una repeteción

### Multiplicación Vectorial

```bash
    Tamano matriz: 2048x2048
    Repeticiones: 1
    Checksum: 86972906452.000000
    C[0][0]: 26800.500000
    C[1023][1023]: 26836.500000
    Operaciones: 17179869184
    Tiempo: 1.495809 segundos
    Rendimiento: 11.485334 GFLOP/s
```

### Multiplicación Escalar

```bash
    Tamano matriz: 2048x2048
    Repeticiones: 1
    Checksum: 86972906452.000000
    C[0][0]: 26800.500000
    C[1023][1023]: 26836.500000
    Operaciones: 17179869184
    Tiempo: 3.225790 segundos
    Rendimiento: 5.325786 GFLOP/s
```

### Speedup

```bash
    3.225790 / 1.495809 ≈ 2.16x
```


## Discusión de resultados

Realizando una relación entre ambos tiempos para observar cómo fue la optimización con el proceso vectorizado, el resultado da que el proceso vectorizado es 2.16 veces más rápido que el proceso sin vectorizar.
Este resultado difiere del resultado ideal de optimización que debería ser de unas 8 veces más rápido. Esto se puede explicar por medio de la Ley de Amdahl donde a pesar que nuestro objetivo era optimizar 8 veces más el proceso, esto no es posible debido al porcentaje del código que es paralelizable y serial.
Comparando el resultado con la gráfica de Ley de Amdahl, es posible observar al encontrarnos con una aceleración de 2 y con 8 procesadores, es posible concluir que nuestro código presenta aproximadamente un valor por arriba del 50% y por debajo de 75% en paralelo. El resto del porcentaje es nuestra limitante para no alcanzar la optimización ideal, la forma de corregir esto sería paralelizar en lo que se pueda, las partes seriales del código.

**Autor:** Gerson Adrián Cordero Zúñiga 

**Curso:** Introducción a la Computación Heterogénea

**Profesor:** Luis Gerardo León Vega