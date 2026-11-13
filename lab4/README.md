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


## ¿Qué es una máquina de estados finitos?

Una **máquina de estados finitos**, conocida como FSM (*Finite State Machine*), es un sistema secuencial que controla el funcionamiento de un circuito mediante estados definidos.

Cada estado realiza una función específica y determina cuál será el siguiente estado.

En el laboratorio se implementó una FSM para desarrollar la multiplicación binaria.

A diferencia de la suma, que utiliza directamente un circuito combinacional, el multiplicador desarrollado realiza varias operaciones a través de diferentes ciclos de reloj.

Los estados utilizados fueron:

| **Estado** | **Función** |
|:---|:---|
| `START_STATE` | Espera la señal de inicio y carga los operandos |
| `CHECK` | Verifica el bit menos significativo del multiplicador |
| `ADD` | Realiza la suma parcial |
| `SHIFT` | Desplaza los operandos |
| `END_STATE` | Finaliza la multiplicación |

### Funcionamiento de la multiplicación

El algoritmo utilizado corresponde al método de **sumas y desplazamientos**.

Su funcionamiento consiste en examinar los bits del multiplicador.

Cuando el bit menos significativo es `1`, el multiplicando se suma al producto parcial.

Posteriormente:

* El multiplicando se desplaza una posición a la izquierda.
* El multiplicador se desplaza una posición a la derecha.
* El procedimiento continúa hasta completar la operación.

Por ejemplo:

```text
A = 9
B = 6

9 × 6 = 54

Resultado decimal = 54

Resultado binario = 00110110
```

La FSM permite controlar correctamente las distintas etapas de la multiplicación.

## ¿Qué es un banco de registros?

Un **banco de registros** es un conjunto de elementos de almacenamiento que permite guardar información binaria y recuperarla posteriormente.

Los registros son componentes fundamentales de los procesadores, debido a que permiten conservar temporalmente operandos, resultados e información de procesamiento.

En este laboratorio se implementó un banco compuesto por:

```text
8 registros de 8 bits
```

Por lo tanto, su capacidad total es:

```text
8 × 8 = 64 bits
```

Cada registro posee una dirección única representada mediante tres bits.

| **Dirección** | **Registro** |
|:---:|:---|
| `000` | Registro 0 |
| `001` | Registro 1 |
| `010` | Registro 2 |
| `011` | Registro 3 |
| `100` | Registro 4 |
| `101` | Registro 5 |
| `110` | Registro 6 |
| `111` | Registro 7 |

El banco permite ejecutar dos operaciones principales:

**Escritura (Write):** almacena un dato en la dirección seleccionada.

**Lectura (Read):** recupera el dato almacenado en una dirección.

Para realizar estas operaciones se utilizan señales de control y un decodificador de direcciones.
## ¿Qué es un multiplexor?

Un **multiplexor**, también conocido como MUX, es un circuito combinacional que permite seleccionar una entrada entre varias opciones disponibles.

La selección depende de un conjunto de señales de control.

En el laboratorio se implementó un multiplexor encargado de determinar qué dato será almacenado en el banco de registros.

Las opciones son:

| **sel_dato** | **Dato seleccionado** |
|:---:|:---|
| `00` | Operando A |
| `01` | Operando B |
| `10` | Resultado de la ALU |
| `11` | Valor cero |

Por ejemplo:

```text
A = 00000101
B = 00000011
R = 00001000

sel = 00 → 00000101
sel = 01 → 00000011
sel = 10 → 00001000
sel = 11 → 00000000
```

En el diseño, los operandos originales de cuatro bits se amplían a ocho bits mediante ceros en las posiciones más significativas.

Esto permite almacenar tanto los operandos como el resultado de la ALU utilizando un mismo bus de datos.

## ¿Qué es un decodificador?

Un **decodificador digital** es un circuito combinacional que recibe un código binario y activa una salida correspondiente a dicho código.

En este laboratorio se desarrolló un decodificador de **3 a 8 líneas**.

Sus tres entradas permiten seleccionar una de las ocho salidas disponibles.

```text
3 bits de entrada

        ↓

Decodificador 3 a 8

        ↓

8 señales de selección
```
El decodificador permite habilitar únicamente el registro que se desea modificar durante una operación de escritura.

## ¿Qué es UART?

La **UART** (*Universal Asynchronous Receiver/Transmitter*) es una interfaz de comunicación serial asíncrona utilizada para transmitir y recibir información entre dispositivos electrónicos.

La comunicación asíncrona no requiere que ambos dispositivos compartan una señal física de reloj.

En este proyecto se implementó un transmisor UART para enviar los datos almacenados en el banco de registros hacia un computador.

La configuración utilizada es:

```text
Baudios: 9600
Bits de datos: 8
Paridad: Ninguna
Bits de parada: 1
```

Esta configuración se conoce como **8N1**.

Cada trama enviada contiene:

1. Un bit de inicio.
2. Ocho bits de información.
3. Un bit de parada.

```text
START | D0 D1 D2 D3 D4 D5 D6 D7 | STOP
```

El bit de inicio corresponde a `0`, mientras que el bit de parada corresponde a `1`.

Los datos se transmiten comenzando por el bit menos significativo.

### Conversión de binario a ASCII

Los registros almacenan números en formato binario.

Sin embargo, para visualizarlos correctamente en una terminal como PuTTY es necesario convertir esos valores en caracteres ASCII.

Por esta razón se desarrolló el módulo `uart_dec_sender`.

Por ejemplo:

```text
Número almacenado: 54

Representación binaria:
00110110

Caracteres ASCII enviados:
'5' '4' CR LF
```

Los caracteres `CR` y `LF` permiten realizar un salto de línea después de transmitir el número.

## ¿Qué es un circuito antirrebote?

Un circuito **antirrebote** (*debounce*) evita que las variaciones eléctricas de un pulsador mecánico generen múltiples activaciones no deseadas.

Al presionar un botón pueden presentarse cambios rápidos entre los estados lógicos `0` y `1`.

Esto podría provocar varias escrituras o lecturas cuando realmente se desea ejecutar una sola.

Para evitarlo, se implementó un módulo que sincroniza la entrada, verifica su estabilidad y genera un pulso válido de un ciclo de reloj.

En la implementación de la FPGA se emplea un contador configurado para aproximadamente **20 ms** de estabilidad con un reloj de 50 MHz.

---
