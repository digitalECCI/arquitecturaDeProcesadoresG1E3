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
# Documentación del diseño

## 1. Organización general del sistema

### 1.1 Descripción

El proyecto se desarrolló mediante una estructura modular en Verilog HDL.

El módulo principal es `top_fpga`, encargado de integrar los distintos componentes y establecer la conexión con las entradas y salidas físicas de la FPGA.

El funcionamiento general comprende las siguientes etapas:

```text
ENTRADAS A Y B
      |
      v
     ALU
      |
      v
SELECTOR DE DATOS
      |
      v
BANCO DE REGISTROS
      |
      v
CONVERSOR DECIMAL
      |
      v
TRANSMISOR UART
      |
      v
COMPUTADOR - PuTTY
```

Cada bloque cumple una función específica dentro del sistema.

La ALU procesa los operandos, el multiplexor selecciona la información, el banco la almacena y la UART permite enviarla al computador.

### 1.2 Señales de entrada y salida

| **Señal** | **Función** |
|:---|:---|
| `MAX10_CLK1_50` | Reloj principal de 50 MHz |
| `KEY[0]` | Reinicio del sistema |
| `KEY[1]` | Inicio de la multiplicación |
| `SW[3:0]` | Operando A |
| `SW[7:4]` | Operando B |
| `GPIO_IN[2:0]` | Selección del opcode |
| `GPIO_IN[3]` | Acarreo de entrada |
| `GPIO_IN[5:4]` | Selección del dato |
| `GPIO_IN[8:6]` | Dirección de lectura y escritura |
| `GPIO_IN[12]` | Control de escritura |
| `GPIO_IN[13]` | Control de lectura |
| `LEDR[7:0]` | Resultado de la ALU |
| `LEDR[8]` | Multiplicación finalizada |
| `LEDR[9]` | UART ocupada |
| `GPIO_TX` | Transmisión serial |

---

## 2. Sumador de 1 bit

### 2.1 Descripción

El módulo `sumador1b` permite sumar tres entradas binarias y generar una salida de suma junto con el acarreo correspondiente.

Este módulo constituye la unidad básica para desarrollar el sumador de cuatro bits.

### 2.2 Código Verilog

```verilog
module sumador1b(
    input  A,
    input  B,
    input  Ci,
    output S,
    output Co
);

    wire w1;
    wire w2;
    wire w3;

    xor (S, A, B, Ci);

    and (w1, A, B);
    and (w2, A, Ci);
    and (w3, B, Ci);

    or (Co, w1, w2, w3);

endmodule
```

### 2.3 Funcionamiento

El código utiliza primitivas de Verilog:

* `xor`: calcula el resultado de la suma.
* `and`: genera los productos lógicos del acarreo.
* `or`: combina las condiciones que producen el acarreo de salida.

La utilización de primitivas permite representar directamente las compuertas que conforman el circuito.

---

## 3. Sumador de 4 bits

### 3.1 Descripción

Para construir el sumador de cuatro bits se realizó la instanciación de cuatro módulos `sumador1b`.

El acarreo generado por una etapa es utilizado como entrada de la siguiente.

### 3.2 Código Verilog

```verilog
module sumador4b(
    input  [3:0] A,
    input  [3:0] B,
    input        Ci,
    output [3:0] So,
    output       Co
);

    wire C1;
    wire C2;
    wire C3;

    sumador1b FA0(
        .A(A[0]),
        .B(B[0]),
        .Ci(Ci),
        .S(So[0]),
        .Co(C1)
    );

    sumador1b FA1(
        .A(A[1]),
        .B(B[1]),
        .Ci(C1),
        .S(So[1]),
        .Co(C2)
    );

    sumador1b FA2(
        .A(A[2]),
        .B(B[2]),
        .Ci(C2),
        .S(So[2]),
        .Co(C3)
    );

    sumador1b FA3(
        .A(A[3]),
        .B(B[3]),
        .Ci(C3),
        .S(So[3]),
        .Co(Co)
    );

endmodule
```

### 3.3 Funcionamiento

Cada sumador completo procesa un bit de los operandos.

La operación inicia desde el bit menos significativo y los acarreos se propagan sucesivamente.

De esta manera se obtiene un resultado binario de cuatro bits y un acarreo final.

---

## 4. Sumador/Restador de 4 bits

### 4.1 Descripción

El módulo `sumador_restador4b` permite realizar operaciones de suma y resta utilizando un único circuito sumador.

La señal `sel` determina cuál operación debe ejecutarse.

### 4.2 Código Verilog

```verilog
module sumador_restador4b(
    input  [3:0] A,
    input  [3:0] B,
    input        sel,
    input        carry_in,

    output [3:0] So,
    output       Co
);

    wire [3:0] B_xor;
    wire Ci_effective;

    assign Ci_effective = sel ? 1'b1 : carry_in;

    xor (B_xor[0], B[0], sel);
    xor (B_xor[1], B[1], sel);
    xor (B_xor[2], B[2], sel);
    xor (B_xor[3], B[3], sel);

    sumador4b SR(
        .A(A),
        .B(B_xor),
        .Ci(Ci_effective),
        .So(So),
        .Co(Co)
    );

endmodule
```

### 4.3 Selección de operación

Cuando:

```text
sel = 0
```

El circuito realiza una suma y utiliza el valor de `carry_in`.

Cuando:

```text
sel = 1
```

El circuito invierte los bits del segundo operando y utiliza un acarreo inicial igual a uno.

Esto permite ejecutar la resta mediante complemento a dos.

---

## 5. Unidad Aritmético-Lógica

### 5.1 Descripción

El módulo `top_ALU` integra las operaciones aritméticas, lógicas y de comparación.

Recibe los operandos `A` y `B`, el código de operación y las señales de control necesarias.

La ALU utiliza diferentes bloques internos y selecciona el resultado correspondiente mediante una estructura `case`.

### 5.2 Código Verilog

La selección de operaciones se implementó mediante el siguiente fragmento:

```verilog
always @(*) begin
    case (opcode)

        3'b000:
            result = {4'b0000, arith_result};

        3'b001:
            result = {4'b0000, arith_result};

        3'b010:
            result = mult_result;

        3'b011:
            result = {4'b0000, compare_result};

        3'b100:
            result = {3'b000, A, 1'b0};

        3'b101:
            result = {4'b0000, not_result};

        3'b110:
            result = {4'b0000, nor_result};

        3'b111:
            result = {4'b0000, xor_result};

        default:
            result = 8'b0;

    endcase
end
```

### 5.3 Indicadores de estado

Adicionalmente, se implementaron señales para identificar condiciones de los resultados.

| **Señal** | **Función** |
|:---|:---|
| `zero` | Indica si el resultado es cero |
| `carry_out` | Indica el acarreo aritmético |
| `overflow` | Indica desbordamiento con signo |
| `negative` | Indica el bit de signo definido por la operación |
| `mult_done` | Indica la finalización de la multiplicación |

Estas señales permiten interpretar diferentes condiciones producidas durante el procesamiento.

---

## 6. Multiplicador secuencial mediante FSM

### 6.1 Descripción

El módulo `mult` realiza una multiplicación binaria entre dos operandos de cuatro bits.

Para ello se utiliza una máquina de estados que controla las sumas parciales y los desplazamientos.

Se emplean los siguientes registros:

* `A`: multiplicando extendido a ocho bits.
* `B`: multiplicador de cuatro bits.
* `pp`: producto parcial de ocho bits.
* `state`: estado actual de la máquina.

### 6.2 Definición de estados

```verilog
localparam START_STATE = 3'b000;
localparam CHECK       = 3'b001;
localparam ADD         = 3'b010;
localparam SHIFT       = 3'b011;
localparam END_STATE   = 3'b100;
```

### 6.3 Funcionamiento de la FSM

**Estado START_STATE**

Espera la señal `start`.

Cuando recibe esta señal, inicializa el producto parcial y carga los operandos.

```verilog
if (start) begin
    pp <= 8'b00000000;

    A <= {4'b0000, MD};
    B <= MR;

    if (MR == 4'b0000)
        state <= END_STATE;
    else
        state <= CHECK;
end
```

**Estado CHECK**

Comprueba el bit menos significativo del multiplicador.

```verilog
if (B[0] == 1'b1)
    state <= ADD;
else
    state <= SHIFT;
```

**Estado ADD**

Realiza la suma correspondiente al producto parcial.

```verilog
pp <= pp + A;

state <= SHIFT;
```

**Estado SHIFT**

Realiza los desplazamientos necesarios para continuar con el algoritmo.

```verilog
A <= A << 1;
B <= B_next;

if (B_next == 4'b0000)
    state <= END_STATE;
else
    state <= CHECK;
```

**Estado END_STATE**

Indica que la operación terminó.

```verilog
done <= 1'b1;

state <= START_STATE;
```

### 6.4 Importancia de la máquina de estados

El uso de una FSM permite dividir la multiplicación en operaciones simples.

Mientras que un sumador combinacional calcula directamente la suma, el multiplicador utiliza varios ciclos del reloj para completar el procedimiento.

Esta implementación permite comprender cómo una unidad de control puede administrar las operaciones secuenciales de un procesador.

---

## 7. Selector de datos

### 7.1 Descripción

El módulo `selector_datos` determina cuál de las entradas disponibles será almacenada en el banco de registros.

Se implementó un multiplexor de cuatro opciones controlado mediante dos bits.

### 7.2 Código Verilog

```verilog
module selector_datos (
    input [3:0] A,
    input [3:0] B,
    input [7:0] resultado,
    input [1:0] sel,
    output reg [7:0] data_in
);

    always @(*) begin
        case (sel)

            2'b00:
                data_in = {4'b0000, A};

            2'b01:
                data_in = {4'b0000, B};

            2'b10:
                data_in = resultado;

            2'b11:
                data_in = 8'b0;

            default:
                data_in = 8'b0;

        endcase
    end

endmodule
```

### 7.3 Funcionamiento

El selector recibe los operandos A, B y el resultado completo de la ALU.

La señal `sel` selecciona el dato que será entregado al banco de registros.

Esto permite almacenar individualmente los operandos o conservar el resultado de una operación.

---

## 8. Banco de registros

### 8.1 Descripción

El módulo `banco_registros` está compuesto por ocho registros de ocho bits.

La escritura se realiza mediante un decodificador que habilita el registro correspondiente a la dirección seleccionada.

La lectura utiliza una estructura `case` para entregar el contenido del registro solicitado.

### 8.2 Decodificador de 3 a 8

El decodificador se implementó utilizando primitivas de Verilog.

```verilog
module decodificador3a8(
    input        enable,
    input  [2:0] addr,
    output [7:0] select
);

    wire n0;
    wire n1;
    wire n2;

    not (n0, addr[0]);
    not (n1, addr[1]);
    not (n2, addr[2]);

    and (select[0], enable, n2, n1, n0);
    and (select[1], enable, n2, n1, addr[0]);
    and (select[2], enable, n2, addr[1], n0);
    and (select[3], enable, n2, addr[1], addr[0]);
    and (select[4], enable, addr[2], n1, n0);
    and (select[5], enable, addr[2], n1, addr[0]);
    and (select[6], enable, addr[2], addr[1], n0);
    and (select[7], enable, addr[2], addr[1], addr[0]);

endmodule
```

### 8.3 Instanciación de registros

Para almacenar los datos se utilizaron ocho instancias del módulo `registro8`.

Ejemplo:

```verilog
registro8 R0 (
    .clk(clk),
    .rst(rst),
    .write_en(write_select[0]),
    .data_in(data_in),
    .data_out(reg0)
);

registro8 R1 (
    .clk(clk),
    .rst(rst),
    .write_en(write_select[1]),
    .data_in(data_in),
    .data_out(reg1)
);
```

La estructura continúa de manera equivalente hasta completar los ocho registros.

### 8.4 Lectura de los registros

```verilog
always @(*) begin
    data_out = 8'b0;

    if (read_en) begin
        case (addr_read)

            3'b000: data_out = reg0;
            3'b001: data_out = reg1;
            3'b010: data_out = reg2;
            3'b011: data_out = reg3;
            3'b100: data_out = reg4;
            3'b101: data_out = reg5;
            3'b110: data_out = reg6;
            3'b111: data_out = reg7;

            default: data_out = 8'b0;

        endcase
    end
end
```

### 8.5 Funcionamiento del banco

Para escribir un dato es necesario seleccionar:

1. El dato mediante el multiplexor.
2. La dirección mediante `addr_write`.
3. La habilitación de escritura.

El valor se almacena en el registro seleccionado.

Para leerlo, se selecciona la dirección correspondiente y se activa `read_en`.

El dato recuperado se entrega al siguiente bloque del sistema.

**Nota:** El módulo `registro8` se instancia en el diseño recibido, pero su definición no aparece entre los códigos compartidos. Para completar los archivos fuente del proyecto debe incluirse su implementación original.

---

## 9. Transmisor UART

### 9.1 Descripción

El módulo `uart_tx` permite transmitir información serial hacia un computador o dispositivo externo.

Se utiliza una máquina de estados para controlar las diferentes etapas de transmisión.

La frecuencia del reloj principal es de 50 MHz y la velocidad configurada para la FPGA es de 9600 baudios.

### 9.2 Definición de estados

```verilog
localparam IDLE  = 2'd0;
localparam START = 2'd1;
localparam DATA  = 2'd2;
localparam STOP  = 2'd3;
```

### 9.3 Funcionamiento

**IDLE**

El transmisor permanece en reposo y espera una solicitud de envío.

**START**

Se genera el bit de inicio de la trama UART.

**DATA**

Se transmiten los ocho bits correspondientes al dato.

**STOP**

Se genera el bit de parada y finaliza la transmisión.
### 9.4 Fragmento del código

```verilog
localparam integer DIVISOR = CLOCK_HZ / BAUD;

reg [1:0] state;
reg [31:0] baud_count;
reg [2:0] bit_index;
reg [7:0] shift_reg;
```

Para transmitir cada bit se utiliza el registro de desplazamiento:

```verilog
tx <= shift_reg[0];

shift_reg <= {1'b0, shift_reg[7:1]};
```

Este procedimiento permite transmitir los bits comenzando por el menos significativo.

La señal `busy` indica que el módulo está transmitiendo.

Al finalizar cada trama se activa `tx_done`.

---

## 10. Conversor decimal UART

### 10.1 Descripción

El módulo `uart_dec_sender` convierte los números binarios almacenados en el banco de registros a caracteres decimales ASCII.

Esto permite que los valores sean visualizados correctamente en la terminal PuTTY.

La conversión contempla números entre 0 y 255.

### 10.2 Código Verilog

```verilog
wire [7:0] h = data / 8'd100;
wire [7:0] t = (data / 8'd10) % 8'd10;
wire [7:0] o = data % 8'd10;
```

Las variables representan:

* `h`: centenas.
* `t`: decenas.
* `o`: unidades.

Posteriormente se realiza la conversión a ASCII.

```verilog
msg[0] <= 8'd48 + h;
msg[1] <= 8'd48 + t;
msg[2] <= 8'd48 + o;

msg[3] <= 8'h0D;
msg[4] <= 8'h0A;
```

### 10.3 Ejemplo

Si el valor almacenado es:

```text
Valor decimal = 125
```

El módulo obtiene:

```text
Centenas = 1
Decenas  = 2
Unidades = 5
```

Posteriormente transmite:

```text
'1' '2' '5' CR LF
```

La terminal puede mostrar:

```text
125
```

Además, el circuito elimina los ceros innecesarios a la izquierda.

Por ejemplo:

```text
Dato = 7

Se transmite "7"

No se transmite "007"
```

---

## 11. Circuito antirrebote

### 11.1 Descripción

El módulo `antirebote` permite eliminar las transiciones no deseadas generadas por los pulsadores físicos.

Se utiliza para controlar:

* Inicio de la multiplicación.
* Escritura en el banco de registros.
* Lectura de los registros.

Cada pulsación válida genera un único pulso de control.

### 11.2 Fragmento del código Verilog

```verilog
parameter MAX_COUNT = 1_000_000;

reg button_sync1;
reg button_sync2;
reg button_state;

reg [19:0] counter;
```

El circuito utiliza dos registros de sincronización y un contador.

Cuando la entrada cambia, el contador verifica que el nuevo estado permanezca estable.

Una vez comprobada la transición, el sistema genera un pulso válido.

### 11.3 Importancia

La implementación de este módulo permite evitar múltiples operaciones no deseadas.

Esto resulta especialmente importante para el banco de registros, debido a que un rebote podría generar varias solicitudes de escritura o lectura.

---

## 12. Módulo principal top_fpga

### 12.1 Descripción

El módulo `top_fpga` se encarga de conectar todos los componentes del sistema con los dispositivos físicos de la tarjeta FPGA.

Su función es integrar:

* Las señales de entrada.
* El procesamiento de la ALU.
* El banco de registros.
* Los antirrebotes.
* La conversión decimal.
* El transmisor UART.
* Las señales de visualización LED.

### 12.2 Asignación de operandos y controles

```verilog
wire clk = MAX10_CLK1_50;
wire rst_n = KEY[0];

wire [3:0] A = SW[3:0];
wire [3:0] B = SW[7:4];

wire [2:0] opcode = GPIO_IN[2:0];

wire carry_in = GPIO_IN[3];

wire [1:0] sel_dato = GPIO_IN[5:4];

wire [2:0] addr_write = GPIO_IN[8:6];
wire [2:0] addr_read  = GPIO_IN[8:6];
```

### 12.3 Salidas LED

```verilog
assign LEDR[7:0] = result;

assign LEDR[8] = mult_done;

assign LEDR[9] = uart_busy;
```

Los primeros ocho LED muestran el resultado de la operación seleccionada.

El LED 8 permite identificar la finalización de la multiplicación.

El LED 9 indica cuándo el transmisor UART se encuentra ocupado.

### 12.4 Integración del procesador

```verilog
top_procesador PROC (
    .clk(clk),
    .rst_n(rst_n),
    .start(start_pulse),
    .write_n(write_n),

    .A(A),
    .B(B),
    .carry_in(carry_in),
    .opcode(opcode),

    .sel_dato(sel_dato),
    .addr_write(addr_write),
    .addr_read(addr_read),
    .read_en(read_pulse),

    .result(result),
    .data_read(data_read),

    .zero(zero),
    .carry_out(carry_out),
    .overflow(overflow),
    .negative(negative),
    .mult_done(mult_done)
);
```

De esta manera se establece la comunicación entre la ALU, el selector de datos y el banco de registros.

---

## 13. Testbench

### 13.1 Descripción

Para verificar el comportamiento del sistema se desarrolló el módulo `tb_top_fpga`.

El testbench permite aplicar entradas de prueba, generar señales de reloj y observar las respuestas del circuito.

Además, incorpora un receptor UART simulado y un modelo de referencia para comparar los datos almacenados con los valores transmitidos.

### 13.2 Configuración

```verilog
localparam integer CLOCK_HZ = 50_000_000;

localparam integer BAUD = 1_000_000;

always #10 clk = ~clk;
```

El reloj tiene un periodo de:

```text
T = 20 ns
```

Su frecuencia es:

```text
f = 50 MHz
```

Para reducir el tiempo de simulación se aumentó temporalmente la velocidad UART.

En la implementación física se conserva la configuración de 9600 baudios.

### 13.3 Pruebas implementadas

| **Prueba** | **Descripción** |
|:---:|:---|
| 1 | Verificación después del reset |
| 2 | Selección de A, B y resultado |
| 3 | Escritura en las ocho direcciones |
| 4 | Sobrescritura de registros |
| 5 | Ejecución de los ocho opcodes |
| 6 | Verificación del antirrebote de escritura |
| 7 | Rechazo de pulsos demasiado cortos |
| 8 | Verificación del antirrebote de lectura |
| 9 | Lectura mientras UART está ocupada |
| 10 | Reset durante una transmisión |

Estas pruebas permiten evaluar diferentes condiciones del funcionamiento del sistema.

Es importante señalar que el testbench comprueba principalmente el almacenamiento y la transmisión de los resultados. Aunque recorre los ocho opcodes, no compara de manera independiente todas las operaciones de la ALU contra valores esperados fijos.

---
# Diagramas

## 1. Diagrama general del sistema

En el siguiente diagrama se presenta la organización de los bloques principales utilizados durante el laboratorio.

![Diagrama general del sistema](imagenes/diagrama_bloques.png)

**Figura 1.** Diagrama general de la ALU, selector de datos, banco de registros y comunicación UART.

La ALU recibe los operandos A y B, ejecuta la operación seleccionada y entrega el resultado al multiplexor.

Posteriormente, el selector determina qué dato será almacenado en el banco.

Finalmente, la información puede recuperarse y enviarse al computador mediante UART.

---

## 2. Arquitectura presentada durante la clase

A continuación, se presenta el esquema desarrollado durante la explicación del funcionamiento general del laboratorio.

![Arquitectura del sistema](imagenes/pizarra_arquitectura.jpg)

**Figura 2.** Esquema de integración de la ALU, banco de registros y transmisión UART.

En este diagrama se observa cómo los diferentes módulos se conectan para conformar una estructura de procesamiento y almacenamiento.

También se presenta la conexión de la FPGA con el computador mediante un adaptador USB-UART CP2102.

Para realizar esta conexión se emplean las señales de transmisión y tierra común, verificando los niveles eléctricos y pines correspondientes.

---

## 3. Diagrama del banco de registros

![Banco de registros](imagenes/pizarra_banco_registros.jpg)

**Figura 3.** Organización del multiplexor y banco de ocho registros.

El esquema representa el almacenamiento de datos mediante direcciones de tres bits.

También se identifican las señales de escritura y lectura, encargadas de controlar el acceso a cada registro.

---

## 4. Diagrama del multiplexor

![Multiplexor](imagenes/pizarra_selector.jpg)

**Figura 4.** Selección de los operandos y del resultado mediante el multiplexor.

Se observa cómo una señal de dos bits permite seleccionar cuál de los valores disponibles será enviado al banco de registros.

---

# Evidencias de implementación

## 1. Simulación de la ALU

A continuación, se presenta la evidencia de simulación obtenida durante el desarrollo del laboratorio.

![Simulación ALU](imagenes/simulacion_alu_uart.jpg)

**Figura 5.** Simulación del módulo principal `top_fpga`.

En la captura se observan los siguientes valores:

```text
A = 1010₂ = 10₁₀

B = 0101₂ = 5₁₀

opcode = 000

carry_in = 0

result = 00001111₂
```

La operación correspondiente es:

```text
      1010
    + 0101
    -------
      1111
```

Por lo tanto:

```text
10 + 5 = 15
```

El resultado hexadecimal observado es:

```text
0F
```

Lo anterior permite comprobar que la operación de suma mostrada en la captura genera el valor correcto.

Adicionalmente, se observa actividad en las señales relacionadas con la comunicación UART.

La señal `uart_busy` cambia a estado bajo y se genera un pulso de `uart_done`, comportamiento correspondiente a la finalización de una trama de transmisión.

---

## 2. Resultados esperados de las operaciones

Durante las pruebas programadas se seleccionan los operandos:

```text
A = 9
B = 6
```

Los resultados esperados a partir del código son:

| **Opcode** | **Operación** | **Resultado decimal** | **Hexadecimal** |
|:---:|:---|:---:|:---:|
| `000` | Suma | 15 | `0F` |
| `001` | Resta | 3 | `03` |
| `010` | Multiplicación | 54 | `36` |
| `011` | Comparación | 9 | `09` |
| `100` | Desplazamiento | 18 | `12` |
| `101` | NOT | 6 | `06` |
| `110` | NOR | 0 | `00` |
| `111` | XOR | 15 | `0F` |

Estos valores se obtienen de las operaciones definidas en el código y sirven como referencia para comprobar la simulación.

No representan por sí solos resultados medidos de todas las pruebas.

---

## 3. Escritura y lectura del banco de registros

Una de las verificaciones contempladas consiste en almacenar diferentes valores en las ocho posiciones disponibles.

Mediante el decodificador, cada dato debe escribirse únicamente en la dirección seleccionada.

Posteriormente, al activar la lectura, el circuito debe recuperar el valor correspondiente.

Este procedimiento permite evaluar:

* La selección de direcciones.
* La habilitación de escritura.
* La conservación de los datos.
* La lectura del registro seleccionado.
* El envío de información hacia UART.

El testbench también contempla sobrescribir una dirección y comprobar que los registros vecinos mantengan sus valores anteriores.

---

## 4. Transmisión UART

La transmisión UART permite convertir los valores recuperados del banco en información legible para un computador.

Por ejemplo, si el banco contiene el resultado:

```text
9 × 6 = 54
```

El conversor decimal prepara los caracteres:

```text
'5' '4' CR LF
```

Posteriormente, el transmisor UART debe enviar dichos caracteres en tramas independientes.

En una terminal configurada correctamente, el valor esperado sería:

```text
54
```

Esta función permite verificar los resultados sin depender únicamente de los indicadores LED de la FPGA.

Para confirmar físicamente la recepción, es necesario anexar una captura de la terminal PuTTY con el valor recibido.

---

## 5. Verificación de antirrebotes

El testbench contempla pruebas para confirmar que los botones de lectura y escritura generen un único pulso válido.

Para ello se simulan transiciones rápidas similares a los rebotes de un interruptor mecánico.

También se introducen pulsos de corta duración que deben ser ignorados.

El propósito de estas pruebas es garantizar que una sola pulsación no genere operaciones repetidas.

---

## 6. Reinicio del sistema

Otra prueba contemplada consiste en activar el reset mientras el transmisor UART se encuentra trabajando.

El comportamiento esperado consiste en regresar la línea de transmisión al estado de reposo y desactivar la señal de ocupación.

Posteriormente, el sistema debe ser capaz de iniciar una nueva transmisión.

Este procedimiento permite evaluar la respuesta de la arquitectura ante una interrupción del funcionamiento normal.

---

## 7. Evidencias adicionales

Para completar la documentación práctica del laboratorio se pueden anexar las siguientes capturas:

* Resultado final de la consola del testbench.
* Simulación de la multiplicación y la señal `mult_done`.
* Escritura y lectura de diferentes direcciones.
* Transmisión UART completa.
* Recepción de los datos mediante PuTTY.
* Implementación física del circuito en la FPGA.

Estas evidencias permitirán respaldar las pruebas programadas y los resultados obtenidos.

---

# Conclusiones

* Se comprendió el funcionamiento de una **Unidad Aritmético-Lógica (ALU)** y su importancia dentro de la arquitectura de un procesador, al permitir ejecutar diferentes operaciones mediante un código de control.

* Se implementó una ALU con **ocho operaciones aritméticas y lógicas**, utilizando operandos de cuatro bits y una salida de hasta ocho bits.

* Se reutilizó el circuito **sumador/restador de cuatro bits**, aplicando el complemento a dos para realizar operaciones de resta mediante una estructura de suma binaria.

* Se comprendió la importancia de las **máquinas de estados finitos**, al desarrollar un multiplicador secuencial que utiliza sumas y desplazamientos controlados mediante diferentes estados.

* Se diseñó un **banco de ocho registros de ocho bits**, permitiendo almacenar y recuperar información mediante direcciones binarias.

* Se implementó un **multiplexor**, encargado de seleccionar entre los operandos y el resultado de la ALU antes de almacenar la información.

* Mediante el uso de un **decodificador de tres a ocho líneas** se estableció un mecanismo de selección para habilitar la escritura en una posición específica del banco de registros.

* Se integró un sistema de comunicación **UART**, permitiendo transmitir información binaria mediante un protocolo serial asíncrono.

* Se comprendió la importancia de la **conversión decimal a ASCII**, debido a que permite representar los datos almacenados como números legibles para una terminal serial.

* La implementación de circuitos **antirrebote** permitió incorporar un mecanismo de control para evitar activaciones múltiples provocadas por las transiciones de los pulsadores.

* La simulación presentada permitió comprobar el resultado de la suma **10 + 5 = 15** y observar la finalización de una trama UART.

* El desarrollo del laboratorio permitió integrar conceptos de electrónica digital, lógica combinacional, circuitos secuenciales, direccionamiento, almacenamiento y comunicación, contribuyendo a comprender la estructura funcional de un sistema de procesamiento digital.

---

# Referencias

* **Universidad ECCI.** *Arquitectura de Procesadores - Laboratorio 04: ALU, Banco de Registros y UART*. Material suministrado durante las sesiones de clase.

* **Material de clase.** Diagramas y explicaciones sobre ALU, multiplexores, banco de registros y comunicación UART.

* **Código fuente del laboratorio.** Implementación en Verilog HDL de los módulos `top_fpga`, `top_procesador`, `top_ALU`, `mult`, `banco_registros`, `selector_datos`, `decodificador3a8`, `sumador4b`, `sumador1b`, `uart_tx`, `uart_dec_sender` y `antirebote`.

* **Altera.** [MAX 10 FPGA Developer Center](https://www.altera.com/developers/developer-centers/max-10). Documentación técnica de la familia de dispositivos FPGA MAX 10.

* **Analog Devices.** [UART: A Hardware Communication Protocol](https://www.analog.com/en/resources/analog-dialogue/articles/uart-a-hardware-communication-protocol.html). Fundamentos de la comunicación serial UART.

* **IEEE Standards Association.** [IEEE 1800-2023: SystemVerilog](https://standards.ieee.org/ieee/1800/7743/). Estándar de lenguaje de descripción y verificación de hardware.