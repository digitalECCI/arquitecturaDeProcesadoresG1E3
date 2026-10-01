# Lab03: Multiplicador de 3 bits usando Máquina de Estados

## Integrantes

* [Juan Diego Cervantes Guio](https://github.com/juandicervantesgu-dev)
* [Juan Sebastian Guerrero Gualteros](https://github.com/juanseguerrerogu07)
* [Samuel Esteban Jaime Gutierrez](https://github.com/Samueljgest) 


### Índice

1. [Fundamentos teóricos](#fundamentos-teóricos)
2. [Documentación del diseño](#documentación-del-diseño)
3. [Diagramas](#diagramas)
4. [Implementación en Verilog](#implementación-en-verilog)
5. [Evidencias de implementación](#evidencias-de-implementación)
6. [Conclusiones](#conclusiones)
7. [Referencias](#referencias)


# Fundamentos teóricos

## 1. Multiplicación secuencial

La **multiplicación secuencial** procesa los operandos bit a bit a lo largo de varios ciclos de reloj.

En este laboratorio se multiplican dos operandos de 3 bits:

* **Multiplicando ($MD$):** Valor de 3 bits que se sumará repetidamente según corresponda.

* **Multiplicador ($MR$):** Valor de 3 bits cuyos bits individuales determinan si el multiplicando se suma o no.

El algoritmo examina el bit menos significativo ($LSB$) del multiplicador $MR$ en cada ciclo:

* **Si el bit evaluado es `1`:** Se suma el valor de $MD$ a la parte superior del registro acumulador.

* **Si el bit evaluado es `0`:** No se efectúa suma (se suma cero).

* **Desplazamiento:** Posteriormente, se desplaza multiplicador a la derecha (o el multiplicando a la izquierda), preparando el siguiente bit de $MR$.

Este proceso se repite $N$ veces (donde $N=3$ es el ancho de los operandos). El resultado final se almacena en un registro ($PP$) de 6 bits ($2 \times N$), garantizando eficiencia en el uso de recursos lógicos al reutilizar la misma unidad aritmética.

## 2. Máquina de Estados Algorítmica (ASM)
Una **Máquina de Estados Algorítmica** (ASM) es un modelo  utilizado para diseñar y representar sistemas secuenciales complejos. Modela la interacción entre la **Unidad de Control** y la **Ruta de Datos** (*Datapath*).

Una **Máquina de Estados Algorítmica** (ASM) coordina el flujo de control del multiplicador interactuando con la Ruta de Datos (*Datapath*) mediante las siguientes señales de control y transiciones de estado[cite: 1]:

1. **`START`:** Estado de inicialización[cite: 1]. Mantiene `RESET = 1`, `DONE = 0`, `SH = 0` y `ADD = 0`[cite: 1]. Permanece en este estado mientras `INIT = 0`[cite: 1]. Al activarse `INIT = 1`, pasa al estado `CHECK`.


2. **`CHECK`:** Inspecciona el bit menos significativo del multiplicador ($LSB\_B$)[cite: 1]. Mantiene `DONE = 0`, `RESET = 0`, `SH = 0`, `ADD = 0`.

   * Si $LSB\_B = 1$, transiciona al estado `ADD`.

   * Si $LSB\_B = 0$, salta directamente al estado `SHIFT`.

3. **`ADD`:** Habilita la suma acumulativa activando la señal `ADD = 1` (`DONE = 0`, `RESET = 0`, `SH = 0`). Transiciona incondicionalmente a `SHIFT`.

4. **`SHIFT`:** Habilita el desplazamiento a la derecha activando `SH = 1` (`DONE = 0`, `RESET = 0`, `ADD = 0`) y actualiza el contador de iteraciones.

   * Si $Z = 0$ (aún faltan bits por procesar), regresa a `CHECK`.

   * Si $Z = 1$ (se completaron los 3 bits), avanza a `END`.

5. **`END`:** Notifica la finalización de la operación activando `DONE = 1` (`RESET = 0`, `SH = 0`, `ADD = 0`). Transiciona de vuelta a `START` para quedar listo ante una nueva multiplicación.

## 3. Lógica secuencial
La lógica secuencial se diferencia de la combinacional en que sus salidas dependen tanto de las entradas actuales como de las salidas/estados anteriores acumulados en el tiempo. Utiliza elementos de almacenamiento síncronos (como *flip-flops*) controlados por una señal de reloj (`clk`).

En este diseño, la lógica secuencial permite:

* Mantener el estado actual de la FSMentre los 5 estados del algoritmo (`START`, `CHECK`, `ADD`, `SHIFT`, `END`).

* Preservar el valor acumulado del producto parcial en cada paso.

* Llevar el conteo de los 3 ciclos requeridos para completar la multiplicación.

### 3. Lógica secuencial y Módulos de Soporte

* **Sincronización y Anti-rebote:** Permite procesar las pulsaciones del botón mecánico de inicio (`KEY_start`) mediante un contador debouncer para prevenir múltiples disparos.

* **Conversión BCD (Double Dabble):** Algoritmo en lógica combinacional que convierte la salida binaria de 6 bits a formato BCD (decenas y unidades) mediante desplazamientos e incrementos condicionales (`+3` si el valor $\ge 5$).

* **Decodificador a 7 Segmentos:** Traduce los dígitos BCD a la codificación de displays de ánodo común en la FPGA.

# Documentación del diseño


# Diagramas




# Implementación en Verilog


# Conclusiones


# Referencias 
**[1]** M. M. Mano y M. D. Ciletti, Digital Design: With an Introduction to the Verilog HDL, VHDL, and SystemVerilog, 6th ed. Pearson, 2018.

**[2]** S. Brown y Z. Vranesic, Fundamentals of Digital Logic with Verilog Design, 3rd ed. McGraw-Hill Education, 2014.

**[3]** D. M. Harris y S. L. Harris, Digital Design and Computer Architecture, 2nd ed. Morgan Kaufmann, 2012.

**[4]** IEEE, IEEE Standard for Verilog Hardware Description Language, IEEE Std 1364.

**[5]** Intel Corporation, Quartus Prime Software Documentation, Intel FPGA.

**[6]** Icarus Verilog, Icarus Verilog Documentation, documentación de simulación y compilación de diseños Verilog HDL.