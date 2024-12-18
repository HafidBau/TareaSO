# SISTEMAS DE ARCHIVOS
##  Ejercicio 1: Concepto y noción de archivo real y virtual

### 1. ¿Qué es un Archivo Real?
Un **archivo real** es un objeto que contiene programas, datos o cualquier otro elemento que se almacena de manera persistente en un sistema de archivos. A diferencia de los archivos virtuales, que son temporales y se utilizan durante la ejecución de procesos, los archivos reales son aquellos que permanecen en el sistema incluso después de que los procesos que los utilizaron han finalizado.

#### Características de un Archivo Real:
- **Persistencia**: Los archivos reales se almacenan en dispositivos de almacenamiento (como discos duros o SSD) y no se eliminan automáticamente al finalizar un proceso.
- **Tamaño Variable**: El tamaño de un archivo real puede variar dependiendo de la cantidad de datos que contenga.
- **Acceso Directo**: Los archivos reales pueden ser accedidos directamente por los usuarios y aplicaciones, permitiendo operaciones como lectura, escritura y modificación.

### 2. Ejemplos de Archivos Reales
- **Documentos**: Archivos de texto, hojas de cálculo, presentaciones, etc.
- **Imágenes y Videos**: Archivos multimedia que se almacenan en formatos como JPEG, PNG, MP4, etc.
- **Ejecutables**: Programas y scripts que pueden ser ejecutados por el sistema operativo.

### 3. Comparación con Archivos Virtuales
- **Archivos Virtuales**: Son temporales y se utilizan durante la ejecución de un sistema. Por ejemplo, archivos con extensión `.tmp` que se crean para almacenar datos temporales y se eliminan al finalizar el proceso.
- **Archivos Reales**: Permanecen en el sistema y pueden ser utilizados en cualquier momento, incluso después de que el proceso que los creó haya terminado.

### 4. Importancia en Sistemas Operativos
La gestión de archivos reales es fundamental en los sistemas operativos, ya que:
- **Almacenamiento de Datos**: Permiten a los usuarios y aplicaciones almacenar y recuperar información de manera eficiente.
- **Organización**: Facilitan la organización de datos en estructuras jerárquicas (carpetas y subcarpetas).
- **Seguridad**: Los sistemas operativos implementan mecanismos de control de acceso para proteger los archivos reales de accesos no autorizados.

## Ejercicio 2: Componentes de un sistema de archivos

### 1. Componentes Clave de un Sistema de Archivos

#### **Metadatos**
- Información sobre los archivos, como el nombre, tamaño, tipo, permisos, y fechas de creación/modificación.
- Permiten al sistema operativo gestionar y acceder a los archivos de manera eficiente.

#### **Tablas de Asignación**
- Estructuras que mantienen un registro de cómo se almacenan los datos en el disco.
- Ejemplos incluyen la tabla de inodos en EXT4 y la Master File Table (MFT) en NTFS.

#### **Inodos**
- En sistemas de archivos como EXT4, un inodo es una estructura que almacena información sobre un archivo o directorio, excluyendo su nombre y contenido.
- Contiene metadatos como permisos, propietario, y punteros a los bloques de datos.

#### **Bloques de Datos**
- La unidad básica de almacenamiento en un sistema de archivos.
- Los archivos se dividen en bloques, que son las unidades que se leen y escriben en el disco.

#### **Directorio**
- Estructura que contiene referencias a archivos y otros directorios.
- Permite la organización jerárquica de los archivos en el sistema.

### 2. Comparación entre EXT4 y NTFS

| Componente            | EXT4 (Linux)                                      | NTFS (Windows)                                   |
|----------------------|---------------------------------------------------|--------------------------------------------------|
| **Metadatos**        | Almacena información en inodos.                   | Utiliza la MFT para almacenar metadatos.         |
| **Tablas de Asignación** | Utiliza una tabla de inodos para gestionar bloques. | MFT actúa como tabla de asignación, con entradas para cada archivo. |
| **Inodos**           | Cada archivo tiene un inodo que almacena metadatos. | No utiliza inodos; la MFT cumple esta función.  |
| **Bloques de Datos** | Soporta tamaños de bloque de hasta 4 KB.          | Soporta tamaños de bloque de hasta 64 KB.       |
| **Directorio**       | Estructura jerárquica con directorios y subdirectorios. | Estructura jerárquica similar, pero con características avanzadas. |
| **Journaling**       | Soporta journaling para recuperación ante fallos. | También soporta journaling, mejorando la integridad de datos. |
| **Compatibilidad**   | Principalmente en sistemas Linux.                  | Utilizado en sistemas Windows y compatible con Linux. |

### 3. Ventajas y Desventajas

#### **EXT4**
- **Ventajas**:
  - Rendimiento superior en operaciones de lectura/escritura.
  - Soporte para archivos grandes y sistemas de archivos de gran tamaño.
  - Eficiente en la gestión de espacio y recuperación ante fallos.

- **Desventajas**:
  - Menos compatibilidad con sistemas no Linux.
  - Puede ser más complejo de gestionar en comparación con NTFS.

#### **NTFS**
- **Ventajas**:
  - Alta compatibilidad con sistemas Windows y soporte para características avanzadas como compresión y cifrado.
  - Mejor gestión de permisos y seguridad a nivel de archivo.

- **Desventajas**:
  - Rendimiento inferior en sistemas Linux en comparación con EXT4.
  - Puede ser más susceptible a la fragmentación en comparación con EXT4.


## Ejercicio 3: Organización lógica y física de archivos

El sistema de archivos EXT4 (Fourth Extended File System) es una evolución de sus predecesores (EXT2 y EXT3) y se utiliza comúnmente en sistemas operativos Linux. A continuación, se describen los componentes clave y la organización lógica de archivos en EXT4.

### 1. **Estructura General del Sistema de Archivos EXT4**

#### **Superbloque**
- **Descripción**: Contiene información crítica sobre el sistema de archivos, como el tamaño del bloque, el número total de bloques, el número de inodos, y las características del sistema de archivos.
- **Ubicación**: Se encuentra en el primer bloque del sistema de archivos y tiene copias de seguridad en otros bloques.

#### **Inodos**
- **Descripción**: Cada archivo y directorio en EXT4 está asociado con un inodo, que almacena metadatos sobre el archivo, como permisos, propietario, y punteros a los bloques de datos.
- **Cantidad**: La cantidad de inodos se define al crear el sistema de archivos y determina cuántos archivos se pueden almacenar.

#### **Bloques de Datos**
- **Descripción**: La unidad básica de almacenamiento en EXT4. Los archivos se dividen en bloques, que son las unidades que se leen y escriben en el disco.
- **Tamaño**: El tamaño de bloque predeterminado es de 4 KB, aunque se pueden usar tamaños de bloque más pequeños o más grandes.

#### **Grupos de Bloques**
- **Descripción**: EXT4 organiza los bloques en grupos de bloques, que son conjuntos de bloques contiguos. Cada grupo de bloques contiene su propio bitmap de bloques y bitmap de inodos.
- **Ventajas**: Esta organización mejora la eficiencia de la lectura y escritura al mantener los datos relacionados físicamente cerca unos de otros.

### 2. **Organización Lógica de Archivos**

#### **Estructura Jerárquica**
- **Descripción**: EXT4 utiliza una estructura de directorios jerárquica, donde los archivos y directorios se organizan en una estructura de árbol.
- **Directorio Raíz**: El directorio raíz (`/`) es el punto de partida de esta jerarquía, y todos los demás archivos y directorios se organizan a partir de él.

#### **Entradas de Directorio**
- **Descripción**: Cada directorio contiene entradas que apuntan a inodos de archivos y subdirectorios. Estas entradas incluyen el número de inodo, la longitud del registro, el tipo de archivo y el nombre del archivo.
- **Formato**: EXT4 utiliza un formato de entrada de directorio que permite almacenar información sobre el tipo de archivo, facilitando la búsqueda y el acceso.

#### **Árboles de Extensiones**
- **Descripción**: EXT4 utiliza un sistema de árboles de extensiones para gestionar la asignación de bloques. Esto permite que un solo inodo apunte a múltiples bloques de datos contiguos, mejorando la eficiencia del almacenamiento.
- **Ventajas**: Reduce la fragmentación y mejora el rendimiento al acceder a archivos grandes.

### 3. **Journaling**
- **Descripción**: EXT4 implementa un sistema de journaling que registra cambios en un diario antes de aplicarlos al sistema de archivos. Esto ayuda a prevenir la corrupción de datos en caso de fallos del sistema.
- **Modos de Journaling**: EXT4 soporta varios modos de journaling, incluyendo el modo de solo metadatos y el modo de datos completos.

### 4. **Ventajas de la Organización Lógica en EXT4**
- **Rendimiento**: La organización en grupos de bloques y el uso de árboles de extensiones mejoran el rendimiento de lectura y escritura.
- **Recuperación**: El journaling proporciona una mayor seguridad y recuperación de datos en caso de fallos.
- **Escalabilidad**: EXT4 puede manejar volúmenes y archivos de gran tamaño, lo que lo hace adecuado para una amplia gama de aplicaciones.

## Ejercicio 4: Mecanismos de acceso a los archivos


### 1. Define los diferentes mecanismos de acceso.

   - **Acceso Secuencial:** Los archivos se leen o escriben en orden, uno tras otro. Este método es simple y eficiente para lecturas/escrituras completas o casi completas de archivos.
   - **Acceso Directo:** Los archivos se acceden directamente en una posición específica. Ideal para situaciones donde se necesita un acceso rápido y no necesariamente en orden secuencial.
   - **Acceso Indexado:** Utiliza un índice para mantener una referencia de posiciones en el archivo. Permite acceso directo a registros específicos de una manera más organizada que el acceso directo puro.

### 2. Escribe un pseudocódigo que muestre cómo acceder a:
   
   - **Un archivo secuencialmente:**
     ```pseudo
     Abrir archivo en modo lectura
     Mientras no sea el fin del archivo
         Leer un registro
         Procesar el registro
     Cerrar archivo
     ```
   
   - **Un archivo directamente mediante su posición:**
     ```pseudo
     Abrir archivo en modo lectura
     Definir la posición a acceder (pos)
     Moverse a la posición 'pos' en el archivo
     Leer el registro en la posición 'pos'
     Procesar el registro
     Cerrar archivo
     ```
   
   - **Un archivo utilizando un índice:**
     ```pseudo
     Abrir archivo de índice en modo lectura
     Leer índice para encontrar posición del registro deseado
     Abrir archivo de datos en modo lectura
     Moverse a la posición obtenida del índice
     Leer el registro en la posición indicada
     Procesar el registro
     Cerrar archivos
     ```

### 3. Compara las ventajas de cada mecanismo dependiendo del caso de uso.

   - **Acceso Secuencial:**
     - **Ventajas:**
       - Simplicidad en la implementación.
       - Buen rendimiento para lecturas/escrituras completas.
     - **Desventajas:**
       - Ineficiente para acceso aleatorio.

   - **Acceso Directo:**
     - **Ventajas:**
       - Acceso rápido a cualquier posición.
       - Ideal para archivos grandes donde solo se necesita acceder a ciertas partes.
     - **Desventajas:**
       - Puede ser más complicado de implementar.

   - **Acceso Indexado:**
     - **Ventajas:**
       - Combina lo mejor del acceso secuencial y directo.
       - Facilita búsquedas rápidas y acceso a datos específicos.
     - **Desventajas:**
       - Requiere mantenimiento del índice.
       - Puede ser costoso en términos de espacio y procesamiento.

## Ejercicio 5: Modelo jerárquico y mecanismos de recuperación en caso de falla

### 1. Diseña un modelo jerárquico para un sistema de archivos con al menos tres niveles de directorios.

   ```text
   /home
       /usuario
           /documentos
               /trabajo
               /personal
           /imágenes
               /vacaciones
               /eventos
           /música
               /rock
               /pop
```

### 2. Simula una falla en un directorio específico y describe los pasos necesarios para recuperarlo.

#### Escenario de Falla:  
Supongamos que el directorio `/home/usuario/documentos/trabajo` se ha corrompido.

---

#### Pasos para Recuperarlo:

**Paso 1: Identificar la falla**  
Usar herramientas como `fsck` (File System Check) para identificar y verificar la corrupción en el sistema de archivos.

```bash
sudo fsck /dev/sdXN
```
**Paso 2: Restaurar desde una copia de seguridad**
Si se dispone de una copia de seguridad reciente, utilizarla para restaurar el directorio corrupto.
```bash
cp -r /backup/usuario/documentos/trabajo /home/usuario/documentos/
```
**Paso 3: Recuperar datos individuales**
Usar herramientas de recuperación de archivos, como `testdisk` o `photorec`, para intentar recuperar archivos individuales que se encuentren en el directorio corrupto.
```bash
sudo testdisk
sudo photorec
```

# Protección y Seguridad

## Ejercicio 1: Concepto y objetivos de protección y seguridad
### 1. **Definiciones**

#### Protección
- **Definición**: Se refiere a los mecanismos que restringen el acceso a recursos y datos a usuarios y procesos autorizados. La protección asegura que solo los usuarios con permisos adecuados puedan acceder a ciertos recursos del sistema.
- **Objetivo**: Prevenir el acceso no autorizado y el uso indebido de los recursos del sistema.

#### Seguridad
- **Definición**: En un contexto más amplio, la seguridad abarca todas las medidas diseñadas para proteger el sistema contra amenazas, tanto externas como internas. Esto incluye la protección contra ataques maliciosos, virus, y accesos no autorizados.
- **Objetivo**: Salvaguardar el sistema y los datos de actividades maliciosas, asegurando la confidencialidad, integridad y disponibilidad de la información.

### 2. **Objetivos Principales de Protección y Seguridad**

#### Confidencialidad
- **Definición**: Asegurar que la información sensible sea accesible solo para usuarios autorizados.
- **Mecanismos**: Autenticación de usuarios, cifrado de datos y control de acceso.

#### Integridad
- **Definición**: Mantener la precisión y consistencia de los datos a lo largo de su ciclo de vida.
- **Mecanismos**: Validación de datos, uso de checksums y técnicas de hashing.

#### Disponibilidad
- **Definición**: Asegurar que los usuarios autorizados tengan acceso a la información y recursos cuando los necesiten.
- **Mecanismos**: Redundancia, copias de seguridad y mantenimiento regular del sistema.

#### Responsabilidad
- **Definición**: Asegurar que las acciones realizadas en el sistema puedan ser rastreadas hasta el usuario o proceso responsable.
- **Mecanismos**: Registro de actividades y auditoría de eventos.

#### No Repudio
- **Definición**: Asegurar que un usuario no pueda negar haber realizado una acción específica.
- **Mecanismos**: Firmas digitales y registros de auditoría.

#### Protección Contra Amenazas
- **Definición**: Salvaguardar el sistema contra diversas amenazas, incluyendo malware, accesos no autorizados y violaciones de datos.
- **Mecanismos**: Software antivirus, firewalls y sistemas de detección de intrusiones (IDS).

### 3. **Ejemplo Práctico**

Un ejemplo práctico de cómo se aplican estos objetivos en un sistema operativo es el uso de **control de acceso basado en roles (RBAC)** en un sistema Linux:

- **Confidencialidad**: Solo los usuarios con el rol adecuado pueden acceder a archivos sensibles, como registros financieros.
- **Integridad**: Se utilizan permisos de archivo para evitar que usuarios no autorizados modifiquen archivos críticos del sistema.
- **Disponibilidad**: Se implementan copias de seguridad automáticas para garantizar que los datos estén disponibles incluso en caso de fallos del sistema.
- **Responsabilidad**: Se registran todas las acciones de los usuarios en un archivo de log, permitiendo auditorías posteriores.
- **No Repudio**: Se utilizan firmas digitales para validar la autenticidad de documentos importantes.

## Ejercicio 2: Clasificación aplicada a la seguridad

### Clasificaciones Comunes de Seguridad

### 1. **Seguridad Física**  
   Se refiere a las medidas tomadas para proteger el hardware del sistema operativo de daños físicos, robos o accesos no autorizados.

   #### **Papel en la Protección del Sistema Operativo**  
   La seguridad física garantiza que los dispositivos que ejecutan el sistema operativo estén protegidos contra amenazas que puedan dañar su funcionamiento, como manipulaciones físicas o desastres ambientales.

   #### **Ejemplos Prácticos**  
   - **Control de acceso físico:** Uso de cerraduras, tarjetas de acceso y vigilancia en áreas restringidas donde se encuentran servidores o equipos críticos.  
   - **Protección contra incendios:** Instalación de sistemas de detección y extinción de incendios.  
   - **Protección contra robo:** Uso de dispositivos de anclaje físico para equipos portátiles.  

---

### 2. **Seguridad Lógica**  
   Consiste en proteger los datos y procesos del sistema operativo contra accesos no autorizados, ataques y errores lógicos.  

   #### **Papel en la Protección del Sistema Operativo**  
   La seguridad lógica protege el sistema operativo a nivel de software, garantizando la integridad, confidencialidad y disponibilidad de los datos.

   #### **Ejemplos Prácticos**  
   - **Autenticación de usuarios:** Uso de contraseñas seguras, autenticación multifactor (MFA) o biometría.  
   - **Cifrado de datos:** Herramientas como **BitLocker** (Windows) o **LUKS** (Linux) para proteger información sensible.  
   - **Antivirus y antimalware:** Software como **Windows Defender** o **ClamAV** para detectar y eliminar amenazas.  
   - **Control de privilegios:** Uso de políticas de usuarios y permisos para restringir el acceso a archivos críticos del sistema.  

---

### 3. **Seguridad de Red**  
   Implica proteger el sistema operativo de amenazas que provienen de conexiones externas o internas a través de redes.

   #### **Papel en la Protección del Sistema Operativo**  
   La seguridad de red evita que usuarios malintencionados o ataques externos comprometan el sistema a través de vulnerabilidades en la red.

   #### **Ejemplos Prácticos**  
   - **Firewalls:** Herramientas como **iptables** (Linux) o **Windows Firewall** para controlar el tráfico de red entrante y saliente.  
   - **Detección de intrusos:** Uso de sistemas IDS/IPS como **Snort** o **Suricata** para detectar accesos sospechosos.  
   - **VPN (Red Privada Virtual):** Uso de herramientas como **OpenVPN** para cifrar conexiones remotas y proteger la transferencia de datos.  
   - **Filtrado de contenido:** Configuración de servidores proxy para restringir accesos no autorizados a sitios web o servicios.  
---

## Ejercicio 3: Funciones del sistema de protección

### Funciones de un Sistema de Protección

Un sistema de protección en un entorno multiusuario tiene como objetivo garantizar la seguridad de los recursos del sistema y controlar el acceso a los mismos para prevenir accesos no autorizados, manipulación indebida o daños. Para ello, se utilizan varias funciones clave que permiten gestionar de manera eficiente el acceso y las acciones de los usuarios.

---

#### 1. **Control de Acceso a los Recursos**

El control de acceso es fundamental para un sistema de protección, ya que asegura que solo los usuarios autorizados puedan acceder a recursos como archivos, dispositivos, aplicaciones, etc.

- **Mecanismo de Control de Acceso:**  
  Utiliza modelos como **control de acceso basado en roles (RBAC)** o **control de acceso discrecional (DAC)** para restringir el acceso a recursos según el rol o la autorización explícita del propietario de los recursos.

- **Ejemplo:**  
  En un sistema multiusuario, un usuario con privilegios de administrador puede acceder a todos los archivos del sistema, mientras que un usuario estándar solo tiene acceso a su propio directorio.

---

#### 2. **Autenticación**

La autenticación es el proceso de verificar la identidad de un usuario antes de permitirle el acceso a los recursos del sistema.

- **Métodos de Autenticación:**  
  - **Contraseña:** La forma más común, donde el usuario debe proporcionar una clave secreta.  
  - **Autenticación multifactor (MFA):** Utiliza múltiples factores, como una contraseña y un código enviado por mensaje de texto.  
  - **Biometría:** El uso de características físicas como huellas dactilares o reconocimiento facial.

- **Ejemplo:**  
  En un entorno multiusuario, al iniciar sesión en el sistema, un usuario debe proporcionar su nombre de usuario y contraseña. Si es correcto, se le concede acceso. En un entorno más seguro, puede requerirse un código de autenticación que se envía a su dispositivo móvil (MFA).

---

#### 3. **Autorización**

La autorización es el proceso mediante el cual se decide si un usuario autenticado tiene permisos para realizar una operación en un recurso determinado.

- **Modelos de Autorización:**  
  - **Control de acceso basado en roles (RBAC):** Los usuarios se asignan a roles y los roles tienen permisos asociados.
  - **Control de acceso basado en atributos (ABAC):** Permisos definidos según atributos de usuario, como grupo o ubicación.
  
- **Ejemplo:**  
  Un usuario con el rol de "administrador" puede modificar configuraciones del sistema, mientras que un usuario con el rol de "usuario estándar" solo puede leer archivos en su directorio personal.

---

#### 4. **Auditoría**

La auditoría es el proceso de registrar y analizar las actividades de los usuarios en el sistema para garantizar que se sigan las políticas de seguridad y para detectar actividades sospechosas.

- **Registro de Eventos:**  
  Los sistemas de protección deben mantener registros detallados de todas las actividades de acceso, modificaciones y fallos de seguridad.
  
- **Ejemplo:**  
  Si un usuario intenta acceder a un archivo al que no tiene permiso, se debe generar un registro de evento que detalle el intento fallido.

---

### Caso Práctico: Sistema de Protección en Acción

**Escenario:**
Un entorno multiusuario donde varios empleados acceden a los recursos de una empresa. El sistema de protección gestiona la seguridad mediante autenticación, autorización y auditoría.

**Paso 1: Autenticación**  
- El empleado "Juan" inicia sesión en su cuenta en el sistema proporcionando su nombre de usuario y su contraseña.  
- El sistema verifica la contraseña contra la base de datos y, si es correcta, permite el acceso.

**Paso 2: Autorización**  
- Después de autenticarse, "Juan" intenta acceder al directorio de "Recursos Financieros".  
- El sistema revisa si "Juan" tiene permisos para acceder a este directorio. Como "Juan" es un usuario estándar, no tiene acceso, por lo que el sistema deniega la solicitud.

**Paso 3: Auditoría**  
- El sistema genera un registro de evento detallado que indica que "Juan" intentó acceder a un directorio al que no tenía permisos.
- El registro incluye la hora, el tipo de intento y la dirección IP desde la que se hizo el intento.

---

## Ejercicio 4: Implantación de matrices de acceso
### Matriz de Acceso y Control de Acceso en un Sistema Operativo

#### Matriz de Acceso

#### Usuarios y Recursos

- **Usuarios:**
  - U1: Alice
  - U2: Bob
  - U3: Charlie

- **Recursos:**
  - R1: Archivo de Finanzas
  - R2: Archivo de Recursos Humanos
  - R3: Archivo de Proyectos
  - R4: Archivo de Informes

#### Matriz de Acceso

|          | R1 (Finanzas) | R2 (Recursos Humanos) | R3 (Proyectos) | R4 (Informes) |
|----------|----------------|-----------------------|----------------|----------------|
| **U1** (Alice)  | Lectura        | No Acceso            | Lectura        | Escritura      |
| **U2** (Bob)    | Escritura      | Lectura              | No Acceso      | Lectura        |
| **U3** (Charlie) | No Acceso      | Escritura            | Lectura        | No Acceso      |

### Explicación de la Matriz de Acceso

La matriz de acceso es una herramienta que se utiliza para definir y controlar los permisos de acceso de los usuarios a los recursos en un sistema operativo. Cada celda de la matriz indica el tipo de acceso que un usuario tiene a un recurso específico. Los tipos de acceso pueden incluir:

- **Lectura:** El usuario puede ver el contenido del recurso.
- **Escritura:** El usuario puede modificar o agregar contenido al recurso.
- **No Acceso:** El usuario no tiene permiso para acceder al recurso.

La matriz de acceso permite a los administradores del sistema gestionar de manera efectiva quién puede hacer qué con cada recurso, lo que ayuda a proteger la integridad y la confidencialidad de los datos.

### Escenario Simulado

#### Escenario:
Supongamos que **Charlie** (U3) intenta acceder al **Archivo de Finanzas** (R1).

#### Proceso de Acceso:
1. **Solicitud de Acceso:** Charlie envía una solicitud al sistema operativo para acceder al Archivo de Finanzas.
2. **Verificación de Permisos:** El sistema operativo consulta la matriz de acceso para determinar los permisos de Charlie sobre el Archivo de Finanzas.
3. **Consulta de la Matriz:**
   - En la matriz, se encuentra que la celda correspondiente a Charlie (U3) y el Archivo de Finanzas (R1) indica "No Acceso".

#### Resultado:
- **Bloqueo de Acceso:** Debido a que Charlie no tiene permiso para acceder al Archivo de Finanzas, el sistema operativo bloquea la solicitud y le muestra un mensaje de error, como "Acceso denegado: no tiene permiso para acceder a este recurso".

Este proceso asegura que los usuarios solo puedan acceder a los recursos para los cuales tienen permisos, protegiendo así la seguridad y la integridad del sistema.

## Ejercicio 5: Protección basada en el lenguaje

### Cómo los lenguajes de programación pueden implementar mecanismos de protección.

#### Concepto
La protección basada en el lenguaje se refiere a las características y mecanismos que un lenguaje de programación proporciona para gestionar el acceso a la memoria y los recursos. Esto incluye la gestión de la memoria, el control de acceso a los datos y la prevención de errores comunes, como desbordamientos de búfer y accesos a memoria no válida.

Los lenguajes que implementan protección basada en el lenguaje suelen incluir características como:

- **Gestión automática de memoria:** Para evitar fugas de memoria y accesos a memoria liberada.
- **Tipos de datos seguros:** Para garantizar que los datos se utilicen de manera coherente y segura.
- **Verificación de límites:** Para prevenir accesos fuera de los límites de los arreglos y estructuras de datos.

### Ejemplo: Protección en Java

#### Mecanismos de Protección en Java
Java es un lenguaje que implementa varios mecanismos de protección:

1. **Gestión Automática de Memoria:** Java utiliza un recolector de basura (Garbage Collector) que se encarga de liberar la memoria no utilizada, lo que reduce el riesgo de fugas de memoria y accesos a memoria liberada.

2. **Verificación de Límites:** Java verifica automáticamente los límites de los arreglos. Si un programa intenta acceder a un índice fuera de los límites de un arreglo, se lanza una excepción `ArrayIndexOutOfBoundsException`, evitando así accesos no autorizados.

3. **Modelo de Seguridad:** Java tiene un modelo de seguridad que permite definir políticas de acceso a recursos, como archivos y redes, a través de un sistema de permisos.

#### Ejemplo de Código en Java
```java
public class EjemploArreglo {
    public static void main(String[] args) {
        int[] numeros = {1, 2, 3};

        // Intento de acceso fuera de límites
        try {
            System.out.println(numeros[5]); // Esto lanzará una excepción
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Acceso fuera de límites: " + e.getMessage());
        }
    }
}
```

## Ejercicio 6: Validación y Amenazas al Sistema

### Descripción
Los sistemas operativos son vulnerables a diversas amenazas que pueden comprometer su seguridad y la integridad de los datos. Es fundamental entender estas amenazas y los mecanismos de validación que se pueden implementar para prevenirlas.

### Amenazas Comunes a un Sistema Operativo

#### 1. Malware
El malware es un software malicioso diseñado para infiltrarse, dañar o robar información de un sistema. Existen diferentes tipos de malware, como virus, gusanos, troyanos y ransomware. El malware puede propagarse a través de correos electrónicos, descargas de software y sitios web comprometidos.

#### 2. Ataques de Fuerza Bruta
Los ataques de fuerza bruta son intentos sistemáticos de adivinar contraseñas o claves de cifrado mediante la prueba de todas las combinaciones posibles. Este tipo de ataque puede ser efectivo si las contraseñas son débiles o si no se implementan mecanismos de bloqueo tras varios intentos fallidos.

#### 3. Inyección de Código
La inyección de código es una técnica utilizada por los atacantes para insertar código malicioso en una aplicación o sistema. Esto puede ocurrir a través de formularios web, donde un atacante introduce código SQL malicioso para manipular bases de datos o ejecutar comandos en el servidor.

### Mecanismos de Validación

#### Autenticación Multifactor (MFA)
La autenticación multifactor es un mecanismo de seguridad que requiere que los usuarios proporcionen dos o más formas de verificación antes de acceder a un sistema. Esto puede incluir:

- **Algo que sabes:** Contraseña o PIN.
- **Algo que tienes:** Un token de seguridad, un teléfono móvil o una tarjeta inteligente.
- **Algo que eres:** Huellas dactilares, reconocimiento facial o escaneo de iris.

La MFA aumenta significativamente la seguridad, ya que incluso si un atacante obtiene la contraseña de un usuario, necesitaría el segundo factor para acceder al sistema.

#### Control de Integridad
El control de integridad se refiere a los mecanismos que aseguran que los datos y los archivos no han sido alterados de manera no autorizada. Esto puede incluir:

- **Sumas de verificación (checksums):** Códigos generados a partir de los datos que permiten verificar su integridad.
- **Firmas digitales:** Usadas para autenticar la fuente de los datos y asegurar que no han sido modificados.
- **Monitoreo de archivos:** Herramientas que detectan cambios en archivos críticos del sistema y alertan a los administradores.

### Esquema de Validación para un Sistema Operativo con Múltiples Usuarios

#### Diseño del Esquema de Validación

1. **Registro de Usuario:**
   - Los nuevos usuarios deben registrarse proporcionando información básica y creando una contraseña segura.
   - Se recomienda el uso de políticas de contraseñas que incluyan longitud mínima, complejidad y caducidad.

2. **Autenticación Inicial:**
   - Al iniciar sesión, el usuario debe ingresar su nombre de usuario y contraseña.
   - Implementar un mecanismo de bloqueo tras varios intentos fallidos (por ejemplo, bloquear la cuenta durante 15 minutos después de 5 intentos fallidos).

3. **Autenticación Multifactor:**
   - Después de la autenticación inicial, se requerirá un segundo factor (por ejemplo, un código enviado por SMS o una aplicación de autenticación) para acceder al sistema.

4. **Control de Acceso:**
   - Implementar un sistema de roles y permisos que limite el acceso a recursos y funciones según el rol del usuario (por ejemplo, administrador, usuario estándar, invitado).

5. **Monitoreo y Auditoría:**
   - Registrar todas las actividades de inicio de sesión y acceso a recursos críticos.
   - Realizar auditorías periódicas para detectar accesos no autorizados o actividades sospechosas.

6. **Control de Integridad:**
   - Implementar sumas de verificación y monitoreo de archivos para detectar cambios no autorizados en archivos críticos del sistema.

#### Diagrama del Esquema de Validación

```plaintext
[Registro de Usuario] --> [Autenticación Inicial] --> [Autenticación Multifactor] --> [Acceso al Sistema]
        |                          |                             |
        |                          |                             |
        |                          |                             |
        v                          v                             v
[Políticas de Contraseña]   [Bloqueo de Cuenta]         [Control de Acceso]
        |                          |                             |
        |                          |                             |
        v                          v                             v
[Monitoreo y Auditoría]   [Control de Integridad]       [Registro de Actividades]
```

## Ejercicio 7: Cifrado

### Cifrado Simétrico y Asimétrico

#### Cifrado Simétrico
El cifrado simétrico es un método de cifrado en el que se utiliza la misma clave tanto para cifrar como para descifrar la información. Esto significa que tanto el emisor como el receptor deben conocer y mantener en secreto la clave. Los algoritmos de cifrado simétrico son generalmente más rápidos y eficientes, pero la gestión de claves puede ser un desafío.

**Ejemplos de algoritmos de cifrado simétrico:**
- AES (Advanced Encryption Standard)
- DES (Data Encryption Standard)
- Blowfish

#### Cifrado Asimétrico
El cifrado asimétrico, también conocido como cifrado de clave pública, utiliza un par de claves: una clave pública y una clave privada. La clave pública se utiliza para cifrar la información, mientras que la clave privada se utiliza para descifrarla. Este método permite que la clave pública sea compartida abiertamente, mientras que la clave privada se mantiene en secreto.

**Ejemplos de algoritmos de cifrado asimétrico:**
- RSA (Rivest-Shamir-Adleman)
- DSA (Digital Signature Algorithm)
- ECC (Elliptic Curve Cryptography)

### Ejemplo Práctico de Cifrado en Sistemas Operativos

#### Cifrado Simétrico: AES
Un ejemplo práctico de cifrado simétrico en un sistema operativo es el uso de AES para cifrar archivos en un disco. Muchas herramientas de cifrado de disco completo, como BitLocker en Windows o FileVault en macOS, utilizan AES para proteger los datos almacenados en el disco.

#### Cifrado Asimétrico: RSA
Un ejemplo de cifrado asimétrico en sistemas operativos es el uso de RSA para la autenticación y el intercambio seguro de claves. Por ejemplo, cuando un usuario se conecta a un servidor a través de SSH (Secure Shell), se puede utilizar RSA para cifrar la sesión y autenticar al usuario.

### Simulación del Proceso de Cifrado y Descifrado de un Archivo

#### Suposiciones
- Usaremos el cifrado simétrico con el algoritmo AES.
- La clave utilizada para el cifrado y descifrado será "clave_secreta_123".

#### Código de Ejemplo en Python

A continuación, se presenta un ejemplo de cómo cifrar y descifrar un archivo utilizando la biblioteca `cryptography` en Python.

##### Instalación de la Biblioteca
Primero, asegúrate de tener instalada la biblioteca `cryptography`. Puedes instalarla usando pip:

```bash
pip install cryptography
```
#### Código de Cifrado y Descifrado
```python
from cryptography.fernet import Fernet

# Generar una clave a partir de una contraseña
def generate_key(password):
    return Fernet.generate_key()

# Cifrar un archivo
def encrypt_file(file_name, key):
    fernet = Fernet(key)
    with open(file_name, 'rb') as file:
        original = file.read()
    encrypted = fernet.encrypt(original)
    with open(file_name, 'wb') as encrypted_file:
        encrypted_file.write(encrypted)

# Descifrar un archivo
def decrypt_file(file_name, key):
    fernet = Fernet(key)
    with open(file_name, 'rb') as encrypted_file:
        encrypted = encrypted_file.read()
    decrypted = fernet.decrypt(encrypted)
    with open(file_name, 'wb') as decrypted_file:
        decrypted_file.write(decrypted)

# Ejemplo de uso
if __name__ == "__main__":
    # Generar una clave
    password = "clave_secreta_123"
    key = generate_key(password.encode())

    # Cifrar el archivo
    encrypt_file('archivo.txt', key)
    print("Archivo cifrado.")

    # Descifrar el archivo
    decrypt_file('archivo.txt', key)
    print("Archivo descifrado.")
```
#### Conclusiones
El proceso de cifrado y descifrado se realizó con éxito utilizando el algoritmo AES a través de la biblioteca cryptography en Python.
La clave de cifrado es esencial para el proceso y debe ser manejada de manera segura.
El cifrado simétrico permite proteger la información, asegurando que solo aquellos con la clave adecuada puedan acceder a los datos originales.