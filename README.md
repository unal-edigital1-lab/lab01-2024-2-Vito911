# Lab 01 - Pruebas básicas de Quartus con el funcionamiento del sumador de 1 bit

## Entregable 1

### Descripción del módulo `sum1bcc_primitive`
Este módulo implementa un **sumador completo de 1 bit** utilizando compuertas lógicas básicas. Realiza la suma de dos bits de entrada (**A** y **B**) junto con un bit de acarreo de entrada (**Ci**) y genera como resultado:
- **S**: El bit resultante de la suma.
- **Cout**: El acarreo de salida.

```verilog
module sum1bcc_primitive (A, B, Ci, Cout, S);  // Declaración del módulo
  input  A;  // Entrada: primer bit a sumar
  input  B;  // Entrada: segundo bit a sumar
  input  Ci; // Entrada: acarreo de entrada
  output Cout; // Salida: acarreo de salida
  output S;    // Salida: suma de un bit
```
En esta sección declaramos el módulo, definiendo las entradas (**A**, **B**, y **Ci**) y salidas (**S** y **Cout**).

### Declaración de cables (`wire`)
Los cables (`wire`) conectan las salidas de las compuertas lógicas con las entradas de otras compuertas o salidas del módulo.

```verilog
  wire a_ab;  // Conexión para el resultado de A AND B
  wire x_ab;  // Conexión para el resultado de A XOR B
  wire cout_t; // Conexión para el resultado de (A XOR B) AND Ci
```

### Operaciones lógicas
Cada operación lógica implementa una parte del cálculo del sumador completo:

1. **Compuerta AND para generar acarreo intermedio de A y B**
```verilog
  and(a_ab, A, B); // a_ab = A AND B
```

2. **Compuerta XOR para calcular suma parcial de A y B**
```verilog
  xor(x_ab, A, B); // x_ab = A XOR B
```

3. **Compuerta XOR para incluir Ci en la suma**
```verilog
  xor(S, x_ab, Ci); // S = (A XOR B) XOR Ci
```

4. **Compuerta AND para calcular acarreo adicional por Ci**
```verilog
  and(cout_t, x_ab, Ci); // cout_t = (A XOR B) AND Ci
```

5. **Compuerta OR para combinar los acarreos**
```verilog
  or (Cout, cout_t, a_ab); // Cout = (cout_t) OR (a_ab)
```

### Finalización del módulo
El módulo se finaliza con:
```verilog
endmodule
```

---

## Entregable 2

### Descripción del módulo `sum1bcc`
Este módulo implementa un **sumador completo de 1 bit** utilizando una descripción funcional basada en un registro (`reg`) de 2 bits para almacenar el resultado de la suma y el acarreo.

```verilog
module sum1bcc (A, B, Ci, Cout, S);  // Declaración del módulo
  input  A;  // Entrada: primer bit a sumar
  input  B;  // Entrada: segundo bit a sumar
  input  Ci; // Entrada: acarreo de entrada
  output Cout; // Salida: acarreo de salida
  output S;    // Salida: suma de un bit
```

### Uso del registro `st`
El registro `st` almacena temporalmente el resultado de la suma y el acarreo. 

```verilog
  reg [1:0] st;   // Registro que guarda el resultado de la suma
  assign S = st[0];  // Bit menos significativo: salida de la suma
  assign Cout = st[1]; // Bit más significativo: acarreo de salida
```

### Bloque `always`
El bloque `always` se activa con cualquier cambio en las entradas y calcula la suma, asignando el resultado al registro `st`.

```verilog
  always @ ( * ) begin
    st  = A + B + Ci; // Suma de las entradas y acarreo
  end
endmodule
```

---

### Diferencias entre `sum1bcc_primitive` y `sum1bcc`

1. **Implementación lógica vs funcional:**
   - `sum1bcc_primitive` utiliza compuertas lógicas (`and`, `xor`, `or`) para calcular el resultado paso a paso.
   - `sum1bcc` utiliza un registro para almacenar la suma y asigna directamente las salidas a los bits correspondientes del registro.

2. **Flexibilidad:**
   - `sum1bcc` es más compacto y legible gracias al uso del bloque `always`, pero depende de la sintaxis avanzada de Verilog.
   - `sum1bcc_primitive` permite observar directamente cómo se construyen las operaciones con compuertas básicas, útil para fines educativos.

---

## Entregable 3: Simulación

### Configuración de la simulación
Utilizamos el entorno de Questat para validar el funcionamiento del sumador. Forzamos las entradas (`A`, `B`, `Ci`) a señales de reloj (`clock`) para generar todas las combinaciones posibles de entrada.

### Resultados de la simulación
En la siguiente imagen observamos las señales de entrada y salida del módulo durante la simulación:

![Simulación 1](Simulación.png)

La gráfica resultante muestra las combinaciones posibles de las entradas y cómo se calculan las salidas (`S` y `Cout`) en cada caso.

![Gráfica de sumador](./pictures/Sumador_1_Bit.jpg)

Finalmente, verificamos que el sumador cumple con la tabla de verdad esperada:

| A  | B  | Ci | S | Cout |
|----|----|----|---|------|
|  0 |  0 |  0 | 0 |    0 |
|  0 |  0 |  1 | 1 |    0 |
|  0 |  1 |  0 | 1 |    0 |
|  0 |  1 |  1 | 0 |    1 |
|  1 |  0 |  0 | 1 |    0 |
|  1 |  0 |  1 | 0 |    1 |
|  1 |  1 |  0 | 0 |    1 |
|  1 |  1 |  1 | 1 |    1 |

---

## Conclusión
- Comprendimos la implementación de un sumador completo de 1 bit usando dos enfoques diferentes en Verilog.
- Validamos su funcionalidad mediante simulaciones en ModelSim y confirmamos que ambas implementaciones cumplen con las especificaciones de la tabla de verdad.
- La experiencia reforzó el conocimiento sobre descripciones primitivas y funcionales en diseño digital.
