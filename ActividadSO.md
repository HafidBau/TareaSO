# Administración de Memoria

## **3.1 Politica y Filosofía**

## 1. Diferencia entre fragmentación interna y externa

### **Fragmentación interna**
- **Definición**: Ocurre cuando un proceso recibe más memoria de la que necesita, dejando espacios sin usar dentro de las particiones asignadas.
- **Ejemplo**: Si un proceso necesita 6 KB pero se le asigna un bloque de 8 KB, los 2 KB restantes no se pueden usar.
- **Impacto en el rendimiento**:
  - La memoria desperdiciada dentro de las particiones asignadas reduce la cantidad de memoria disponible para otros procesos.
  - Dificulta la eficiencia global de la memoria.

### **Fragmentación externa**
- **Definición**: Aparece cuando hay suficiente memoria libre para asignar a un proceso, pero está fragmentada en bloques pequeños no contiguos.
- **Ejemplo**: Un proceso requiere 10 KB, pero los bloques libres disponibles son de 4 KB y 6 KB dispersos.
- **Impacto en el rendimiento**:
  - Puede impedir la ejecución de procesos incluso si hay suficiente memoria total.
  - Requiere técnicas adicionales como **compactación** o **swapping**, que consumen tiempo de CPU.

### Comparación en tabla

| **Aspecto**          | **Fragmentación interna**             | **Fragmentación externa**              |
|-----------------------|---------------------------------------|---------------------------------------|
| **Causa**            | Asignación de bloques mayores a los necesarios | Bloques libres dispersos en memoria   |
| **Memoria afectada** | Dentro de las particiones asignadas   | Espacio libre fuera de las particiones |
| **Solución común**   | Mejor asignación de memoria          | Compactación de memoria                |

---

## 2. Políticas de reemplazo de páginas en sistemas operativos

### Políticas principales

1. **FIFO (First-In, First-Out)**:
   - **Funcionamiento**: La página que lleva más tiempo en memoria es reemplazada.
   - **Ventajas**: Simple de implementar.
   - **Desventajas**: Puede provocar más fallos de página (anomalía de Belady).

2. **LRU (Least Recently Used)**:
   - **Funcionamiento**: Se reemplaza la página que no ha sido utilizada durante el mayor tiempo.
   - **Ventajas**: Es eficiente cuando los patrones de acceso son recientes.
   - **Desventajas**: Requiere un seguimiento detallado del uso de las páginas, aumentando la complejidad.

3. **LFU (Least Frequently Used)**:
   - **Funcionamiento**: Reemplaza la página menos utilizada en términos de frecuencia.
   - **Ventajas**: Mantiene páginas activas más tiempo en memoria.
   - **Desventajas**: Páginas usadas en el pasado pueden permanecer aunque ya no sean necesarias.

4. **OPT (Optimal Page Replacement)**:
   - **Funcionamiento**: Reemplaza la página que no se necesitará durante el mayor tiempo en el futuro.
   - **Ventajas**: Es el método más eficiente en teoría.
   - **Desventajas**: Imposible de implementar porque requiere conocer el futuro.

---

### Comparación de eficiencia

| **Política**  | **Ventajas**                          | **Desventajas**                        |
|---------------|--------------------------------------|----------------------------------------|
| **FIFO**      | Fácil de implementar                 | Ineficiente en algunos casos (Belady). |
| **LRU**       | Mejor seguimiento del uso reciente   | Complejidad en implementación.         |
| **LFU**       | Favorece las páginas más usadas      | Puede ignorar cambios en el uso reciente. |
| **OPT**       | Ideal teórico, minimiza fallos       | Impracticable en la realidad.          |

### Conclusión
- **En teoría**: La política **OPT** es la más eficiente.
- **En la práctica**: **LRU** es la mejor opción porque se adapta a los patrones de uso reales y minimiza los fallos de página, aunque requiere más recursos para su implementación.

---
# 3.2 Memoria real

### Administrador de Memoria Mediante Particiones Fijas

```python
class MemoriaFija:
    def __init__(self, tamanos_particiones):
        # Inicializa la memoria con particiones de tamaños fijos
        self.particiones = tamanos_particiones
        self.ocupada = [False] * len(tamanos_particiones)  # Inicialmente, todas las particiones están libres
        self.procesos = [None] * len(tamanos_particiones)   # Lista de procesos asignados a cada partición

    def asignar_proceso(self, proceso, tamano_proceso):
        # Intenta asignar un proceso a una partición que sea lo suficientemente grande
        for i in range(len(self.particiones)):
            if not self.ocupada[i] and self.particiones[i] >= tamano_proceso:
                self.procesos[i] = proceso
                self.ocupada[i] = True
                print(f"Proceso '{proceso}' asignado a la partición {i} de tamaño {self.particiones[i]}.")
                return True
        print(f"No hay suficiente espacio para el proceso '{proceso}'.")
        return False

    def liberar_proceso(self, proceso):
        # Libera el proceso y deja la partición vacía
        for i in range(len(self.procesos)):
            if self.procesos[i] == proceso:
                self.procesos[i] = None
                self.ocupada[i] = False
                print(f"Proceso '{proceso}' liberado de la partición {i}.")
                return True
        print(f"Proceso '{proceso}' no encontrado.")
        return False

    def mostrar_estado(self):
        # Muestra el estado actual de la memoria
        print("\nEstado actual de la memoria:")
        for i in range(len(self.particiones)):
            estado = "Ocupada" if self.ocupada[i] else "Libre"
            proceso = self.procesos[i] if self.procesos[i] else "Ninguno"
            print(f"Partición {i}: {estado}, Proceso: {proceso}")

# Crear un sistema de memoria con particiones de tamaños 4, 6, 8 y 10
memoria = MemoriaFija([4, 6, 8, 10])

# Asignar procesos a las particiones
memoria.asignar_proceso("Proceso A", 4)  # Se puede asignar a la partición 0
memoria.asignar_proceso("Proceso B", 5)  # Se puede asignar a la partición 1
memoria.asignar_proceso("Proceso C", 8)  # Se puede asignar a la partición 2
memoria.asignar_proceso("Proceso D", 10) # Se puede asignar a la partición 3
memoria.asignar_proceso("Proceso E", 7)  # No se puede asignar, no hay suficiente espacio

# Mostrar el estado de la memoria
memoria.mostrar_estado()

# Liberar un proceso
memoria.liberar_proceso("Proceso B")

# Mostrar el estado de la memoria después de liberar el proceso
memoria.mostrar_estado()
```
###  Algoritmo de *"Primera Cabida"*

```python
class Partition:
    def __init__(self, size):
        self.size = size
        self.process = None  # Almacena el proceso asignado

class Process:
    def __init__(self, name, size):
        self.name = name
        self.size = size

class MemoryManager:
    def __init__(self):
        self.partitions = []
        self.processes = []

    def add_partition(self, size):
        self.partitions.append(Partition(size))

    def add_process(self, name, size):
        self.processes.append(Process(name, size))

    def allocate_memory(self):
        for process in self.processes:
            allocated = False
            for partition in self.partitions:
                if partition.process is None and partition.size >= process.size:
                    partition.process = process
                    allocated = True
                    print(f"Proceso {process.name} asignado a partición de tamaño {partition.size}.")
                    break
            if not allocated:
                print(f"Proceso {process.name} no se pudo asignar (sin espacio disponible).")

    def display_memory(self):
        print("\nEstado de la memoria:")
        for i, partition in enumerate(self.partitions):
            if partition.process:
                print(f"Partición {i + 1} ({partition.size} KB) - Proceso: {partition.process.name} ({partition.process.size} KB)")
            else:
                print(f"Partición {i + 1} ({partition.size} KB) - Vacía")

def main():
    memory_manager = MemoryManager()

    # Definimos particiones de memoria
    memory_manager.add_partition(100)  # Partición de 100 KB
    memory_manager.add_partition(200)  # Partición de 200 KB
    memory_manager.add_partition(300)  # Partición de 300 KB

    # Definimos procesos a asignar
    memory_manager.add_process("P1", 50)   # Proceso P1 de 50 KB
    memory_manager.add_process("P2", 150)  # Proceso P2 de 150 KB
    memory_manager.add_process("P3", 250)  # Proceso P3 de 250 KB
    memory_manager.add_process("P4", 100)  # Proceso P4 de 100 KB

    # Intentamos asignar memoria
    memory_manager.allocate_memory()

    # Mostramos el estado de la memoria
    memory_manager.display_memory()

if __name__ == "__main__":
    main()
```
# 3.3 Organización de Memoria Virtual

## 1. Concepto de "Paginación" y "Segmentación"

**Paginación** y **segmentación** son dos técnicas fundamentales en la organización de la memoria virtual en sistemas operativos. Ambas permiten la gestión eficiente de la memoria, pero lo hacen de maneras diferentes.

### **Paginación**
- **Definición**: La paginación divide tanto la memoria lógica de los procesos como la memoria física en bloques de tamaño fijo llamados *páginas* y *marcos de página*, respectivamente. Cada página del proceso se asigna a un marco en la memoria física, lo que permite un uso más eficiente de la memoria y reduce la fragmentación.
- **Funcionamiento**: Los programas no necesitan saber dónde están almacenados físicamente sus datos en la memoria, ya que el sistema operativo maneja la asignación de páginas a marcos de página a través de una tabla de páginas.
  
  **Ventajas**:
  - **Reducción de la fragmentación externa**: Debido a la asignación fija de bloques de memoria, la fragmentación externa se minimiza.
  - **Mejor uso de la memoria**: La memoria se utiliza de manera más eficiente, ya que no se desperdician grandes bloques contiguos.
  
  **Desventajas**:
  - **Fragmentación interna**: Si el tamaño de la página no coincide con el tamaño de los datos, puede haber espacio no utilizado dentro de cada página.
  - **Overhead de la tabla de páginas**: El mantenimiento de la tabla de páginas requiere memoria adicional y puede impactar el rendimiento si el sistema tiene muchas páginas.

### **Segmentación**
- **Definición**: La segmentación divide la memoria en bloques de tamaño variable, llamados segmentos. Cada segmento contiene una parte lógica del proceso, como el código, los datos, y la pila.
- **Funcionamiento**: Los segmentos pueden ser de diferentes tamaños y están diseñados para reflejar la estructura lógica de un programa. Cada segmento tiene su propia tabla de segmentos, que indica su ubicación en la memoria.

  **Ventajas**:
  - **Estructura lógica**: La segmentación refleja la estructura lógica de los programas, como el código y los datos, lo que facilita la gestión y el acceso.
  - **Menos fragmentación interna**: Al ser bloques de tamaño variable, se reduce la posibilidad de que haya memoria desperdiciada dentro de los segmentos.

  **Desventajas**:
  - **Fragmentación externa**: La fragmentación externa puede ocurrir porque los segmentos son de tamaño variable y pueden dispersarse por toda la memoria.
  - **Dificultad en la asignación de memoria**: Debido a la variabilidad del tamaño de los segmentos, la asignación y liberación de memoria puede ser más compleja.

---

## 2. Programa para simular una tabla de páginas para procesos con acceso aleatorio a memoria virtual

Aquí tienes un ejemplo de cómo podrías simular una tabla de páginas para procesos con acceso aleatorio a memoria virtual en Python. Este programa generará una tabla de páginas para un proceso con páginas de tamaño fijo y permitirá el acceso aleatorio a las páginas:

```python
import random

# Simulación de un sistema de memoria virtual con paginación
class MemoriaVirtual:
    def __init__(self, num_paginas, tam_pagina):
        self.num_paginas = num_paginas
        self.tam_pagina = tam_pagina
        self.tabla_de_paginas = [random.randint(0, 1) for _ in range(num_paginas)]  # 1 para página en memoria, 0 para página en disco

    def acceder_a_pagina(self, pagina):
        if self.tabla_de_paginas[pagina] == 1:
            print(f"Acceso a la página {pagina}: ¡En memoria!")
        else:
            print(f"Acceso a la página {pagina}: ¡Falló el acceso! Página no está en memoria, se cargará desde disco.")
            # Simula cargar la página en memoria
            self.tabla_de_paginas[pagina] = 1

    def mostrar_tabla(self):
        print("Tabla de páginas:")
        for i, estado in enumerate(self.tabla_de_paginas):
            estado_memoria = "Memoria" if estado == 1 else "Disco"
            print(f"Página {i}: {estado_memoria}")

# Crear una memoria virtual con 10 páginas de tamaño 4KB
memoria_virtual = MemoriaVirtual(num_paginas=10, tam_pagina=4)

# Mostrar la tabla de páginas
memoria_virtual.mostrar_tabla()

# Acceso aleatorio a páginas
for _ in range(5):
    pagina_random = random.randint(0, 9)
    memoria_virtual.acceder_a_pagina(pagina_random)
```
   # 3.3 Organización de memoria virtual

## **Paginación**:
La **paginación** es una técnica de gestión de memoria utilizada en sistemas operativos que divide la memoria tanto física como virtual en bloques de tamaño fijo llamados **páginas** y **marcos de página**. Cada página de la memoria virtual se asigna a un marco de página en la memoria física, permitiendo una asignación no contigua de la memoria. La tabla de páginas es utilizada para realizar este mapeo entre direcciones virtuales y físicas.

### **Ventajas**:
- **Eliminación de la fragmentación externa**: No es necesario que los procesos ocupen bloques contiguos de memoria, eliminando la fragmentación externa.
- **Gestión eficiente de la memoria**: El sistema operativo puede mover páginas entre la memoria RAM y el almacenamiento secundario (como el disco duro) para optimizar el uso de la memoria física.
- **Acceso eficiente a la memoria**: Cada proceso tiene su propio espacio de direcciones virtuales, y el sistema mantiene la asignación entre las direcciones virtuales y físicas.

### **Desventajas**:
- **Fragmentación interna**: Aunque se elimina la fragmentación externa, la fragmentación interna puede ocurrir debido a que las páginas pueden no estar completamente llenas.
- **Sobrecarga de la tabla de páginas**: El manejo de las tablas de páginas puede generar una sobrecarga de memoria y tiempo de procesamiento.

## **Segmentación**:
La **segmentación** es otra técnica de gestión de memoria que divide la memoria en segmentos de tamaño variable, donde cada segmento representa una estructura lógica del programa, como el código, los datos o la pila. A diferencia de la paginación, que divide la memoria en bloques de tamaño fijo, la segmentación permite que los segmentos tengan tamaños variables.

### **Ventajas**:
- **Adaptabilidad a la estructura del programa**: Los segmentos son lógicos y están relacionados con las estructuras del programa, lo que facilita la programación y el manejo de datos.
- **Mejor manejo de la memoria**: No hay desperdicio de espacio ya que los segmentos pueden variar en tamaño, a diferencia de los bloques fijos de la paginación.

### **Desventajas**:
- **Fragmentación externa**: Al igual que en la asignación de memoria convencional, la segmentación puede sufrir fragmentación externa, ya que los segmentos pueden ser de tamaños muy diferentes y podrían dejar huecos de memoria no utilizados.
- **Mayor complejidad en la gestión**: El sistema operativo debe gestionar múltiples segmentos de diferentes tamaños, lo que puede ser más complejo que la gestión de páginas de tamaño fijo.

---

# 2. **Programa que simula una tabla de páginas para procesos con acceso aleatorio a memoria virtual**

El siguiente programa simula una tabla de páginas en un sistema de memoria virtual, permitiendo que los procesos accedan a direcciones de memoria aleatorias.

```python
import random

class TablaDePaginas:
    def __init__(self, tamano_memoria, tamano_pagina):
        # Inicializa la memoria con espacio para la cantidad de páginas posibles
        self.tamano_memoria = tamano_memoria
        self.tamano_pagina = tamano_pagina
        self.paginas = [None] * (tamano_memoria // tamano_pagina)  # Tabla de páginas

    def acceder_pagina(self, proceso_id, direccion_virtual):
        # Convierte la dirección virtual a número de página
        numero_pagina = direccion_virtual // self.tamano_pagina

        # Si la página ya está en la tabla, retorna el proceso asignado
        if self.paginas[numero_pagina] is not None:
            print(f"Acceso a la página {numero_pagina} por el proceso {self.paginas[numero_pagina]} con dirección {direccion_virtual}.")
        else:
            # Si no está en la tabla, se asigna el proceso a la página
            self.paginas[numero_pagina] = proceso_id
            print(f"Asignando página {numero_pagina} al proceso {proceso_id} con dirección {direccion_virtual}.")

    def mostrar_tabla(self):
        # Muestra el estado actual de la tabla de páginas
        print("\nEstado de la tabla de páginas:")
        for i, proceso in enumerate(self.paginas):
            estado = proceso if proceso is not None else "Libre"
            print(f"Página {i}: {estado}")

# Simulación de la tabla de páginas con un tamaño de memoria de 1024 bytes y páginas de 256 bytes
memoria_virtual = TablaDePaginas(1024, 256)

# Acceso aleatorio a la memoria por parte de varios procesos
for i in range(10):
    direccion_virtual = random.randint(0, 1023)  # Dirección aleatoria dentro del rango de la memoria virtual
    proceso_id = f"Proceso-{random.randint(1, 3)}"  # Simulamos 3 procesos
    memoria_virtual.acceder_pagina(proceso_id, direccion_virtual)

# Mostrar el estado final de la tabla de páginas
memoria_virtual.mostrar_tabla()
```
# 3.4 Administración de memoria virtual

### "Least Recently Used" (LRU)
```python
class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # Almacena las páginas
        self.order = []  # Mantiene el orden de uso de las páginas

    def access_page(self, page: int):
        if page in self.cache:
            # Si la página ya está en la caché, la movemos al final (más reciente)
            self.order.remove(page)
            self.order.append(page)
            print(f"Página {page} accedida. (ya estaba en la caché)")
        else:
            # Si la página no está en la caché
            if len(self.cache) >= self.capacity:
                # Si la caché está llena, eliminamos la página menos recientemente usada
                lru_page = self.order.pop(0)  # La primera página en la lista es la menos recientemente usada
                del self.cache[lru_page]
                print(f"Página {lru_page} eliminada de la caché.")
            
            # Agregamos la nueva página a la caché
            self.cache[page] = True
            self.order.append(page)
            print(f"Página {page} añadida a la caché.")

    def display_cache(self):
        print("Estado actual de la caché:")
        print("Páginas en caché:", list(self.cache.keys()))

def main():
    capacity = int(input("Ingrese la capacidad de la caché: "))
    lru_cache = LRUCache(capacity)

    while True:
        page = int(input("Ingrese el número de la página a acceder (o -1 para salir): "))
        if page == -1:
            break
        lru_cache.access_page(page)
        lru_cache.display_cache()

if __name__ == "__main__":
    main()
```

### Proceso de Traducción de Direcciones Virtuales a Físicas

```plaintext
+---------------------+
|  Dirección Virtual  |
|      (VA)          |
+---------------------+
          |
          v
+---------------------+
|  Dividir VA en      |
|  Número de Página   |
|  y Desplazamiento   |
+---------------------+
          |
          v
+---------------------+
|  Consultar Tabla de |
|  Páginas (Page Table)|
+---------------------+
          |
          v
+---------------------+
|  ¿Página en Memoria?|
+---------------------+
          |          |
         Yes         No
          |          |
          v          v
+---------------------+   +---------------------+
|  Obtener Dirección  |   |  Cargar Página desde |
|  Física (PA)        |   |  Disco a Memoria    |
+---------------------+   +---------------------+
          |                      |
          v                      |
+---------------------+          |
|  Actualizar Tabla   |<---------+
|  de Páginas         |
+---------------------+
          |
          v
+---------------------+
|  Dirección Física   |
|      (PA)          |
+---------------------+
```
# INTEGRACION

## Análisis de la Administración de Memoria Virtual en Linux

Linux es un sistema operativo de código abierto que utiliza un enfoque sofisticado para la administración de memoria virtual. A continuación, se describen los aspectos clave de cómo Linux gestiona la memoria virtual.

### 1. Espacio de Direcciones Virtuales

Cada proceso en Linux tiene su propio espacio de direcciones virtuales, que permite que múltiples procesos se ejecuten simultáneamente sin interferir entre sí. Esto se logra mediante la traducción de direcciones virtuales a direcciones físicas utilizando una tabla de páginas.

### 2. Páginas y Tamaño de Página

Linux utiliza un modelo de memoria basado en páginas. La memoria se divide en bloques de tamaño fijo llamados "páginas". El tamaño de página comúnmente utilizado en sistemas Linux es de 4 KB, aunque también se pueden utilizar tamaños de página más grandes (por ejemplo, 2 MB o 1 GB) en configuraciones específicas (páginas grandes o "huge pages").

### 3. Tabla de Páginas

Cada proceso tiene una tabla de páginas que mapea las direcciones virtuales a las direcciones físicas. Cuando un proceso intenta acceder a una dirección virtual, el sistema operativo consulta la tabla de páginas para determinar si la página correspondiente está en memoria física.

### 4. Manejo de Fallos de Página

Cuando un proceso intenta acceder a una página que no está en memoria (un fallo de página), el sistema operativo maneja la situación de la siguiente manera:

- **Interrupción de Fallo de Página:** Se genera una interrupción que notifica al sistema operativo que se ha producido un fallo de página.
- **Carga de Página desde Disco:** El sistema operativo busca la página en el disco (normalmente en un archivo de intercambio o "swap") y la carga en memoria.
- **Actualización de la Tabla de Páginas:** Una vez que la página se ha cargado en memoria, se actualiza la tabla de páginas para reflejar que la página ahora está disponible.

### 5. Memoria Virtual y Espacio de Intercambio (Swap)

Linux utiliza un área de intercambio (swap) en el disco para manejar situaciones en las que la memoria física está llena. Cuando la memoria RAM se llena, el sistema operativo puede mover páginas que no se están utilizando activamente desde la memoria a la partición de intercambio. Esto libera espacio en la RAM para nuevas páginas.

### 6. Gestión de Memoria y Algoritmos de Reemplazo

Linux implementa varios algoritmos de reemplazo de páginas para decidir qué páginas deben ser eliminadas de la memoria cuando se necesita espacio. Uno de los algoritmos más comunes es el **Least Recently Used (LRU)**, que elimina las páginas que no se han utilizado durante el mayor tiempo.

### 7. Protección de Memoria

El sistema operativo también implementa mecanismos de protección de memoria para evitar que un proceso acceda a la memoria de otro proceso. Esto se logra mediante el uso de bits de protección en la tabla de páginas, que indican si una página es de solo lectura o de lectura/escritura.

### 8. Asignación de Memoria

Linux utiliza un sistema de asignación de memoria que incluye funciones como `malloc` y `free` para la gestión dinámica de memoria. El sistema operativo también proporciona mecanismos para la asignación de memoria a nivel de página, lo que permite a los procesos solicitar y liberar memoria de manera eficiente.

## Simulación de Swapping de Procesos en Memoria Virtual

### ¿Qué es el swapping?
El **swapping de procesos** es una técnica utilizada en los sistemas operativos para mover procesos entre la memoria principal (RAM) y la memoria secundaria (disco duro) cuando la memoria RAM está llena. Esto permite que el sistema operativo pueda liberar espacio en RAM para ejecutar otros procesos.

### Simulación en Python
A continuación se muestra una simulación en Python de cómo se maneja el swapping de procesos en memoria virtual:

```python
import random
import time

class MemoriaVirtual:
    def __init__(self, tamano_memoria, tamano_pagina):
        # Tamaño de la memoria física (RAM) y el disco (memoria secundaria)
        self.tamano_memoria = tamano_memoria
        self.tamano_pagina = tamano_pagina
        self.memoria_fisica = [None] * (tamano_memoria // tamano_pagina)  # Memoria RAM
        self.disco = []  # Memoria secundaria (simulada como disco)
        self.procesos = {}  # Diccionario de procesos

    def cargar_proceso(self, proceso_id, tamano):
        # Calcular cuántas páginas necesita el proceso
        paginas_requeridas = (tamano + self.tamano_pagina - 1) // self.tamano_pagina
        # Si la memoria RAM tiene suficiente espacio, cargarlo allí
        if paginas_requeridas <= self.memoria_fisica.count(None):
            for i in range(len(self.memoria_fisica)):
                if self.memoria_fisica[i] is None:
                    self.memoria_fisica[i] = proceso_id
                    self.procesos[proceso_id] = "Cargado en RAM"
                    paginas_requeridas -= 1
                    if paginas_requeridas == 0:
                        break
            print(f"Proceso {proceso_id} cargado en RAM.")
        else:
            # Si no hay suficiente espacio en RAM, hacer swapping con algún proceso
            self.swap_con_proceso(proceso_id, paginas_requeridas)
    
    def swap_con_proceso(self, proceso_id, paginas_requeridas):
        # Buscar procesos para hacer swapping con el disco
        for i in range(len(self.memoria_fisica)):
            if self.memoria_fisica[i] is not None:
                proceso_que_swap = self.memoria_fisica[i]
                self.memoria_fisica[i] = proceso_id
                self.procesos[proceso_id] = "Cargado en RAM"
                self.procesos[proceso_que_swap] = "Enviado al disco"
                self.disco.append(proceso_que_swap)
                print(f"Swapping: Proceso {proceso_que_swap} movido al disco.")
                break

    def liberar_proceso(self, proceso_id):
        # Liberar proceso de la RAM
        for i in range(len(self.memoria_fisica)):
            if self.memoria_fisica[i] == proceso_id:
                self.memoria_fisica[i] = None
                self.procesos[proceso_id] = "Liberado"
                print(f"Proceso {proceso_id} liberado de la RAM.")
                break

    def mostrar_estado(self):
        # Mostrar el estado actual de la memoria RAM y el disco
        print("\nEstado actual:")
        print("Memoria RAM:", self.memoria_fisica)
        print("Disco:", self.disco)
        print("Procesos:", self.procesos)


# Crear la memoria virtual con 8 páginas de tamaño 1
memoria_virtual = MemoriaVirtual(8, 1)

# Simular la carga de procesos en la memoria virtual
memoria_virtual.cargar_proceso("P1", 2)
memoria_virtual.cargar_proceso("P2", 3)
memoria_virtual.cargar_proceso("P3", 1)
memoria_virtual.cargar_proceso("P4", 2)

# Simular el swapping
time.sleep(1)  # Pausa para simular tiempo
memoria_virtual.cargar_proceso("P5", 4)

# Liberar procesos de la RAM
time.sleep(1)
memoria_virtual.liberar_proceso("P1")

# Mostrar el estado final
memoria_virtual.mostrar_estado()
```

# Administración de Entrada/Salida

## **4.1 Dispositivos y manejadores de dispositivos**

## Diferencia entre dispositivos de bloque y dispositivos de carácter

## 1. Dispositivos de bloque y dispositivos de carácter
Los dispositivos de **bloque** y los de **carácter** son dos tipos de dispositivos que gestionan el flujo de datos en los sistemas operativos. La diferencia clave entre ellos radica en la forma en que los datos son accesados y gestionados.

### Dispositivos de bloque
Los **dispositivos de bloque** son dispositivos de almacenamiento que operan con datos en bloques. Cada bloque es una unidad de almacenamiento contigua que se puede leer o escribir de manera independiente. Los dispositivos de bloque permiten acceder a cualquier parte del dispositivo de forma aleatoria, lo que facilita la lectura y escritura de datos sin necesidad de seguir un orden secuencial.

**Ejemplos de dispositivos de bloque**:
- Discos duros (HDD)
- Unidades de estado sólido (SSD)
- CD-ROM

### Dispositivos de carácter
Los **dispositivos de carácter**, por otro lado, operan con datos de manera secuencial, es decir, los datos se procesan uno a la vez. No tienen una estructura de bloques, por lo que el acceso es secuencial y no aleatorio. Estos dispositivos permiten el flujo de datos en forma de caracteres o bytes individuales.

**Ejemplos de dispositivos de carácter**:
- Teclados
- Ratones
- Puertos de serie

### Diferencia clave
- Los **dispositivos de bloque** son adecuados para tareas de almacenamiento y permiten el acceso aleatorio a los datos.
- Los **dispositivos de carácter** son más adecuados para el manejo de flujos de datos secuenciales, como las entradas o salidas de caracteres o bytes.

---

## 2. Diseño de un programa que implemente un manejador de dispositivos sencillo para un dispositivo virtual de entrada

Un manejador de dispositivos es una parte crucial del sistema operativo que gestiona el acceso a los dispositivos de entrada y salida. Aquí, diseñaremos un programa simple en Python que simule un manejador de dispositivos para un dispositivo virtual de entrada. Este dispositivo se encargará de recibir entradas de un usuario y procesarlas de manera sencilla.

### Código en Python:

```python
class ManejadorDispositivo:
    def __init__(self):
        self.buffer = []  # Almacena las entradas del dispositivo

    def recibir_dato(self, dato):
        """Simula la recepción de datos desde el dispositivo de entrada."""
        self.buffer.append(dato)
        print(f"Datos recibidos: {dato}")

    def procesar_datos(self):
        """Procesa los datos recibidos en el buffer."""
        if self.buffer:
            dato = self.buffer.pop(0)  # Obtiene el primer dato en el buffer
            print(f"Procesando dato: {dato}")
        else:
            print("No hay datos para procesar.")

    def mostrar_buffer(self):
        """Muestra el estado actual del buffer de datos."""
        print(f"Estado actual del buffer: {self.buffer}")

# Simulando el uso del manejador de dispositivos
dispositivo = ManejadorDispositivo()

# Simulando la recepción de datos
dispositivo.recibir_dato("Tecla 1 presionada")
dispositivo.recibir_dato("Tecla 2 presionada")

# Mostrando el estado del buffer
dispositivo.mostrar_buffer()

# Procesando los datos
dispositivo.procesar_datos()
dispositivo.procesar_datos()

# Mostrando el estado del buffer después del procesamiento
dispositivo.mostrar_buffer()
```
## **4.2 Mecanismos y funciones de los manejadores de dispositivos**

## ¿Qué es una Interrupción por E/S?

La interrupción por E/S (Entrada/Salida) es un mecanismo que permite a los dispositivos de hardware (como discos duros, impresoras, teclados, etc.) notificar al sistema operativo que han completado una operación de entrada/salida. Este mecanismo es crucial para la eficiencia del sistema operativo, ya que permite que el CPU realice otras tareas mientras espera que se completen las operaciones de E/S.

## ¿Cómo la Administra el Sistema Operativo?

El sistema operativo gestiona las interrupciones por E/S mediante los siguientes pasos:

1. **Configuración de la Interrupción:**
   - Cuando un proceso solicita una operación de E/S, el sistema operativo configura el dispositivo y le indica que inicie la operación.

2. **Ejecución de la Operación de E/S:**
   - El dispositivo realiza la operación de E/S. Durante este tiempo, el CPU puede continuar ejecutando otras tareas.

3. **Generación de la Interrupción:**
   - Una vez que el dispositivo completa la operación de E/S, genera una señal de interrupción al CPU.

4. **Manejo de la Interrupción:**
   - El CPU interrumpe su ejecución actual y guarda su estado.
   - El sistema operativo identifica la fuente de la interrupción y ejecuta el manejador de interrupciones correspondiente.

5. **Restauración del Estado:**
   - Después de que el manejador de interrupciones ha completado su tarea (por ejemplo, actualizar el estado del proceso que solicitó la E/S), el CPU restaura su estado anterior y continúa la ejecución del proceso interrumpido.

## Ejemplo en Pseudocódigo

A continuación se presenta un ejemplo en pseudocódigo que simula el proceso de manejo de una interrupción por E/S:

```plaintext
// Definición de variables
Proceso procesoActual;
Dispositivo dispositivoE/S;

// Función principal
Inicio
    // Inicializar el dispositivo
    dispositivoE/S.inicializar();

    // Proceso que solicita E/S
    procesoActual = crearProceso();
    procesoActual.solicitarE/S(dispositivoE/S);

    // Simular la ejecución del proceso
    mientras (procesoActual.estado != TERMINADO) hacer
        ejecutarProceso(procesoActual);
        
        // Verificar si hay una interrupción
        si (dispositivoE/S.interrupcionPendiente()) entonces
            manejarInterrupcion();
        fin si
    fin mientras
Fin

// Función para manejar la interrupción
Función manejarInterrupcion()
    // Guardar el estado del proceso actual
    guardarEstado(procesoActual);
    
    // Identificar el dispositivo que generó la interrupción
    dispositivo = dispositivoE/S.obtenerDispositivoInterrumpido();
    
    // Ejecutar el manejador de interrupciones para el dispositivo
    si (dispositivo == dispositivoE/S) entonces
        // Actualizar el estado del proceso que solicitó E/S
        procesoActual.estado = E/S_COMPLETADA;
        procesoActual.resultado = dispositivoE/S.obtenerResultado();
    fin si
    
    // Restaurar el estado del proceso
    restaurarEstado(procesoActual);
Fin
```

## Sistema Básico de Manejo de Interrupciones

```python
import time
import threading

# Clase que representa un dispositivo de E/S
class Device:
    def __init__(self, name):
        self.name = name
        self.interrupted = False
        self.result = None

    def start_io(self):
        print(f"{self.name} started I/O operation")
        time.sleep(2)  # Simulamos una operación de E/S que tarda 2 segundos
        self.interrupted = True
        self.result = "I/O operation completed"

    def is_interrupted(self):
        return self.interrupted

    def get_result(self):
        return self.result

# Clase que representa el sistema operativo
class OperatingSystem:
    def __init__(self):
        self.devices = []
        self.interrupt_handlers = {}

    def add_device(self, device):
        self.devices.append(device)

    def register_interrupt_handler(self, device, handler):
        self.interrupt_handlers[device] = handler

    def handle_interrupt(self, device):
        handler = self.interrupt_handlers.get(device)
        if handler:
            handler(device)

    def run(self):
        while True:
            for device in self.devices:
                if device.is_interrupted():
                    self.handle_interrupt(device)
            time.sleep(0.1)  # Simulamos un bucle de espera de 0.1 segundos

# Clase que representa un proceso
class Process:
    def __init__(self, name, device):
        self.name = name
        self.device = device

    def start(self):
        print(f"{self.name} started")
        self.device.start_io()

    def handle_interrupt(self, device):
        print(f"{self.name} received interrupt from {device.name}")
        result = device.get_result()
        print(f"{self.name} received result: {result}")

# Crear dispositivos y procesos
device1 = Device("Device 1")
device2 = Device("Device 2")

process1 = Process("Process 1", device1)
process2 = Process("Process 2", device2)

# Crear sistema operativo y registrar dispositivos y manejadores de interrupciones
os = OperatingSystem()
os.add_device(device1)
os.add_device(device2)
os.register_interrupt_handler(device1, process1.handle_interrupt)
os.register_interrupt_handler(device2, process2.handle_interrupt)

# Iniciar procesos y sistema operativo
process1.start()
process2.start()
os.run()
```

## **4.3 Estructuras de datos para manejo de dispositivos**

## Diferencia entre dispositivos de bloque y dispositivos de carácter

### 1. Dispositivos de bloque y dispositivos de carácter
Los dispositivos de **bloque** y los de **carácter** son dos tipos de dispositivos que gestionan el flujo de datos en los sistemas operativos. La diferencia clave entre ellos radica en la forma en que los datos son accesados y gestionados.

### Dispositivos de bloque
Los **dispositivos de bloque** son dispositivos de almacenamiento que operan con datos en bloques. Cada bloque es una unidad de almacenamiento contigua que se puede leer o escribir de manera independiente. Los dispositivos de bloque permiten acceder a cualquier parte del dispositivo de forma aleatoria, lo que facilita la lectura y escritura de datos sin necesidad de seguir un orden secuencial.

**Ejemplos de dispositivos de bloque**:
- Discos duros (HDD)
- Unidades de estado sólido (SSD)
- CD-ROM

### Dispositivos de carácter
Los **dispositivos de carácter**, por otro lado, operan con datos de manera secuencial, es decir, los datos se procesan uno a la vez. No tienen una estructura de bloques, por lo que el acceso es secuencial y no aleatorio. Estos dispositivos permiten el flujo de datos en forma de caracteres o bytes individuales.

**Ejemplos de dispositivos de carácter**:
- Teclados
- Ratones
- Puertos de serie

### Diferencia clave
- Los **dispositivos de bloque** son adecuados para tareas de almacenamiento y permiten el acceso aleatorio a los datos.
- Los **dispositivos de carácter** son más adecuados para el manejo de flujos de datos secuenciales, como las entradas o salidas de caracteres o bytes.

---

### 2. Diseño de un programa que implemente un manejador de dispositivos sencillo para un dispositivo virtual de entrada

Un manejador de dispositivos es una parte crucial del sistema operativo que gestiona el acceso a los dispositivos de entrada y salida. Aquí, diseñaremos un programa simple en Python que simule un manejador de dispositivos para un dispositivo virtual de entrada. Este dispositivo se encargará de recibir entradas de un usuario y procesarlas de manera sencilla.

### Código en Python:

```python
class ManejadorDispositivo:
    def __init__(self):
        self.buffer = []  # Almacena las entradas del dispositivo

    def recibir_dato(self, dato):
        """Simula la recepción de datos desde el dispositivo de entrada."""
        self.buffer.append(dato)
        print(f"Datos recibidos: {dato}")

    def procesar_datos(self):
        """Procesa los datos recibidos en el buffer."""
        if self.buffer:
            dato = self.buffer.pop(0)  # Obtiene el primer dato en el buffer
            print(f"Procesando dato: {dato}")
        else:
            print("No hay datos para procesar.")

    def mostrar_buffer(self):
        """Muestra el estado actual del buffer de datos."""
        print(f"Estado actual del buffer: {self.buffer}")

# Simulando el uso del manejador de dispositivos
dispositivo = ManejadorDispositivo()

# Simulando la recepción de datos
dispositivo.recibir_dato("Tecla 1 presionada")
dispositivo.recibir_dato("Tecla 2 presionada")

# Mostrando el estado del buffer
dispositivo.mostrar_buffer()

# Procesando los datos
dispositivo.procesar_datos()
dispositivo.procesar_datos()

# Mostrando el estado del buffer después del procesamiento
dispositivo.mostrar_buffer()
```
## Manejador de Dispositivos

```python
import time
import random

# Definición de la estructura del dispositivo
class Device:
    def __init__(self, id, name):
        self.id = id
        self.name = name
        self.status = "idle"  # Estado del dispositivo (idle, busy, error)
        self.data = None

    def start_io(self):
        """Simula el inicio de una operación de E/S."""
        self.status = "busy"
        print(f"{self.name} is starting I/O operation...")
        time.sleep(random.uniform(1, 3))  # Simula un tiempo de operación de E/S
        self.data = f"Data from {self.name}"
        self.status = "idle"
        print(f"{self.name} has completed I/O operation.")

    def get_status(self):
        """Devuelve el estado del dispositivo."""
        return self.status

    def get_data(self):
        """Devuelve los datos obtenidos del dispositivo."""
        return self.data

# Clase que representa el manejador de dispositivos
class DeviceManager:
    def __init__(self):
        self.device_table = []  # Tabla de dispositivos

    def add_device(self, device):
        """Agrega un dispositivo a la tabla."""
        self.device_table.append(device)

    def start_io_operations(self):
        """Inicia operaciones de E/S en todos los dispositivos."""
        for device in self.device_table:
            if device.get_status() == "idle":
                device.start_io()

    def display_device_status(self):
        """Muestra el estado de todos los dispositivos."""
        print("\nDevice Status:")
        for device in self.device_table:
            print(f"{device.name} (ID: {device.id}) - Status: {device.get_status()}")

# Crear el manejador de dispositivos
device_manager = DeviceManager()

# Crear dispositivos y agregarlos al manejador
device1 = Device(1, "Disk Drive")
device2 = Device(2, "Printer")
device3 = Device(3, "Network Card")

device_manager.add_device(device1)
device_manager.add_device(device2)
device_manager.add_device(device3)

# Simular operaciones de E/S
for _ in range(3):  # Realizar 3 ciclos de operaciones
    device_manager.start_io_operations()
    device_manager.display_device_status()
    time.sleep(1)  # Esperar un segundo antes del siguiente ciclo

```
## **4.4 Operaciones de Entrada/Salida**

## Proceso de Lectura de un Archivo desde un Disco Magnético

### Flujo del Proceso

1. **Inicio**
2. **Solicitar la lectura del archivo**
   - El sistema operativo recibe la solicitud de lectura del archivo.
3. **Verificar la existencia del archivo**
   - Si el archivo no existe, mostrar un mensaje de error y finalizar.
   - Si el archivo existe, continuar.
4. **Localizar el archivo en el disco**
   - El sistema operativo busca la ubicación del archivo en el disco magnético.
5. **Enviar la solicitud de lectura al controlador de disco**
   - El sistema operativo envía la solicitud de lectura al controlador de disco.
6. **Esperar la finalización de la operación de E/S**
   - El controlador de disco realiza la operación de lectura.
   - Durante este tiempo, el CPU puede realizar otras tareas.
7. **Recibir los datos leídos**
   - Una vez completada la operación, el controlador de disco envía los datos al sistema operativo.
8. **Almacenar los datos en la memoria**
   - El sistema operativo almacena los datos leídos en la memoria.
9. **Finalizar la operación**
   - Mostrar los datos leídos o procesarlos según sea necesario.
10. **Fin**

### Programa Básico en Python

A continuación, se presenta un programa en Python que simula el proceso de lectura de un archivo desde un disco magnético:

```python
import time
import random

# Simulación de un disco magnético
class MagneticDisk:
    def __init__(self):
        self.files = {
            "file1.txt": "Contenido del archivo 1.",
            "file2.txt": "Contenido del archivo 2.",
            "file3.txt": "Contenido del archivo 3."
        }

    def read_file(self, filename):
        """Simula la lectura de un archivo desde el disco magnético."""
        if filename not in self.files:
            raise FileNotFoundError(f"Error: {filename} no encontrado en el disco.")
        
        print(f"Localizando {filename} en el disco...")
        time.sleep(random.uniform(1, 2))  # Simula el tiempo de búsqueda
        print(f"Enviando solicitud de lectura para {filename}...")
        time.sleep(random.uniform(1, 2))  # Simula el tiempo de lectura
        return self.files[filename]

# Función principal para simular el proceso de lectura
def read_file_from_disk(filename):
    disk = MagneticDisk()
    
    try:
        print(f"Solicitando lectura del archivo: {filename}")
        content = disk.read_file(filename)
        print(f"Datos leídos: {content}")
    except FileNotFoundError as e:
        print(e)

# Simulación de lectura de archivos
if __name__ == "__main__":
    read_file_from_disk("file1.txt")
    read_file_from_disk("file2.txt")
    read_file_from_disk("file4.txt")  # Archivo que no existe
```

## Operaciones de Entrada/Salida Asíncronas

### Primero instalamos "aiofiles"
```bash
pip install aiofiles
```
### Codigo en Python
```python
import asyncio
import aiofiles

async def write_to_file(filename, content):
    """Función asíncrona para escribir contenido en un archivo."""
    async with aiofiles.open(filename, mode='w') as file:
        await file.write(content)
        print(f"Escrito en {filename}: {content}")

async def read_from_file(filename):
    """Función asíncrona para leer contenido de un archivo."""
    async with aiofiles.open(filename, mode='r') as file:
        content = await file.read()
        print(f"Leído de {filename}: {content}")

async def main():
    filename = 'example.txt'
    
    # Escribir contenido en el archivo
    await write_to_file(filename, "Hola, este es un contenido de ejemplo.\n")
    
    # Leer contenido del archivo
    await read_from_file(filename)

# Ejecutar el programa
if __name__ == "__main__":
    asyncio.run(main())
```

## **Implementación**

## Algoritmo de Planificación de Discos "Elevator (SCAN)" y Simulación de Comunicación entre Dispositivos

### 1. Programa que implementa el algoritmo "Elevator (SCAN)"
El algoritmo **Elevator (SCAN)** es una técnica de planificación de discos que simula el movimiento de un elevador. El cabezal del disco se mueve en una dirección procesando las solicitudes hasta alcanzar el final, luego invierte su dirección.

### Implementación en Python
A continuación, se muestra un programa que implementa el algoritmo SCAN para planificación de discos:

```python
def elevator_scan(requests, head, direction, max_cylinder):
    requests.sort()
    if direction == "up":
        greater = [r for r in requests if r >= head]
        lesser = [r for r in requests if r < head]
        path = greater + lesser
    elif direction == "down":
        greater = [r for r in requests if r > head]
        lesser = [r for r in requests if r <= head]
        path = lesser[::-1] + greater[::-1]
    else:
        raise ValueError("Direction must be 'up' or 'down'")
    
    total_movement = 0
    current_position = head
    for request in path:
        total_movement += abs(request - current_position)
        current_position = request
    
    return path, total_movement

# Simulación de solicitudes y posición inicial
requests = [95, 180, 34, 119, 11, 123, 62]
initial_head = 50
direction = "up"
max_cylinder = 200

path, movement = elevator_scan(requests, initial_head, direction, max_cylinder)

print(f"Orden de ejecución: {path}")
print(f"Movimiento total del cabezal: {movement} cilindros")
```

## Simulación de un sistema con múltiples dispositivos
```python
import threading
import queue
import time

class Dispositivo:
    def __init__(self, nombre):
        self.nombre = nombre

    def procesar(self, tarea):
        print(f"{self.nombre} está procesando: {tarea}")
        time.sleep(1)  # Simula tiempo de procesamiento
        print(f"{self.nombre} completó la tarea: {tarea}")

class Sistema:
    def __init__(self):
        self.cola_tareas = queue.Queue()
        self.dispositivos = {
            "disco_duro": Dispositivo("Disco Duro"),
            "impresora": Dispositivo("Impresora"),
            "teclado": Dispositivo("Teclado"),
        }

    def agregar_tarea(self, dispositivo, tarea):
        self.cola_tareas.put((dispositivo, tarea))
        print(f"Tarea '{tarea}' añadida al {dispositivo}")

    def ejecutar(self):
        while not self.cola_tareas.empty():
            dispositivo, tarea = self.cola_tareas.get()
            if dispositivo in self.dispositivos:
                self.dispositivos[dispositivo].procesar(tarea)
            else:
                print(f"Dispositivo desconocido: {dispositivo}")

# Simulación de tareas
sistema = Sistema()
sistema.agregar_tarea("disco_duro", "Guardar archivo")
sistema.agregar_tarea("impresora", "Imprimir documento")
sistema.agregar_tarea("teclado", "Registrar entrada de usuario")

# Ejecución de tareas
sistema.ejecutar()

```

## **Avanzado**

## Optimización de Operaciones de Entrada/Salida con Memoria Caché

Los sistemas operativos modernos implementan diversas estrategias para optimizar las operaciones de entrada/salida (I/O), y una de las más efectivas es el uso de memoria caché. A continuación, se detalla cómo funciona este proceso:

### ¿Qué es la Memoria Caché?

La memoria caché es un área de almacenamiento de alta velocidad que almacena temporalmente los datos que se utilizan con más frecuencia. Se encuentra entre la CPU y la memoria principal (RAM), actuando como intermediaria para acelerar el acceso a los datos.

### Beneficios del Uso de Memoria Caché en I/O

1. **Reducción de Tiempos de Acceso**:  
   La caché permite recuperar rápidamente los datos más frecuentemente utilizados, evitando el acceso constante a la memoria principal o a dispositivos de almacenamiento más lentos.

2. **Ahorro de Ancho de Banda**:  
   Al almacenar temporalmente los datos en la caché, se reduce la cantidad de datos transferidos entre la memoria principal y los dispositivos de almacenamiento, optimizando el uso del ancho de banda disponible.

3. **Mejora del Rendimiento del Sistema**:  
   Al reducir la latencia de acceso a datos, la caché mejora el rendimiento general del sistema, permitiendo una ejecución más rápida de aplicaciones y procesos.

### Implementación de la Caché en Sistemas Operativos

1. **Caché de Disco**:  
   Los sistemas operativos utilizan caché de disco para almacenar temporalmente los datos leídos y escritos en el disco duro, minimizando los accesos directos al disco.

2. **Memoria Virtual**:  
   Permite gestionar la memoria de manera eficiente, utilizando técnicas como la paginación y la segmentación. Las porciones activas de un programa se almacenan en la memoria física, y el resto en el disco, que puede ser almacenado en caché para un acceso más rápido.

3. **Caché de Página**:  
   Almacena las páginas de memoria accedidas con frecuencia, ayudando a reducir los fallos de página y mejorando el rendimiento de acceso a la memoria.

4. **Caché de Sistema de Archivos**:  
   Los sistemas de archivos modernos emplean caché para almacenar datos accedidos con frecuencia, como directorios y metadatos de archivos. Esto agiliza las operaciones de apertura, lectura y escritura de archivos.
   
## Conclusión

El uso de memoria caché es una técnica esencial para optimizar las operaciones de entrada/salida, mejorar el rendimiento del sistema y ofrecer una experiencia de usuario más eficiente. Al minimizar los tiempos de acceso a datos y optimizar el uso del ancho de banda, la memoria caché es clave en la gestión eficaz de los recursos del sistema.


# Actividades: Dispositivos de entrada y salida en Linux

# Actividad 1: Listar dispositivos conectados

## 1. ¿Qué tipos de dispositivos se muestran en la salida de `lsblk`?
El comando `lsblk` lista los dispositivos de bloque, tales como discos duros, SSDs, particiones y unidades USB conectadas. La salida muestra información sobre:
- El nombre de los dispositivos (por ejemplo, `sda`, `sdb`, `nvme0n1`).
- El tipo de dispositivo (por ejemplo, `disk`, `part` para particiones).
- El tamaño de los dispositivos.
- El punto de montaje, si está disponible (por ejemplo, `/mnt`, `/home`).

En resumen, `lsblk` muestra la estructura de almacenamiento en el sistema, tanto a nivel de discos como de particiones.

## 2. ¿Cuál es la diferencia entre `lsusb` y `lspci`?
- **`lsusb`**: Lista los dispositivos conectados a los puertos USB de tu computadora. Muestra dispositivos como teclados, ratones, impresoras, cámaras, unidades USB, entre otros. Cada dispositivo USB conectado tendrá un identificador único, con el fabricante y el ID del producto.
  
- **`lspci`**: Muestra los dispositivos conectados al bus PCI de la computadora. Esto incluye tarjetas gráficas, tarjetas de red, controladores de almacenamiento, entre otros dispositivos internos que están conectados a través de la interfaz PCI (Peripheral Component Interconnect). La salida incluye detalles como el fabricante y el tipo de dispositivo conectado.

**Diferencia principal**: `lsusb` se enfoca en dispositivos conectados a puertos USB, mientras que `lspci` lista dispositivos conectados a través de la interfaz PCI, que son típicamente internos.

## 3. ¿Qué información adicional proporciona `dmesg | grep usb`?
El comando `dmesg` muestra los mensajes del kernel de Linux, que incluyen información sobre el arranque del sistema, controladores y hardware. Al ejecutar `dmesg | grep usb`, se filtran los mensajes que están relacionados con dispositivos USB. Esto puede incluir:
- Información sobre el reconocimiento de dispositivos USB al conectarse.
- Mensajes de errores o advertencias relacionadas con dispositivos USB.
- Carga de controladores para dispositivos USB.
- Detalles sobre la velocidad de conexión (por ejemplo, USB 2.0, 3.0, etc.).

En resumen, este comando proporciona información detallada sobre los eventos del kernel relacionados con la conexión y operación de dispositivos USB.

## Actividad 2: Verificar dispositivos de almacenamiento

### 1. ¿Qué dispositivos de almacenamiento están conectados a su sistema?
El comando `fdisk -l` lista todos los discos y sus particiones. La salida típicamente muestra información como:
- Los dispositivos de almacenamiento conectados (por ejemplo, `/dev/sda`, `/dev/sdb`, `/dev/nvme0n1`).
- El tamaño de cada disco.
- La tabla de particiones de cada disco (por ejemplo, GPT o MBR).
  
A partir de esta salida, puedes identificar los discos duros, SSDs y otros dispositivos de almacenamiento conectados a tu sistema.

### 2. ¿Qué particiones están montadas actualmente?
El comando `df -h` muestra los dispositivos de almacenamiento montados en el sistema, así como el espacio disponible en cada uno. La salida incluye:
- Las particiones montadas (por ejemplo, `/dev/sda1`, `/dev/nvme0n1p1`).
- El punto de montaje correspondiente (por ejemplo, `/`, `/home`, `/mnt`).
- El espacio total, utilizado y disponible de cada partición.

Con esta información, puedes identificar qué particiones están montadas y dónde están ubicadas en tu sistema de archivos.

### 3. ¿Qué tipo de sistemas de archivos se usan en las particiones?
El comando `blkid` muestra los identificadores UUID y los tipos de sistema de archivos de las particiones. La salida incluye:
- El UUID de cada partición.
- El tipo de sistema de archivos (por ejemplo, `ext4`, `ntfs`, `vfat`, `xfs`).
  

## Actividad 3: Explorar dispositivos de entrada

### 1. ¿Qué eventos genera cada dispositivo al interactuar con ellos?
Al ejecutar `evtest`, se pueden monitorear los eventos generados por dispositivos de entrada como teclados, ratones y otros. Los tipos de eventos que generan son:

- **Teclado**: El teclado genera eventos relacionados con la pulsación y liberación de teclas. Estos eventos se representan con códigos numéricos para cada tecla presionada o liberada. Por ejemplo, al presionar una tecla, se genera un evento `EV_KEY` con el código de la tecla correspondiente (por ejemplo, `KEY_A` para la tecla 'A'). También puede haber eventos de repetición si la tecla se mantiene presionada.
  
- **Mouse (Ratón)**: El ratón genera eventos relacionados con el movimiento y los clics. Los eventos más comunes son:
  - `EV_REL` para el movimiento relativo del ratón (ejes X y Y).
  - `EV_KEY` para los clics de los botones del ratón (por ejemplo, botones izquierdo, derecho y medio).
  
- **Controladores USB adicionales**: Otros dispositivos USB, como controladores de juegos o cámaras, también generan eventos. Estos pueden variar según el dispositivo, pero comúnmente incluyen eventos de botones o movimiento, y en el caso de las cámaras, podrían generar eventos relacionados con la activación o desactivación de la cámara.

#### Resumen de los eventos:
- **Teclado**: `EV_KEY` (presión y liberación de teclas).
- **Ratón**: `EV_REL` (movimiento), `EV_KEY` (clics).
- **Controladores USB adicionales**: Eventos de botones, movimiento u otros específicos de cada dispositivo.

### 2. ¿Cómo se identifican los dispositivos en `/proc/bus/input/devices`?
El archivo `/proc/bus/input/devices` contiene información sobre los dispositivos de entrada conectados al sistema. La salida muestra detalles como:
- **Nombre del dispositivo**: Indica el nombre del dispositivo de entrada (por ejemplo, "AT Translated Set 2 keyboard" para un teclado).
- **ID del dispositivo**: Un identificador único para cada dispositivo de entrada.
- **Tipo de dispositivo**: El tipo de dispositivo de entrada (por ejemplo, `keyboard`, `mouse`, `misc`).
- **Eventos soportados**: Los eventos que el dispositivo puede generar, como `EV_KEY`, `EV_REL`, entre otros.
- **Archivo de dispositivo**: El archivo de dispositivo asociado con el dispositivo de entrada (por ejemplo, `/dev/input/eventX`).

En la salida de este archivo, cada dispositivo de entrada está listado con su información asociada, y se puede identificar por el nombre y los tipos de eventos que soporta. Los dispositivos generalmente están separados por bloques de texto, y cada dispositivo tiene una serie de atributos que permiten reconocer su tipo y función.

## Actividad 4: Examinar dispositivos de salida

### 1. ¿Qué salidas de video están disponibles en su sistema?
El comando `xrandr` lista las pantallas conectadas y sus resoluciones. La salida típicamente incluye:
- **Conexiones de video disponibles**: Por ejemplo, `HDMI-1`, `eDP-1`, `VGA-1`.
- **Resoluciones soportadas**: Cada salida de video tendrá una lista de resoluciones soportadas, como `1920x1080`, `1280x720`, entre otras.
- **Estado de las pantallas**: El comando muestra si las pantallas están activas o desconectadas, junto con su resolución actual.

Por ejemplo, si tienes una pantalla conectada a través de HDMI y una laptop con pantalla integrada, podrías ver algo como:
**HDMI-1 connected 1920x1080+0+0 (normal left inverted right) 344mm x 194mm eDP-1 connected 1366x768+0+0 (normal left inverted right) 287mm x 159mm**


Esto indica que tienes una pantalla conectada a través de HDMI con una resolución de 1920x1080 y la pantalla integrada de la laptop con una resolución de 1366x768.

#### 2. ¿Qué dispositivos de sonido se detectaron?
El comando `aplay -l` lista las tarjetas de sonido disponibles en el sistema. La salida muestra:
- **Tarjetas de sonido**: Cada tarjeta de sonido conectada al sistema, con un identificador numérico (por ejemplo, `card 0`, `card 1`).
- **Dispositivos de salida**: Cada tarjeta tendrá sus respectivos dispositivos de salida (por ejemplo, `device 0`, `device 1`).
  
Un ejemplo de salida podría ser:
**List of PLAYBACK Hardware Devices card 0: HDMI [HDA ATI HDMI], device 3: HDMI 0 [HDMI 0] Subdevices: 1/1 Subdevice #0: subdevice #0 card 1: PCH [HDA Intel PCH], device 0: ALC892 Analog [ALC892 Analog] Subdevices: 1/1 Subdevice #0: subdevice #0**

Esto muestra dos tarjetas de sonido:
1. **card 0**: Una tarjeta de sonido HDMI (`HDA ATI HDMI`) con salida en el dispositivo `HDMI 0`.
2. **card 1**: Una tarjeta de sonido integrada en la placa base (`HDA Intel PCH`) con un dispositivo de salida analógico (`ALC892 Analog`).

#### 3. ¿Qué procesos están usando la tarjeta de sonido?
El comando `lsof /dev/snd/*` muestra los procesos que están utilizando los dispositivos de sonido en tu sistema. La salida incluye:
- **Procesos activos**: Muestra qué procesos están utilizando dispositivos de sonido en `/dev/snd/`.
- **PID de los procesos**: Cada proceso tiene un identificador de proceso (PID).
- **Archivo de dispositivo de sonido utilizado**: Los archivos correspondientes a los dispositivos de sonido, como `/dev/snd/pcmC0D0p` (para la tarjeta de sonido 0).

Por ejemplo, la salida podría ser algo como:
**COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME firefox 1234 user 50u CHR 116,10 0t0 12345 /dev/snd/pcmC0D0p**


## Actividad 5: Crear un script de resumen

### 1. ¿Qué ventajas tiene usar un script para recopilar esta información?  
- **Automatización**: Permite ejecutar múltiples comandos de forma automática, ahorrando tiempo y evitando errores humanos.  
- **Consistencia**: La información se recopila siempre en el mismo formato y orden.  
- **Registro de información**: La salida guardada en un archivo facilita su análisis posterior.  
- **Reutilización**: Se puede ejecutar el mismo script en diferentes equipos.  
- **Simplicidad**: Basta con un solo comando (`bash dispositivos.sh`) para ejecutar todas las tareas.  

### 2. ¿Qué cambios realizaría para personalizar el script?  
**Añadir fecha y hora**:  
   ```bash
   echo "Fecha y Hora:" >> resumendispositivos.txt  
   date >> resumendispositivos.txt  
   ```
## Actividad 6: Reflexión y discusión

### 1. **Versatilidad y Adopción Generalizada**
- **Amplia adopción**: Linux se utiliza en una variedad de dispositivos, desde servidores y estaciones de trabajo hasta dispositivos embebidos y smartphones.
- **Crecimiento del mercado**: Se proyecta que el mercado de Linux crecerá de USD 6.27 mil millones en 2022 a USD 22.15 mil millones para 2029, lo que resalta la necesidad de una gestión adecuada de dispositivos para mantenerse competitivo.

### 2. **Desafíos de la Fragmentación**
- **Ecosistema fragmentado**: Linux cuenta con múltiples distribuciones (distros) que requieren diferentes configuraciones y métodos de gestión, lo que puede aumentar la carga de trabajo del personal de TI.
- **Soluciones de gestión unificada**: Herramientas como Hexnode Unified Endpoint Management (UEM) permiten gestionar diversas distribuciones desde una única plataforma, simplificando la administración y mejorando la seguridad.

### 3. **Mejora de la Seguridad**
- **Políticas de seguridad**: La gestión de dispositivos permite implementar políticas de seguridad consistentes, como la gestión de contraseñas y restricciones de acceso a características del dispositivo.
- **Soporte para configuraciones avanzadas**: Las soluciones de gestión permiten personalizar configuraciones específicas que se alinean con las necesidades operativas de la organización.

### 4. **Facilidad de Mantenimiento y Actualización**
- **Actualizaciones remotas**: La capacidad de desplegar actualizaciones de sistema operativo de manera remota ayuda a mantener los dispositivos seguros y actualizados, reduciendo vulnerabilidades.
- **Automatización de tareas**: La ejecución de scripts personalizados y la automatización de tareas de gestión mejoran la eficiencia operativa.

### 5. **Soporte y Resolución de Problemas**
- **Acciones remotas**: La gestión de dispositivos permite a los administradores solucionar problemas de forma remota, lo que reduce el tiempo de inactividad y mejora la experiencia del usuario.
- **Asistencia técnica**: Proveedores de soluciones de gestión ofrecen soporte técnico dedicado, lo que facilita la resolución de problemas y la implementación de soluciones.

### 6. **Cumplimiento Normativo**
- **Regulaciones y estándares**: La gestión de dispositivos ayuda a las organizaciones a cumplir con regulaciones y estándares de seguridad, garantizando que los dispositivos estén configurados y gestionados de acuerdo con las políticas de la empresa.

### 7. **Optimización de Recursos**
- **Centralización de la gestión**: La gestión unificada de dispositivos permite a las organizaciones optimizar el uso de recursos, reduciendo la necesidad de múltiples herramientas y procesos.
- **Aumento de la productividad**: Al simplificar la gestión de dispositivos, los equipos de TI pueden centrarse en tareas más estratégicas, mejorando la productividad general.

## Comandos de Entrada y Salida, Discos y Archivos

### Ejercicio 1: Montar y Desmontar Discos

#### **Objetivo**  
Aprender a montar y desmontar un dispositivo externo.

---

#### **Pasos a seguir**

1. **Inserta una memoria USB en el sistema.**

2. **Encuentra el dispositivo usando el siguiente comando:**

   ```bash
   lsblk
    ```
    o
    ```bash
    fdisk -l
    ```
3. **Monta la memoria USB en un directorio, por ejemplo, `/mnt/usb:`**
     ```bash
    sudo mount /dev/sdX1 /mnt/usb
    ```

4. **Verifica que esté montado correctamente:**
    ```bash
    df -h
    ```

5. **Copia un archivo desde tu directorio personal al dispositivo USB:**
    ```bash
    cp archivo.txt /mnt/usb/
    ```

6. **Desmonta la memoria USB:**
     ```bash
    sudo umount /mnt/usb
    ```

### **Conclusión**  
Este ejercicio te enseña a identificar, montar, utilizar y desmontar dispositivos de almacenamiento externos en un sistema Linux mediante comandos básicos. Dominar estos pasos es fundamental para la gestión eficiente de discos y archivos.


## Ejercicio 2: Redirección de Entrada y Salida
### Objetivo: Usar redirección para guardar la salida de comandos en archivos.**

1. **Lista los archivos de tu directorio actual y guarda el resultado en un archivo `listado.txt`:**
    ```bash
          ls -l > listado.txt  
    ```
2. **Muestra el contenido del archivo en la terminal:**
    ```bash
        cat listado.txt  
    ```
3. **Añade la fecha actual al final del archivo:**
     ```bash
        date >> listado.txt  
    ```
4. **Muestra todo el contenido del archivo nuevamente:**
     ```bash
      cat listado.txt  
    ```
## Ejercicio 3: Copiar y Mover Archivos

### Objetivo
Practicar cómo copiar y mover archivos y directorios en un sistema Linux.

### Pasos

1. **Crear un archivo de texto**
Primero, vamos a crear un archivo de texto llamado `archivo1.txt` utilizando el comando `echo`:

```bash
echo "Este es un archivo de prueba" > archivo1.txt
```
    Explicación:
- `echo "Este es un archivo de prueba"`: Este comando imprime el texto entre comillas.
- `>:` Este operador redirige la salida del comando al archivo `archivo1.txt` . Si el archivo no existe, se creará; si ya existe, se sobrescribirá.


2. **Copiar el archivo a otro directorio**
Copia el archivo `archivo1.txt` al `directorio /tmp:`
    ```bash
    cp archivo1.txt /tmp/
    ```
3. **Paso 3: Renombrar el archivo copiado**
Renombra el archivo copiado a `archivo2.txt` en el directorio `/tmp`:
    ```bash
       mv /tmp/archivo1.txt /tmp/archivo2.txt
    ```
    Explicación:
- `mv`: Este comando se utiliza para mover o renombrar archivos y directorios.
- `/tmp/archivo1.txt`: Es la ruta del archivo que deseas renombrar.
- `/tmp/archivo2.txt`: Es el nuevo nombre del archivo.

4. **Mover el archivo de vuelta a tu directorio actual**
Mueve el archivo `archivo2.txt` de vuelta a tu directorio actual:

```bash
mv /tmp/archivo2.txt .
```
Explicación:
- `.`: Este símbolo representa el directorio actual. Al usarlo como destino, estás indicando que deseas mover el archivo al directorio en el que te encuentras actualmente.
## Ejercicio 4: Comprimir y Descomprimir Archivos
### Objetivo
Aprender a trabajar con la compresión de archivos en un sistema Linux.

---

### Pasos

#### 1. **Crear un directorio llamado `backup`**
Primero, crea un directorio llamado `backup` y copia algunos archivos en él. Utiliza el siguiente comando:

```bash
mkdir backup
```
Explicación:
- `mkdir backup`: Este comando crea un nuevo directorio llamado backup.

#### 2. **Copiar algunos archivos al directorio `backup`**
Copia archivos existentes al directorio recién creado. Por ejemplo, si tienes un archivo llamado archivo1.txt, usa el siguiente comando:

```bash
cp archivo1.txt backup/
```
Explicación:
- `cp archivo1.txt backup/`: Este comando copia el archivo archivo1.txt al directorio backup.

#### 3. **Comprimir el directorio backup en un archivo .`tar.gz`**
Comprime el directorio backup en un archivo llamado backup.tar.gz con el siguiente comando:

```bash
tar -czvf backup.tar.gz backup/
```
Explicación:
- tar: Comando utilizado para crear y manipular archivos tar.
- `-c`: Crea un nuevo archivo tar.
- `-z`: Comprime el archivo utilizando gzip.
- `-v`: Muestra el progreso del proceso en la terminal (modo verbose).
- `-f`: Especifica que el siguiente argumento es el nombre del archivo tar a crear.
backup/: Es el directorio que deseas comprimir.

#### 4. **Borrar el directorio original**
Una vez comprimido el directorio, elimina el directorio original backup con el siguiente comando:

```bash
rm -r backup
```
Explicación:
- `rm -r backup`: Este comando elimina de forma recursiva el directorio backup y todo su contenido. Ten cuidado al usar este comando, ya que elimina los archivos permanentemente.

#### 5. Extraer el contenido del archivo comprimido
Extrae el contenido del archivo comprimido backup.tar.gz con este comando:

```bash
tar -xzvf backup.tar.gz
```
Explicación:
- `-x`: Extrae el contenido del archivo tar.
- `-z`: Indica que el archivo está comprimido con gzip.
- `-v`: Muestra el progreso del proceso en la terminal (modo verbose).
- `-f`: Especifica que el siguiente argumento es el nombre del archivo tar a extraer.

## Ejercicio 5: Permisos y Propiedades de Archivos

### Objetivo
Aprender a modificar los permisos y propietarios de archivos en un sistema Linux.

---

### Pasos

#### 1. Crear un archivo llamado `privado.txt`
Crea un archivo vacío llamado `privado.txt` utilizando el comando `touch`:

```bash
touch privado.txt
```
Explicación:
- `touch privado.txt`: Este comando crea un nuevo archivo vacío llamado privado.txt. Si el archivo ya existe, simplemente actualiza su fecha de modificación.

#### 2. Cambiar los permisos del archivo
Cambia los permisos del archivo para que solo el propietario pueda leer y escribir. Usa el siguiente comando:

```bash
chmod 600 privado.txt
```
Explicación:
- `chmod`: Comando para cambiar los permisos de acceso a archivos y directorios.
- `600`: Establece los permisos:
    - `6` (lectura y escritura) para el propietario.
    - `0` (sin permisos) para el grupo.
    - `0` (sin permisos) para otros usuarios.
- `privado.txt:` Es el archivo al que se le están modificando los permisos.

#### 3. Cambiar el propietario del archivo
Si tienes privilegios de superusuario, puedes cambiar el propietario del archivo a otro usuario con el siguiente comando:

```bash
sudo chown usuario privado.txt
```
Explicación:
- sudo: Ejecuta el comando con privilegios de superusuario.
- chown: Cambia el propietario de un archivo o directorio.
- `usuario`: Reemplázalo con el nombre del usuario al que deseas transferir la propiedad del archivo.
- `privado.txt`: Es el archivo cuyo propietario deseas cambiar.

## Ejercicio 6: Exploración de Dispositivos

### Objetivo
Identificar discos y particiones en el sistema.


### Comandos y Explicaciones

#### 1.  Listar discos y particiones
Usa el siguiente comando para listar todos los discos y particiones conectados a tu sistema:

```bash
lsblk
```
Explicación:

lsblk: Muestra una lista de todos los dispositivos de bloque (discos y particiones) en el sistema.
Incluye información como:
Nombre del dispositivo.
Tamaño.
Tipo (partición, disco, etc.).
Punto de montaje (si está montado).

#### 2. Ver el tamaño del contenido en un directorio
Para conocer el tamaño total del contenido de un directorio específico, ejecuta:

```bash
du -sh /ruta/directorio
```
Explicación:
- `du`: Estima el uso del espacio en disco de archivos y directorios.
- `-s`: Muestra solo el tamaño total del directorio (no desglosa subdirectorios).
- `-h`: Formatea el tamaño en unidades legibles para humanos (KB, MB, GB).
- `/ruta/directorio`: Ruta del directorio cuyo tamaño deseas verificar.

#### 3. Verificar el uso del disco
Para revisar el uso del disco en todo el sistema, utiliza:

```bash
df -h
```
Explicación:
- `df`: Muestra el uso del espacio en disco de los sistemas de archivos montados.
- `-h`: Presenta la información en un formato legible para humanos (KB, MB, GB).

## Ejercicio 7: Crear y Formatear Particiones

### Objetivo
Aprender a crear y formatear una nueva partición utilizando un disco no particionado. Este ejercicio es ideal para un entorno de práctica, como una máquina virtual.

---

### Pasos Detallados

#### 1. Identificar un disco no particionado
Primero, lista todos los discos y particiones disponibles en tu sistema:

```bash
sudo fdisk -l
```
Explicación:

- `sudo`: Ejecuta el comando con privilegios de superusuario.
- `fdisk -l`: Muestra todos los discos y sus particiones. Busca un disco sin particiones, como /dev/sdX.

#### 2. Crear una nueva partición
Utiliza fdisk para crear una nueva partición en el disco identificado (reemplaza `sdX` con el identificador del disco):

```bash
sudo fdisk /dev/sdX
```
Dentro de la interfaz de `fdisk`:

- *Presiona* `n`: Para crear una nueva partición.
- *Selecciona* `p`: Para una partición primaria.
- *Elige el número de partición*: Generalmente 1 para la primera partición.
- A*cepta los valores predeterminados*: Para el primer y último sector (o ajusta el tamaño según necesites).
- *Presiona* `w`: Para escribir los cambios y salir.

#### 3. Formatear la partición como ext4
Formatea la partición recién creada como **ext4** (reemplaza `sdX1` con la partición correcta):

```bash
sudo mkfs.ext4 /dev/sdX1
```
Explicación:

- `mkfs.ext4`: Crea un sistema de archivos ext4 en la partición especificada.
- `/dev/sdX1`: Es la nueva partición que acabas de crear.

#### 4. Montar la partición en un directorio
Crea un directorio para montar la partición y realiza el montaje:

```bash
sudo mkdir /mnt/nueva_particion
sudo mount /dev/sdX1 /mnt/nueva_particion
```
Explicación:

- `mkdir /mnt/nueva_particion`: Crea un directorio en `/mnt` para montar la partición.
- `mount /dev/sdX1 /mnt/nueva_particion`: Monta la partición en el directorio creado.

#### 5. Probar escribiendo archivos en la partición
Escribe un archivo de prueba en la partición montada:

```bash
echo "Prueba de escritura" > /mnt/nueva_particion/test.txt
```
Explicación:
- `echo "Prueba de escritura"`: Genera el texto que deseas escribir.
 `/mnt/nueva_particion/test.txt`: Guarda el texto en un archivo dentro de la nueva partición.
