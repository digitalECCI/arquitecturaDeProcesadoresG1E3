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