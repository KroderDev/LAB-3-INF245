# Laboratorio 3: Don Bit - Arquitectura y Organización de Computadores

## Introducción

En este laboratorio se implementa un sistema digital en Logisim que simula el recorrido de un vehículo por una ciudad digital llamada Bitópolis. El sistema procesa un código de 4 bits como punto de partida y muestra, paso a paso, el trayecto en un display de 7 segmentos hasta llegar al nodo final ‘F’.

## Diseño del sistema

### Algoritmo general de funcionamiento

1. El usuario selecciona un nodo inicial (4 bits) con los interruptores y pulsa **LOAD**.
2. En el siguiente flanco de reloj, el registro de estado carga ese valor inicial.
3. A partir de ahí, en cada pulso de reloj:
   - La lógica combinacional de **NextState** calcula el siguiente nodo según una ruta predefinida.
   - El registro de estado (cuatro flip-flops D) se actualiza con ese nuevo valor.
   - El decodificador de 7 segmentos muestra la letra correspondiente al nodo actual.
4. Cuando el estado coincide con ‘F’, el bloque **DetectF** activa la señal **Finished**, detiene la captura en el registro de estado y fija el display en ‘F’.  
5. Para reiniciar, se activa **Reset**, que fuerza el registro a “0000” (nodo A).

### Tabla de verdad

- Para **NextState** se define una tabla de verdad que, para cada código de estado (A…P, de 0000 a 1111), indica el código binario del nodo sucesor.  
- Para el **Decodificador 7 segmentos** (subcircuito D7) se construye una tabla de verdad que mapea cada código de 4 bits (estado A–P) a las 7 señales \((a,b,c,d,e,f,g)\) que encienden los segmentos correspondientes.

### Mapas de Karnaugh

- A partir de las tablas de verdad de **NextState** y de cada segmento del decodificador, se generan mapas de Karnaugh para minimizar las funciones booleanas.
- Esto permite implementar cada bit de **NextState** y cada segmento del display con un número reducido de puertas lógicas.

### Subcircuitos

1. **StateRegister**:  
   - Cuatro flip-flops tipo D en paralelo que almacenan el estado actual \(\,State[3..0]\).  
   - Entrada asíncrona **Reset** para forzar “0000” y entrada **Enable** deshabilita la captura cuando se alcanza ‘F’.

2. **MuxDeCarga**:  
   - Conjunto de multiplexores 2:1 que, según la señal **LOAD**, eligen entre  
     - \(\texttt{UserIn}[3..0]\) (valor inicial)  
     - \(\texttt{NextState}[3..0]\) (siguiente estado calculado).  

3. **NextState**:  
   - Lógica combinacional (o multiplexores 16:1) de 4 bits a 4 bits que, dado el estado actual, genera el nodo sucesor.  
   - Cuando el estado es ‘F’, se mantiene en ‘F’ para detener el avance.

4. **DetectF**:  
   - Comparador que detecta si el registro de estado vale el código binario de ‘F’.  
   - Activa la señal **Finished** que deshabilita el registro de estado e indica al usuario que el recorrido terminó.

5. **D7 (Decodificador 7 segmentos)**:  
   - Convierte el código de 4 bits \(\,State[3..0]\) en las 7 señales \((a,b,c,d,e,f,g)\) para mostrar la letra del nodo en el display.  
   - Utiliza las expresiones minimizadas obtenidas de los mapas de Karnaugh.

## Supuestos

No se requiere retroceso ni manejo de errores en tiempo real.
El sistema parte desde reposo y no repite el ciclo una vez finalizado.

> [!NOTE]  
> La información detallada se encuentra en [informe.pdf](informe.pdf).

## Contacto

* **Sebastián Richiardi**, Rol: 202030555-2, Paralelo: 201  
* **Gabriel Alejandro Toro Varela**, Rol: 202204557-4, Paralelo: 201
