# Lab04 - ALU, Banco de Registros y Comunicación UART

## Integrantes

* [Juan Diego Cervantes Guio](https://github.com/juandicervantesgu-dev)
* [Juan Sebastian Guerrero Gualteros](https://github.com/juanseguerrerogu07)
* [Samuel Esteban Jaime Gutierrez](https://github.com/Samueljgest)

**Asignatura:** Arquitectura de Procesadores  
**Universidad:** ECCI  
**Lenguaje de programación:** Verilog HDL  
**Dispositivo:** FPGA MAX 10  

### Índice

1. [Fundamentos teóricos](#fundamentos-teóricos)
2. [Documentación del diseño](#documentación-del-diseño)
3. [Diagramas](#diagramas)
4. [Evidencias de implementación](#evidencias-de-implementación)
5. [Conclusiones](#conclusiones)
6. [Referencias](#referencias)

---
## ¿Qué es una ALU?

La **Unidad Aritmético-Lógica (ALU)**, conocida en inglés como *Arithmetic Logic Unit*, es uno de los componentes fundamentales de un procesador digital.

Su función principal consiste en realizar operaciones aritméticas y lógicas sobre datos binarios.

Entre las operaciones que puede ejecutar encontramos:

* Suma.
* Resta.
* Multiplicación.
* Comparación.
* Desplazamientos binarios.
* Operaciones lógicas NOT, NOR y XOR.

En este laboratorio se implementó una ALU que recibe **dos operandos de 4 bits**, identificados como `A` y `B`, y genera un resultado de hasta **8 bits**.

La operación se selecciona mediante una señal denominada `opcode`.

```text
       A[3:0] ────────┐
                      │
       B[3:0] ────────┤
                      ▼
                 ┌─────────┐
       opcode ──►│   ALU   │
                 └─────────┘
                      │
                      ▼
                result[7:0]
```

Esta estructura representa una parte importante de la ruta de datos de un procesador.

- - - - -
## ¿Qué es un opcode?

Un **opcode** (*Operation Code*) es un código binario que indica qué operación debe ejecutar la ALU.

En el diseño desarrollado se utilizan **3 bits** para representar el código de operación.

Por lo tanto:

```text
2³ = 8 combinaciones posibles
```

Cada combinación selecciona una función diferente.

| **Opcode** | **Operación** | **Descripción** |
|:---:|:---|:---|
| `000` | Suma | A + B + carry_in |
| `001` | Resta | A - B |
| `010` | Multiplicación | A × B |
| `011` | Comparación | Selecciona el mayor entre A y B |
| `100` | Desplazamiento | Desplaza A un bit a la izquierda |
| `101` | NOT | Invierte los bits de A |
| `110` | NOR | Niega la operación OR entre A y B |
| `111` | XOR | Realiza OR exclusivo entre A y B |

Por ejemplo:

```text
A = 9
B = 6

opcode = 000

Operación: SUMA

9 + 6 = 15
```

Al modificar únicamente el `opcode`, la ALU puede ejecutar una operación diferente sin necesidad de modificar los operandos.

## ¿Qué es un sumador binario?

Un **sumador binario** es un circuito combinacional encargado de realizar la suma de dos números representados en sistema binario.

En este laboratorio se utilizó un sumador completo de un bit como elemento básico.

Este circuito posee tres entradas:

* `A`: primer operando.
* `B`: segundo operando.
* `Ci`: acarreo de entrada.

Y dos salidas:

* `S`: resultado de la suma.
* `Co`: acarreo de salida.

Las expresiones lógicas correspondientes son:

```text
S = A XOR B XOR Ci

Co = (A AND B) OR (A AND Ci) OR (B AND Ci)
```

Para construir un sumador de cuatro bits se conectan cuatro sumadores completos.

El acarreo de cada etapa se utiliza como entrada de la siguiente.

Esta estructura recibe el nombre de **sumador de acarreo en cascada**.

## ¿Cómo se realiza la resta binaria?

La resta puede implementarse utilizando el mismo circuito sumador mediante el **complemento a dos**.

La expresión matemática es:

```text
A - B = A + (~B) + 1
```

Donde:

* `~B`: complemento a uno de B.
* `+1`: permite obtener el complemento a dos.

Para realizar ambas operaciones se utiliza una señal de selección.

| **sel** | **Operación** | **Operando B** | **Acarreo inicial** |
|:---:|:---|:---|:---:|
| `0` | Suma | B | carry_in |
| `1` | Resta | NOT B | 1 |

De esta manera, la misma estructura puede ejecutar sumas y restas.