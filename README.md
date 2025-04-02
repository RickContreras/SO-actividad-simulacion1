# Actividad de seguimiento - Simulación 1

![Universidad](https://img.shields.io/badge/Universidad-UdeA-green)

|Integrante|correo|usuario github|
|---|---|---|
| Ricardo Contreras Garzón | ricardo.contreras1@udea.edu.co | [RickContreras](https://github.com/RickContreras) |
| Santiago Arenas Gómez | santiago.arenas1@udea.edu.co |[Sag0719](https://github.com/Sag0719)|

## Instrucciones

Antes de empezar a realizar esta actividad haga un **fork** de este repositorio y sobre este trabaje en la solución de las preguntas planteadas en la actividad de simulación. Las respuestas deben ser respondidas en español o si lo prefiere en ingles en el lugar señalado para ello (La palabra **answer** muestra donde).

**Importante**:
* Como la actividad es en las parejas del laboratorio, solo uno de los integrantes tiene que hacer el fork; y sobre repositorio bifurcado que se genera, la modificación se realiza en equipo.
* Como la entrega se debe hacer modificando el archivo READNE, se recomienda que consulte mas sobre el lenguaje **Markdown**. En el repo adjuntan dos cheatsheet ([cheat sheet 1](Markdown_Cheat_Sheet.pdf), [cheatsheet 2](markdown-cheatsheet.pdf)) para consulta rapida.
* Entre mas creativo mejor.

## Homework (Simulation)

This program, [`process-run.py`](process-run.py), allows you to see how process states change as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the [README](https://github.com/remzi-arpacidusseau/ostep-homework/blob/master/cpu-intro/README.md) for details.

## ❓ Preguntas

### 1️⃣ Pregunta 1

> Ejecuta `process-run.py` con las siguientes banderas: `-l 5:100,5:100`. ¿Cuál debería ser la utilización de la CPU (es decir, el porcentaje de tiempo que la CPU está en uso)? ¿Por qué lo sabes? Utiliza las banderas `-c` y `-p` para comprobar si estabas en lo correcto.

<details>
<summary><b>📊 Respuesta</b></summary>

Al ejecutar el comando:

```bash
python3 ./process-run.py -l 5:100,5:100
```

Se observa que ambos procesos realizan únicamente operaciones en la CPU. A continuación, se muestra cómo se distribuyen las instrucciones:

| **Process 0** | **Process 1** |
|:-------------:|:-------------:|
| cpu | cpu |
| cpu | cpu |
| cpu | cpu |
| cpu | cpu |
| cpu | cpu |

Al ejecutar el comando con las banderas `-c` y `-p`:

```bash
python3 ./process-run.py -l 5:100,5:100 -c -p
```

Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0 | PID: 1 | CPU | I/O |
|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:cpu | READY | 1 | |
| 2 | RUN:cpu | READY | 1 | |
| 3 | RUN:cpu | READY | 1 | |
| 4 | RUN:cpu | READY | 1 | |
| 5 | RUN:cpu | READY | 1 | |
| 6 | DONE | RUN:cpu | 1 | |
| 7 | DONE | RUN:cpu | 1 | |
| 8 | DONE | RUN:cpu | 1 | |
| 9 | DONE | RUN:cpu | 1 | |
| 10 | DONE | RUN:cpu | 1 | |

#### 📈 Estadísticas:
| Métrica | Valor |
|:-------:|:-----:|
| Tiempo total | 10 unidades |
| CPU ocupada | 10 unidades |
| I/O ocupada | 0 unidades |

#### 🔍 Análisis:

- En la primera ejecución, se observa cómo cada proceso utiliza la CPU de manera secuencial, pero no se detalla cómo el sistema operativo gestiona el cambio de procesos.
- En la segunda ejecución, con las banderas `-c` y `-p`, se muestra explícitamente el estado de cada proceso en cada unidad de tiempo. Esto permite observar cómo los procesos se turnan para usar la CPU.
- La CPU está ocupada durante todo el tiempo de ejecución, lo que resulta en una utilización del **100%**. No hay operaciones de I/O, por lo que el tiempo ocupado por I/O es **0%**.
   
En resumen, esta simulación demuestra cómo los procesos se alternan en el uso de la CPU y cómo el sistema operativo gestiona su ejecución de manera eficiente.

</details>

### 2️⃣ Pregunta 2

> Ahora ejecuta con estas banderas: `./process-run.py -l 4:100,1:0`. Estas banderas especifican un proceso con 4 instrucciones (todas para usar la CPU), y uno que simplemente emite un I/O y espera a que se complete. ¿Cuánto tiempo lleva completar ambos procesos? Utiliza `-c` y `-p` para averiguar si estabas en lo correcto.

<details>
<summary><b>📊 Respuesta</b></summary>

Al ejecutar el comando:

```bash
python3 process-run.py -l 4:100,1:0
```

Se observa que el Proceso 0 realiza 4 instrucciones en la CPU, mientras que el Proceso 1 realiza una operación de I/O y espera a que esta finalice. A continuación, se muestra cómo se distribuyen las instrucciones:

| **Process 0** | **Process 1** |
|:-------------:|:-------------:|
| cpu | io |
| cpu | io_done |
| cpu | |
| cpu | |

Al ejecutar el comando con las banderas `-c` y `-p`:

```bash
python3 process-run.py -l 4:100,1:0 -c -p
```

Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0 | PID: 1 | CPU | I/O |
|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:cpu | READY | 1 | |
| 2 | RUN:cpu | READY | 1 | |
| 3 | RUN:cpu | READY | 1 | |
| 4 | RUN:cpu | READY | 1 | |
| 5 | DONE | RUN:io | 1 | |
| 6 | DONE | BLOCKED | | 1 |
| 7 | DONE | BLOCKED | | 1 |
| 8 | DONE | BLOCKED | | 1 |
| 9 | DONE | BLOCKED | | 1 |
| 10 | DONE | BLOCKED | | 1 |
| 11* | DONE | RUN:io_done | 1 | |

#### 📈 Estadísticas:
| Métrica | Valor |
|:-------:|:-----:|
| Tiempo total | 11 unidades |
| CPU ocupada | 6 unidades |
| I/O ocupada | 5 unidades |
   
#### 🔍 Análisis:

- El **Proceso 0** utiliza la CPU durante 4 unidades de tiempo consecutivas y finaliza.
- El **Proceso 1** realiza una operación de I/O, lo que bloquea su ejecución durante 5 unidades de tiempo hasta que la operación de I/O se completa.
- La CPU está ocupada durante 6 de las 11 unidades de tiempo, lo que resulta en una utilización del **54.55%**.
- La I/O está ocupada durante 5 de las 11 unidades de tiempo, lo que resulta en una utilización del **45.45%**.
   
En resumen, esta simulación muestra cómo el sistema operativo gestiona los procesos que realizan operaciones de I/O. Este caso ilustra un escenario ideal donde la CPU se asigna a otro proceso tan pronto como el proceso actual ha finalizado completamente, maximizando así la eficiencia del uso de los recursos del sistema.

</details>

### 3️⃣ Pregunta 3

> Cambia el orden de los procesos: `-l 1:0,4:100`. ¿Qué sucede ahora? ¿Importa cambiar el orden? ¿Por qué? (Como siempre, utiliza `-c` y `-p` para ver si estabas en lo correcto)

<details>
<summary><b>📊 Respuesta</b></summary>

Al ejecutar el comando:

```bash
python3 ./process-run.py -l 1:0,4:100
```

Se observa que el Proceso 0 realiza una operación de I/O y espera a que esta finalice, mientras que el Proceso 1 utiliza la CPU para ejecutar 4 instrucciones. A continuación, se muestra cómo se distribuyen las instrucciones:

| **Process 0** | **Process 1** |
|:-------------:|:-------------:|
| io | cpu |
| io_done | cpu |
| | cpu |
| | cpu |

Al ejecutar el comando con las banderas `-c` y `-p`:

```bash
python3 ./process-run.py -l 1:0,4:100 -c -p
```

Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0 | PID: 1 | CPU | I/O |
|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:io | READY | 1 | |
| 2 | BLOCKED | RUN:cpu | 1 | 1 |
| 3 | BLOCKED | RUN:cpu | 1 | 1 |
| 4 | BLOCKED | RUN:cpu | 1 | 1 |
| 5 | BLOCKED | RUN:cpu | 1 | 1 |
| 6 | BLOCKED | DONE | | 1 |
| 7* | RUN:io_done | DONE | 1 | |

#### 📈 Estadísticas:
| Métrica | Valor |
|:-------:|:-----:|
| Tiempo total | 7 unidades |
| CPU ocupada | 6 unidades |
| I/O ocupada | 5 unidades |
   
#### 🔍 Análisis:

- El **Proceso 0** realiza una operación de I/O en la primera unidad de tiempo y queda bloqueado durante 5 unidades de tiempo mientras espera que la operación de I/O finalice.
- El **Proceso 1** utiliza la CPU durante 4 unidades de tiempo consecutivas y finaliza antes de que el Proceso 0 complete su operación de I/O.
- La CPU está ocupada durante 6 de las 7 unidades de tiempo, lo que resulta en una utilización del **85.71%**.
- La I/O está ocupada durante 5 de las 7 unidades de tiempo, lo que resulta en una utilización del **71.43%**.

En resumen, esta simulación muestra cómo el sistema operativo prioriza el uso de la CPU para procesos listos mientras otros procesos están bloqueados esperando operaciones de I/O. Esto asegura un uso eficiente de los recursos del sistema.

</details>

### 4️⃣ Pregunta 4

> Ahora exploraremos algunas de las otras banderas. Una bandera importante es `-S`, que determina cómo reacciona el sistema cuando un proceso emite un I/O. Con la bandera establecida en SWITCH_ON_END, el sistema NO cambiará a otro proceso mientras uno está haciendo I/O, en su lugar esperará hasta que el proceso esté completamente terminado. ¿Qué sucede cuando ejecutas los siguientes dos procesos (`-l 1:0,4:100 -c -S SWITCH_ON_END`), uno haciendo I/O y el otro haciendo trabajo de CPU?

<details>
<summary><b>📊 Respuesta</b></summary>

Al ejecutar el comando:

```bash
python3 process-run.py -l 1:0,4:100 -c -S SWITCH_ON_END
```

Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0 | PID: 1 | CPU | I/O |
|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:io | READY | 1 | |
| 2 | BLOCKED | READY | | 1 |
| 3 | BLOCKED | READY | | 1 |
| 4 | BLOCKED | READY | | 1 |
| 5 | BLOCKED | READY | | 1 |
| 6 | BLOCKED | READY | | 1 |
| 7* | RUN:io_done | READY | 1 | |
| 8 | DONE | RUN:cpu | 1 | |
| 9 | DONE | RUN:cpu | 1 | |
| 10 | DONE | RUN:cpu | 1 | |
| 11 | DONE | RUN:cpu | 1 | |

#### 📈 Estadísticas:
| Métrica | Valor |
|:-------:|:-----:|
| Tiempo total | 11 unidades |
| CPU ocupada | 6 unidades |
| I/O ocupada | 5 unidades |

#### 🔍 Análisis detallado:

- El **Proceso 0** realiza una operación de I/O en la primera unidad de tiempo y queda bloqueado durante 5 unidades de tiempo mientras espera que la operación de I/O finalice.
- Durante este tiempo, el sistema **no cambia** al **Proceso 1**, ya que la bandera `SWITCH_ON_END` indica que no se debe cambiar de proceso hasta que el proceso actual haya terminado completamente.
- Una vez que el **Proceso 0** finaliza su operación de I/O, el **Proceso 1** utiliza la CPU durante 4 unidades de tiempo consecutivas y finaliza.
- La CPU está ocupada durante **6 de las 11 unidades de tiempo**, lo que resulta en una utilización del **54.55%**.
- La I/O está ocupada durante **5 de las 11 unidades de tiempo**, lo que resulta en una utilización del **45.45%**.

#### 💡 Conclusión:

Este comportamiento **no es eficiente**, ya que el sistema permanece inactivo durante el tiempo en que el **Proceso 0** está bloqueado esperando la finalización de su operación de I/O. Esto demuestra que la bandera `SWITCH_ON_END` puede llevar a un uso ineficiente de los recursos del sistema, especialmente en escenarios donde hay procesos listos para ejecutarse mientras otros están bloqueados.  

En este caso, **permitir que el sistema cambie a otro proceso mientras uno está bloqueado** podría mejorar significativamente la utilización de la CPU y reducir el tiempo total de ejecución.

</details>
   <br>

### 5️⃣ Pregunta 5

> Ahora, ejecuta los mismos procesos, pero con el comportamiento de cambio establecido para cambiar a otro proceso cuando uno está WAITING por I/O (`-l 1:0,4:100 -c -S SWITCH_ON_IO`). ¿Qué sucede ahora? Utiliza `-c` y `-p` para confirmar que estás en lo correcto.

<details>
<summary><b>📊 Respuesta</b></summary>

```bash
python3 process-run.py -l 1:0,4:100 -c -S SWITCH_ON_IO
```
Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0 | PID: 1 | CPU | I/O |
|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:io | READY | 1 | |
| 2 | BLOCKED | RUN:cpu | | 1 |
| 3 | BLOCKED | RUN:cpu | | 1 |
| 4 | BLOCKED | RUN:cpu | | 1 |
| 5 | BLOCKED | RUN:cpu | | 1 |
| 6 | BLOCKED | DONE | | 1 |
| 7* | RUN:io_done | DONE | 1 | |

#### 📈 Estadísticas:
| Métrica | Valor |
|:-------:|:-----:|
| Tiempo total | 7 unidades |
| CPU ocupada | 6 unidades |
| I/O ocupada | 5 unidades |

#### 🔍 Análisis detallado:

- En la primera unidad de tiempo, el Proceso 0 inicia su operación de I/O y queda bloqueado.

- A partir del segundo ciclo, el sistema cambia inmediatamente al Proceso 1, ya que SWITCH_ON_IO permite el cambio cuando un proceso queda en estado de espera por I/O.

- El Proceso 1 utiliza la CPU durante 4 unidades de tiempo hasta que finaliza su ejecución.

- Después de que el Proceso 1 finaliza, la operación de I/O del Proceso 0 se completa en la unidad de tiempo 7.

- La CPU está ocupada durante 6 de las 7 unidades de tiempo, lo que resulta en una utilización del 85.71%.

- La I/O está ocupada durante 5 de las 7 unidades de tiempo, lo que resulta en una utilización del 71.43%.

#### 💡 Conclusión:

- En comparación con el caso anterior (SWITCH_ON_END), donde la CPU estuvo inactiva durante 5 unidades de tiempo, este enfoque permite que otro proceso utilice la CPU mientras el primero está bloqueado por I/O.

- Esto mejora significativamente la eficiencia del sistema, reduciendo el tiempo total de ejecución de 11 a 7 unidades de tiempo.

- La bandera SWITCH_ON_IO permite un mayor aprovechamiento de los recursos al evitar tiempos muertos cuando un proceso está en espera.
</details>

### 6️⃣ Pregunta 6

> Otro comportamiento importante es qué hacer cuando se completa un I/O. Con `-I IO_RUN_LATER`, cuando se completa un I/O, el proceso que lo emitió no se ejecuta necesariamente de inmediato; más bien, lo que sea que se estaba ejecutando en ese momento sigue ejecutándose. ¿Qué sucede cuando ejecutas esta combinación de procesos? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER`) ¿Se están utilizando eficazmente los recursos del sistema?

<details>
<summary><b>📊 Respuesta</b></summary>

```bash
python3 process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER
```
Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0	| PID: 1	| PID: 2	| PID: 3	| CPU	| I/O |
|:------:|:------:|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:io  | READY  | READY | READY | 1 | |
| 2 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 3 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 4 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 5 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 6 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 7* | READY | DONE | RUN:cpu | READY | 1 | |
| 8 | READY | DONE | RUN:cpu | READY | 1 | |
| 9 | READY | DONE | RUN:cpu | READY | 1 | |
| 10 | READY | DONE | RUN:cpu | READY | 1 | |
| 11 | READY | DONE | RUN:cpu | READY | 1 | |
| 12 | READY | DONE | DONE | RUN:cpu | 1 | |
| 13 | READY | DONE | DONE | RUN:cpu | 1 | |
| 14 | READY | DONE | DONE | RUN:cpu | 1 | |
| 15 | READY | DONE | DONE | RUN:cpu | 1 | |
| 16 | READY | DONE | DONE | RUN:cpu | 1 | |
| 17 | RUN:io_done | DONE | DONE | DONE | 1 | |
| 18 | RUN:io | DONE | DONE | DONE | 1 | |
| 19 | BLOCKED | DONE | DONE | DONE | | 1 |
| 20 | BLOCKED | DONE | DONE | DONE | | 1 |
| 21 | BLOCKED | DONE | DONE | DONE | | 1 |
| 22 | BLOCKED | DONE | DONE | DONE | | 1 |
| 23 | BLOCKED | DONE | DONE | DONE | | 1 |
| 24* | RUN:io_done | DONE | DONE | DONE | 1 | |
| 25 | RUN:io | DONE | DONE | DONE | 1 | |
| 26 | BLOCKED | DONE | DONE | DONE | | 1 |
| 27 | BLOCKED | DONE | DONE | DONE | | 1 |
| 28 | BLOCKED | DONE | DONE | DONE | | 1 |
| 29 | BLOCKED | DONE | DONE | DONE | | 1 |
| 30 | BLOCKED | DONE | DONE | DONE | | 1 |
| 31* | RUN:io_done | DONE | DONE | DONE | 1 | |

#### 📈 Estadísticas:
| Métrica        | Valor    |
|---------------|---------|
| Tiempo total  | 31      |
| CPU ocupada   | 21 (67.74%) |
| I/O ocupada   | 15 (48.39%) |

#### 🔍 Análisis detallado:

- En la primera unidad de tiempo, el Proceso 0 inicia una operación de I/O y queda bloqueado.

- Como el sistema tiene SWITCH_ON_IO, el Proceso 1 toma la CPU y ejecuta sus instrucciones.

- Cuando la operación de I/O de un proceso finaliza, con IO_RUN_LATER este no se ejecuta inmediatamente, sino que sigue en estado READY hasta que le toque su turno en la cola de ejecución.

- Esto causa que la CPU siga ejecutando los otros procesos de CPU en lugar de atender al proceso que finalizó su operación de I/O.

- La CPU se mantiene ocupada durante 21 de las 31 unidades de tiempo, lo que implica un uso del 67.74%.

- La I/O estuvo ocupada durante 15 de las 31 unidades de tiempo, lo que representa un 48.39%.

#### 💡 Conclusión:

- El comportamiento de IO_RUN_LATER retrasa la ejecución de procesos que finalizan operaciones de I/O, permitiendo que otros procesos continúen en la CPU sin interrupción.

- Este enfoque es eficiente para procesos de CPU intensivos, ya que permite que la CPU no se quede inactiva esperando la finalización de una operación de I/O.

- Sin embargo, si un proceso depende de I/O frecuente, este comportamiento puede introducir demoras innecesarias, ya que el proceso que completó su operación de I/O tiene que esperar su turno para volver a ejecutarse.

- Una posible optimización sería usar IO_RUN_IMMEDIATE, que permite que el proceso que termina su I/O reciba la CPU de inmediato, reduciendo tiempos de espera.

</details>

### 7️⃣ Pregunta 7

> Ahora ejecuta los mismos procesos, pero con `-I IO_RUN_IMMEDIATE` establecido, que ejecuta inmediatamente el proceso que emitió el I/O. ¿En qué difiere este comportamiento? ¿Por qué ejecutar nuevamente un proceso que acaba de completar un I/O podría ser una buena idea?

<details>
<summary><b>📊 Respuesta</b></summary>

```bash
python3 process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_IMMEDIATE
```
Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

| Tiempo | PID: 0 | PID: 1 | PID: 2 | PID: 3 | CPU | I/O |
|:------:|:------:|:------:|:------:|:------:|:---:|:---:|
| 1 | RUN:io | READY | READY | READY | 1 | |
| 2 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 3 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 4 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 5 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 6 | BLOCKED | RUN:cpu | READY | READY | 1 | 1 |
| 7* | RUN:io_done | DONE | READY | READY | 1 | |
| 8 | RUN:io | DONE | READY  | READY | 1 | |
| 9 | BLOCKED | DONE | RUN:cpu | READY | 1 | 1 |
| 10 | BLOCKED | DONE | RUN:cpu | READY | 1 | 1 |
| 11 | BLOCKED | DONE | RUN:cpu | READY | 1 | 1 |
| 12 | BLOCKED | DONE | RUN:cpu | READY | 1 | 1 |
| 13 | BLOCKED | DONE | RUN:cpu | READY | 1 | 1 |
| 14* | RUN:io_done | DONE | DONE | READY | 1 | |
| 15 | RUN:io | DONE | DONE | READY  | 1 | |
| 16 | BLOCKED | DONE | DONE | RUN:cpu | 1 | 1 |
| 17 | BLOCKED | DONE | DONE | RUN:cpu | 1 | 1 |
| 18 | BLOCKED | DONE | DONE | RUN:cpu | 1 | 1 |
| 19 | BLOCKED | DONE | DONE | RUN:cpu | 1 | 1 |
| 20 | BLOCKED | DONE | DONE | RUN:cpu | 1 | 1 |
| 21* | RUN:io_done | DONE | DONE | DONE | 1 | |

#### 📈 Estadísticas:
| Métrica        | Valor    |
|---------------|---------|
| Tiempo total  | 21      |
| CPU ocupada   | 21 (100.00%) |
| I/O ocupada   | 15 (71.43%) |

#### 🔍 Análisis detallado:

- Con IO_RUN_IMMEDIATE, el proceso que finaliza una operación de I/O se ejecuta inmediatamente después de completarla.

- Esto contrasta con la ejecución anterior con IO_RUN_LATER, donde el proceso que terminaba la I/O tenía que esperar a que el proceso en ejecución terminara su tiempo en CPU.

- En este caso, el tiempo total de ejecución se reduce de 31 a 21 unidades, lo que indica una mayor eficiencia.

- La CPU estuvo ocupada el 100% del tiempo, eliminando periodos de inactividad.

- La utilización de I/O también aumentó, pasando del 48.39% al 71.43%, lo que significa que se usó de manera más efectiva.

#### 💡 Conclusión:

Ejecutar inmediatamente un proceso que acaba de completar un I/O mejora significativamente el rendimiento del sistema al evitar tiempos muertos y aprovechar al máximo los recursos disponibles.

Esto es beneficioso en situaciones donde los procesos dependen fuertemente de I/O, como en servidores web o sistemas interactivos, donde reducir la latencia es crucial.

</details>
   <br>

### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.
