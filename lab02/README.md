# Lab02 - Sumador/Restador de 4 bits

## Integrantes

* [Juan Diego Cervantes Guio](https://github.com/juandicervantesgu-dev)
* [Juan Sebastian Guerrero Gualteros](https://github.com/juanseguerrerogu07)
* [Samuel Esteban Jaime Gutierrez](https://github.com/Samueljgest) 


### Índice

1. [Fundamentos teóricos](#fundamentos-teóricos)
2. [Documentación del diseño](#documentación-del-diseño)
3. [Diagramas](#diagramas)
4. [Evidencias de implementación](#evidencias-de-implementación)
5. [Conclusiones](#conclusiones)
6. [Referencias](#referencias)


# Fundamentos teóricos
1. *Sistemas binarios*

Los **sistemas digitales** trabajan principalmente con información representada mediante números binarios. A diferencia del sistema decimal, que utiliza diez símbolos del 0 al 9, el sistema binario utiliza únicamente dos valores:

**0: nivel lógico bajo.**
**1: nivel lógico alto.**

Cada posición de un número binario representa una potencia de dos.

Por ejemplo:

1011₂

Su equivalente decimal se obtiene de la siguiente manera:

1×2³ + 0×2² + 1×2¹ + 1×2⁰
8 + 0 + 2 + 1 = 11

Por lo tanto: 1011₂ = 11₁₀


--- 
*En circuitos digitales, cada dígito binario se denomina bit. Cuando se agrupan varios bits es posible representar cantidades mayores y realizar operaciones aritméticas directamente mediante circuitos lógicos.*
2. Suma binaria

La suma binaria sigue reglas similares a la suma decimal, pero utiliza únicamente los valores 0 y 1.

Las operaciones básicas son:

A	B	Suma	Acarreo
0	0	0	0
0	1	1	0
1	0	1	0
1	1	0	1

Cuando se suman tres bits, incluyendo un acarreo de entrada, se utiliza un sumador completo.

3. Sumador completo de 1 bit

Un sumador completo o Full Adder es un circuito combinacional que permite sumar tres entradas binarias:

A
B
Cin: acarreo de entrada

Y genera dos salidas:

S: resultado de la suma.
Cout: acarreo de salida.

La ecuación de la suma es:

S = A ⊕ B ⊕ Cin 

Mientras que el acarreo puede obtenerse mediante:

Cout = (A · B) + (Cin · (A ⊕ B))

La tabla de verdad correspondiente es:

A	B	Cin	S	Cout
0	0	0	0	0
0	0	1	1	0
0	1	0	1	0
0	1	1	0	1
1	0	0	1	0
1	0	1	0	1
1	1	0	0	1
1	1	1	1	1

- - -


4. Sumador de 4 bits

Para realizar operaciones entre números de cuatro bits se pueden conectar cuatro sumadores completos de 1 bit.

Cada etapa recibe el acarreo generado por la etapa anterior.

Para:

A = A3 A2 A1 A0
B = B3 B2 B1 B0

la operación puede visualizarse como:

        A3 A2 A1 A0
      + B3 B2 B1 B0
      -------------
   Cout S3 S2 S1 S0

Debido a que la suma máxima de dos números de cuatro bits es:

1111₂ + 1111₂

equivalente a:

15 + 15 = 30

*se requieren cinco bits para representar completamente el resultado.*

30₁₀ = 11110₂

Por esta razón, el resultado final puede construirse concatenando el acarreo final con los cuatro bits correspondientes a la suma.

{Cout, S}
5. Resta binaria

*Una resta binaria puede implementarse mediante un circuito sumador utilizando el complemento del segundo operando

***En lugar de construir un circuito completamente diferente para la resta, puede aprovecharse el mismo sumador de cuatro bits.***

La expresión convencional es:

A - B

Esta operación puede escribirse mediante complemento a dos como:

A + complemento_a_2(B)
6. Complemento a uno

El complemento a uno de un número binario se obtiene invirtiendo cada uno de sus bits.

Ejemplo:

B = 0101

Complemento a uno:

~B = 1010

En términos de compuertas digitales, esta operación se realiza mediante compuertas NOT.

7. Complemento a dos

El complemento a dos se obtiene realizando dos operaciones:

Invertir todos los bits del número.
Sumar 1 al resultado.

Por ejemplo:

B = 0101

Complemento a uno:

1010

Sumando uno:

1010
+0001
-----
1011

Por lo tanto:

Complemento a 2 de 0101 = 1011

Gracias a este método es posible utilizar el mismo circuito sumador para realizar operaciones de resta.

8. Sumador/Restador

El circuito desarrollado permite seleccionar entre suma y resta utilizando una señal de control.

Se define una entrada denominada:

sel

Su funcionamiento es:

sel	Operación
0	Suma
1	Resta

Cuando:

sel = 0

el número B pasa sin modificaciones al circuito.

Cuando:

sel = 1

los bits de B deben invertirse y se utiliza un acarreo inicial igual a 1.

Esto permite ejecutar:

A + (~B) + 1

que corresponde al complemento a dos utilizado para realizar:

A - B
9. Compuerta XOR como selector

La compuerta XOR permite controlar fácilmente la inversión del operando B.

Su tabla de verdad es:

**B	sel	Salida
**0	0	0**
**1	0	1**
**0	1	1**
**1	1	0**

Cuando:

sel = 0

se obtiene:

*B XOR 0 = B*

Por lo tanto, se realiza una suma normal.

Cuando:

**sel = 1**

se obtiene:

**B XOR 1 = ~B**

permitiendo generar el complemento a uno de B.

Adicionalmente:

***Cin inicial = sel***

Por esta razón, cuando sel = 1, también se suma automáticamente el 1 necesario para obtener el complemento a dos.
# Documentación del diseño
# Diagramas
# Evidencias de implementación
# Conclusiones
# Referencias 