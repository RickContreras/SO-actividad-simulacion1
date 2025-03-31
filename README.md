# Actividad de seguimiento - Simulación 1

|Integrante|correo|usuario github|
|---|---|---|
| Ricardo Contreras Garzón | ricardo.contreras1@udea.edu.co | RickContreras |
|Nombre completo integrante 2|correo integrante 2|gihub user integrante 2|

## Instrucciones

Antes de empezar a realizar esta actividad haga un **fork** de este repositorio y sobre este trabaje en la solución de las preguntas planteadas en la actividad de simulación. Las respuestas deben ser respondidas en español o si lo prefiere en ingles en el lugar señalado para ello (La palabra **answer** muestra donde).

**Importante**:
* Como la actividad es en las parejas del laboratorio, solo uno de los integrantes tiene que hacer el fork; y sobre repositorio bifurcado que se genera, la modificación se realiza en equipo.
* Como la entrega se debe hacer modificando el archivo READNE, se recomienda que consulte mas sobre el lenguaje **Markdown**. En el repo adjuntan dos cheatsheet ([cheat sheet 1](Markdown_Cheat_Sheet.pdf), [cheatsheet 2](markdown-cheatsheet.pdf)) para consulta rapida.
* Entre mas creativo mejor.

## Homework (Simulation)

This program, [`process-run.py`](process-run.py), allows you to see how process states change as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the [README](https://github.com/remzi-arpacidusseau/ostep-homework/blob/master/cpu-intro/README.md) for details.

### Questions

1. Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` flags to see if you were right.
   
   <details>
   <summary>Answer</summary>

   Al ejecutar el comando:

   ```bash
   python3 ./process-run.py -l 5:100,5:100
   ```

   Se observa que ambos procesos realizan únicamente operaciones en la CPU. A continuación, se muestra cómo se distribuyen las instrucciones:

   | **Process 0**         | **Process 1**         |
   |------------------------|-------------------|
   | cpu         | cpu      |
   | cpu         | cpu      |
   | cpu         | cpu      |
   | cpu         | cpu      |


   Al ejecutar el comando con las banderas `-c` y `-p`:

   ```bash
   python3 ./process-run.py -l 5:100,5:100 -c -p
   ```

   Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

   | Tiempo | PID: 0         | PID: 1         | CPU | I/O |
   |--------|----------------|----------------|-----|-----|
   | 1      | RUN:cpu        | READY          | 1   |     |
   | 2      | RUN:cpu        | READY          | 1   |     |
   | 3      | RUN:cpu        | READY          | 1   |     |
   | 4      | RUN:cpu        | READY          | 1   |     |
   | 5      | RUN:cpu        | READY          | 1   |     |
   | 6      | DONE           | RUN:cpu        | 1   |     |
   | 7      | DONE           | RUN:cpu        | 1   |     |
   | 8      | DONE           | RUN:cpu        | 1   |     |
   | 9      | DONE           | RUN:cpu        | 1   |     |
   | 10     | DONE           | RUN:cpu        | 1   |     |

   **Estadísticas:**
   | Métrica         | Valor         |
   |------------------|---------------|
   | Tiempo total     | 10 unidades   |
   | CPU ocupada      | 10 unidades   |
   | I/O ocupada      | 0 unidades    |


   Análisis:

   - En la primera ejecución, se observa cómo cada proceso utiliza la CPU de manera secuencial, pero no se detalla cómo el sistema operativo gestiona el cambio de procesos.
   - En la segunda ejecución, con las banderas `-c` y `-p`, se muestra explícitamente el estado de cada proceso en cada unidad de tiempo. Esto permite observar cómo los procesos se turnan para usar la CPU.
   - La CPU está ocupada durante todo el tiempo de ejecución, lo que resulta en una utilización del 100%. No hay operaciones de I/O, por lo que el tiempo ocupado por I/O es 0%.
   
   En resumen, esta simulación demuestra cómo los procesos se alternan en el uso de la CPU y cómo el sistema operativo gestiona su ejecución de manera eficiente.

   </details>
   <br>

2. Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right. 
   
   <details>
   <summary>Answer</summary>
   Al ejecutar el comando:

   ```bash
   python3 process-run.py -l 4:100,1:0
   ```

   Se observa que el Proceso 0 realiza 4 instrucciones en la CPU, mientras que el Proceso 1 realiza una operación de I/O y espera a que esta finalice. A continuación, se muestra cómo se distribuyen las instrucciones:

   | **Process 0**         | **Process 1**         |
   |------------------------|-------------------|
   | cpu         | io      |
   | cpu         | io_done      |
   | cpu         |      |
   | cpu         |      |

   Al ejecutar el comando con las banderas `-c` y `-p`:

   ```bash
   python3 process-run.py -l 4:100,1:0 -c -p
   ```

   Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

   | Tiempo | PID: 0         | PID: 1         | CPU | I/O |
   |--------|----------------|----------------|-----|-----|
   | 1      | RUN:cpu        | READY          | 1   |     |
   | 2      | RUN:cpu        | READY          | 1   |     |
   | 3      | RUN:cpu        | READY          | 1   |     |
   | 4      | RUN:cpu        | READY          | 1   |     |
   | 5      | DONE           | RUN:io         | 1   |     |
   | 6      | DONE           | BLOCKED        |    |   1  |
   | 7      | DONE           | BLOCKED        |    |   1  |
   | 8      | DONE           | BLOCKED        |    |   1  |
   | 9      | DONE           | BLOCKED        |    |   1  |
   | 10     | DONE           | BLOCKED        |    |   1  |
   | 11*    | DONE           | RUN:io_done    | 1   |     |

   **Estadísticas:**
   | Métrica         | Valor         |
   |------------------|---------------|
   | Tiempo total     | 11 unidades   |
   | CPU ocupada      | 6 unidades   |
   | I/O ocupada      | 5 unidades    |
   
   Análisis:

   - El **Proceso 0** utiliza la CPU durante 4 unidades de tiempo consecutivas y finaliza.
   - El **Proceso 1** realiza una operación de I/O, lo que bloquea su ejecución durante 5 unidades de tiempo hasta que la operación de I/O se completa.
   - La CPU está ocupada durante 6 de las 11 unidades de tiempo, lo que resulta en una utilización del **54.55%**.
   - La I/O está ocupada durante 5 de las 11 unidades de tiempo, lo que resulta en una utilización del **45.45%**.
   
   En resumen, esta simulación muestra cómo el sistema operativo gestiona los procesos que realizan operaciones de I/O. Este caso ilustra un escenario ideal donde la CPU se asigna a otro proceso tan pronto como el proceso actual ha finalizado completamente, maximizando así la eficiencia del uso de los recursos del sistema.

   </details>
   <br>

3. Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why? (As always, use `-c` and `-p` to see if you were right)
   
   <details>
   <summary>Answer</summary>

   Al ejecutar el comando:

   ```bash
   python3 ./process-run.py -l 1:0,4:100
   ```

   Se observa que el Proceso 0 realiza una operación de I/O y espera a que esta finalice, mientras que el Proceso 1 utiliza la CPU para ejecutar 4 instrucciones. A continuación, se muestra cómo se distribuyen las instrucciones:

   | **Process 0**         | **Process 1**         |
   |------------------------|-------------------|
   | io         | cpu      |
   | io_done         | cpu      |
   |         |   cpu   |
   |         |   cpu   |

   Al ejecutar el comando con las banderas `-c` y `-p`:

   ```bash
   python3 ./process-run.py -l 1:0,4:100 -c -p
   ```

   Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

   | Tiempo | PID: 0         | PID: 1         | CPU | I/O |
   |--------|----------------|----------------|-----|-----|
   | 1      | RUN:io         | READY          | 1   |     |
   | 2      | BLOCKED        | RUN:cpu        | 1   |  1  |
   | 3      | BLOCKED        | RUN:cpu        | 1   |  1  |
   | 4      | BLOCKED        | RUN:cpu        | 1   |  1  |
   | 5      | BLOCKED        | RUN:cpu        | 1   |  1  |
   | 6      | BLOCKED        | DONE           |     |  1  |
   | 7*     | RUN:io_done    | DONE           | 1   |     |

   **Estadísticas:**
   | Métrica         | Valor         |
   |------------------|---------------|
   | Tiempo total     | 7 unidades   |
   | CPU ocupada      | 6 unidades   |
   | I/O ocupada      | 5 unidades    |
   
   Análisis:

   - El **Proceso 0** realiza una operación de I/O en la primera unidad de tiempo y queda bloqueado durante 5 unidades de tiempo mientras espera que la operación de I/O finalice.
   - El **Proceso 1** utiliza la CPU durante 4 unidades de tiempo consecutivas y finaliza antes de que el Proceso 0 complete su operación de I/O.
   - La CPU está ocupada durante 6 de las 7 unidades de tiempo, lo que resulta en una utilización del **85.71%**.
   - La I/O está ocupada durante 5 de las 7 unidades de tiempo, lo que resulta en una utilización del **71.43%**.

   En resumen, esta simulación muestra cómo el sistema operativo prioriza el uso de la CPU para procesos listos mientras otros procesos están bloqueados esperando operaciones de I/O. Esto asegura un uso eficiente de los recursos del sistema.

   </details>
   <br>

4. We'll now explore some of the other flags. One important flag is `-S`, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (`-l 1:0,4:100 -c -S SWITCH ON END`), one doing I/O and the other doing CPU work?
   
   <details>
   <summary>Answer</summary>
   Al ejecutar el comando:

   ```bash
   python3 process-run.py -l 1:0,4:100 -c -S SWITCH_ON_END
   ```

   Se genera la siguiente traza, que detalla el estado de los procesos en cada unidad de tiempo:

   ```bash
   python3 ./process-run.py -l 1:0,4:100 -c -S SWITCH_ON_END
   ```
   |Time    | PID: 0        | PID: 1      | CPU  | IOs | 
   |--------|--------------|------------|------|------|
   |  1     | RUN:io      | READY      |  1   |      |
   |  2     | BLOCKED     | READY      |      |  1   |
   | 3     | BLOCKED     | READY      |      |  1   |
   |  4     | BLOCKED     | READY      |      |  1   |
   |  5     | BLOCKED     | READY      |      |  1   |
   |  6     | BLOCKED     | READY      |      |  1   |
   |  7*    | RUN:io_done | READY      |  1   |      |
   |  8     | DONE        | RUN:cpu    |  1   |      |
   |  9     | DONE        | RUN:cpu    |  1   |      |
   | 10     | DONE        | RUN:cpu    |  1   |      |
   | 11     | DONE        | RUN:cpu    |  1   |      |


   ## Estadísticas

   | Métrica         | Valor |
   |----------------|-------|
   | Tiempo total   | 11 unidades |
   | CPU ocupada    | 6 unidades |
   | I/O ocupada    | 5 unidades |

   ## Análisis detallado

   - El **Proceso 0** realiza una operación de I/O en la primera unidad de tiempo y queda bloqueado durante 5 unidades de tiempo mientras espera que la operación de I/O finalice.
   - Durante este tiempo, el sistema no cambia al **Proceso 1**, ya que la bandera `SWITCH_ON_END` indica que no se debe cambiar de proceso hasta que el proceso actual haya terminado completamente.
   - Una vez que el **Proceso 0** finaliza su operación de I/O, el **Proceso 1** utiliza la CPU durante 4 unidades de tiempo consecutivas y finaliza.
   - La CPU está ocupada durante **6 de las 11 unidades de tiempo**, lo que resulta en una utilización del **54.55%**.
   - La I/O está ocupada durante **5 de las 11 unidades de tiempo**, lo que resulta en una utilización del **45.45%**.

   ## Conclusión

   Este comportamiento **no es eficiente**, ya que el sistema permanece inactivo durante el tiempo en que el **Proceso 0** está bloqueado esperando la finalización de su operación de I/O. Esto demuestra que la bandera `SWITCH_ON_END` puede llevar a un uso ineficiente de los recursos del sistema, especialmente en escenarios donde hay procesos listos para ejecutarse mientras otros están bloqueados.  

   En este caso, **permitir que el sistema cambie a otro proceso mientras uno está bloqueado** podría mejorar significativamente la utilización de la CPU y reducir el tiempo total de ejecución.


   </details>
   <br>

5. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (`-l 1:0,4:100 -c -S SWITCH ON IO`). What happens now? Use `-c` and `-p` to confirm that you are right.
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

6. One other important behavior is what to do when an I/O completes. With `-I IO RUN LATER`, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER`) Are system resources being effectively utilized?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

7. Now run the same processes, but with `-I IO RUN IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>


### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.
