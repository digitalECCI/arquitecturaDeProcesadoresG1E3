# Lab01 - Sumador/Restador de 4 bits

## Integrantes

* [Juan Diego Cervantes Guio](https://github.com/juandicervantesgu-dev)
* [Juan Sebastian Guerrero Gualteros](https://github.com/juanseguerrerogu07)
* [Samuel Esteban Jaime Gutierrez](https://github.com/Samueljgest) 


### Índice

1. [Fundamentos teóricos](#fundamentos-teóricos)
2. [Documentación del diseño](#documentación-del-diseño)
3. [Simulaciones](#simulaciones)
4. [Evidencias de implementación](#evidencias-de-implementación)
5. [Conclusiones](#conclusiones)
6. [Referencias](#referencias)
# Fundamentos teóricos

## ¿Qué es un bit?

Un **bit** (*Binary Digit*) es la unidad más pequeña de información utilizada en los sistemas digitales.

Un bit solamente puede tomar dos valores:

* `0` → Estado lógico bajo.
* `1` → Estado lógico alto.

Al combinar varios bits podemos representar una mayor cantidad de números.

Por ejemplo, con **4 bits** tenemos:

```text
0000 → 0
0001 → 1
0010 → 2
...
1111 → 15
```

En total existen:

```text
2^4 = 16 combinaciones
```

## ¿Qué es un byte?

Un **byte** corresponde a un conjunto de **8 bits**.

```text
1 Byte = 8 bits
```

También podemos encontrar:

* *4 bits* = 1 nibble.
* *8 bits* = 1 byte.
* *16 bits* = 2 bytes.
* *32 bits* = 4 bytes.

En este laboratorio trabajamos principalmente con números de **4 bits**.
Ahora se abordara sobre el sistema binario, factor muy importante para la descripción e implementación de hardware en Quartus.

Únicamente utiliza:

```text
0 y 1
```

Cada posición tiene un valor correspondiente a una potencia de 2.

Por ejemplo:
| **Concepto** | **Bit 3** | **Bit 2** | **Bit 1** | **Bit 0** |
|:---:|:---:|:---:|:---:|:---:|
| **Binario** | 1 | 0 | 1 | 1 |
| **Posición** | 3 | 2 | 1 | 0 |
| **Valor** | 8 | 4 | 2 | 1 |
Entonces:

```text
1011₂ = 8 + 0 + 2 + 1

1011₂ = 11₁₀
```

## Suma binaria

La **suma binaria** funciona de manera similar a la suma decimal, pero solamente utiliza `0` y `1`.

Las operaciones básicas son:
| **A** | **B** | **Suma** | **Acarreo** |
|:-----:|:-----:|:--------:|:-----------:|
|   0   |   0   |    0     |      0      |
|   0   |   1   |    1     |      0      |
|   1   |   0   |    1     |      0      |
|   1   |   1   |    0     |      1      |

La operación más importante es:

```text
1 + 1 = 10₂
```

El `0` corresponde al resultado de la posición actual y el `1` corresponde al **acarreo**.

## ¿Qué es el acarreo?

El **acarreo** (*Carry*) aparece cuando el resultado de una suma necesita un bit adicional.

Por ejemplo:

```text
  1
+ 1
---
 10
```

En el laboratorio utilizamos:

* `Ci` → **Carry In** → Acarreo de entrada.
* `Co` → **Carry Out** → Acarreo de salida.

---

# Documentación del diseño

## 1. Sumador de 1 bit

### 1.1 Descripción

El **sumador completo de 1 bit** permite sumar tres valores:

```text
A + B + Ci
```

Sus entradas son:

* `A` → Primer bit.
* `B` → Segundo bit.
* `Ci` → Acarreo de entrada.

Sus salidas son:

* `S` → Resultado de la suma.
* `Co` → Acarreo de salida.

La expresión correspondiente a la suma es:

```text
S = A XOR B XOR Ci
```

### 1.2 Código Verilog

El circuito fue desarrollado utilizando **primitivas de Verilog**, específicamente compuertas `XOR`, `AND` y `OR`.

```verilog
module sumador1b(
    input A,
    input B,
    input Ci,
    output S,
    output Co
);

    wire res;
    wire res2;
    wire res3;
    wire res4;

    xor (res, B, Ci);
    xor (S, res, A);

    and (res2, A, Ci);
    or  (res3, A, Ci);
    and (res4, B, res3);
    or  (Co, res4, res2);

endmodule
```

### 1.3 Diagrama