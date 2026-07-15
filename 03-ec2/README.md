# 🖥️ EC2 - Elastic Compute Cloud

---

## Introducción

EC2 es el servicio de cómputo de AWS. Permite lanzar instancias con el sistema
operativo, capacidad de cómputo y configuración de red que necesites, con la
flexibilidad de escalarlos hacia arriba o hacia abajo según la demanda.

---

## Contenido

- [Conceptos clave](#1-conceptos-clave)
- [Lanzar una instancia](#2-lanzar-una-instancia)
- [Conexión SSH](#3-conexión-ssh)
- [Administración básica](#4-administración-básica)
- [Buenas prácticas](#5-buenas-prácticas)



## 1. Conceptos clave

Antes de lanzar tu primer servidor en la nube, es fundamental entender las piezas que lo componen:

### 1.1 Instancia
Es el servidor virtual en la nube. Su capacidad de procesamiento y memoria depende del **tipo de instancia** que elijas.

### 1.2 AMI (Amazon Machine Image)
Es la plantilla que define el sistema operativo y el software inicial de tu servidor (por ejemplo: Ubuntu Server, Amazon Linux 3, Windows Server).

### 1.3 Familias y Tipos de Instancia
AWS categoriza sus instancias según el caso de uso. Se nombran con una combinación de letras y números (p. ej., `t3.micro`):

| Familia | Tipo | Optimizado para | Ejemplo común |
|---------|------|-----------------|---------------|
| **t3 / t2** | General | Balance de cómputo, memoria y red. Ideal para desarrollo y pruebas. | `t3.micro` (Apto para Free Tier) |
| **c6g / c5** | Cómputo | Procesadores de alto rendimiento. Ideal para servidores web de alto tráfico. | `c6g.large` |
| **r6g / r5** | Memoria | Cargas de trabajo que procesan grandes volúmenes de datos en memoria. | `r5.large` |

### 1.4 Key Pairs (Pares de Claves)
Es el método de autenticación para conectarte de forma segura a tu instancia por SSH. Se compone de:
*   **Clave pública:** AWS la guarda en el servidor.
*   **Clave privada:** Un archivo (generalmente `.pem`) que descargas en tu computadora. **Nunca la compartas ni la subas a tu repositorio.**

### 1.5 Security Groups (Grupos de Seguridad)
Actúan como un **firewall virtual** que controla el tráfico entrante y saliente de tu instancia. Por defecto, todo el tráfico entrante está bloqueado a menos que crees una regla que lo permita explícitamente.

---

## 2. Lanzar una instancia

Sigue estos pasos para desplegar tu primer servidor virtual bajo la capa gratuita (Free Tier).

### Paso 1: Ir al Panel de EC2
1. Inicia sesión con tu usuario IAM administrativo.
2. En la barra de búsqueda superior, escribe **EC2** y selecciona el servicio.
3. Haz click en el botón naranja **Launch instance** (Lanzar instancia).

### Paso 2: Nombre y Etiquetas (Tags)
1. En **Name**, escribe un nombre descriptivo para identificar tu servidor (p. ej., `servidor-web-dev`).
2. *(Opcional)* Agrega etiquetas adicionales como `Environment: dev` o `Project: tutorial` para mantener el orden.

### Paso 3: Selección de Sistema Operativo (AMI)
1. En la sección **Application and OS Images**, selecciona **Ubuntu**.
2. Asegúrate de que en el menú desplegable esté seleccionada una versión con la etiqueta **"Free tier eligible"** (elegible para la capa gratuita), por ejemplo, *Ubuntu Server 24.04 LTS*.

### Paso 4: Tipo de Instancia
1. En **Instance type**, busca y selecciona **`t2.micro`** (o **`t3.micro`**, dependiendo de la región en la que te encuentres) que tenga la etiqueta **"Free tier eligible"**.

### Paso 5: Configurar Key Pair
1. En **Key pair (login)**, haz click en **Create new key pair**.
2. Dale un nombre descriptivo (p. ej., `mi-clave-aws`).
3. Selecciona el formato **`.pem`** (si usas Linux, macOS o PowerShell en Windows) o **`.ppk`** (si usas Putty en versiones antiguas de Windows).
4. Haz click en **Create key pair** y guarda el archivo descargado en un lugar seguro.

> ⚠️ **Crítico:** Si pierdes este archivo `.pem`, no podrás volver a conectarte a tu servidor por SSH. AWS no guarda una copia de tu clave privada.

### Paso 6: Configuración de Red y Security Group
Aquí defines las reglas de acceso al servidor:

1. En la sección **Network settings**, selecciona **Create security group**.
2. Configura las reglas del firewall:

| Tipo de Tráfico | Puerto | Origen (Source) | Propósito / Recomendación |
|-----------------|--------|-----------------|---------------------------|
| **SSH** | 22 | **My IP** (Tu IP pública) | ⚠️ **Seguridad:** Nunca uses `0.0.0.0/0` (Anywhere) para SSH. Esto expone tu puerto de administración a todo internet. |
| **HTTP** | 80 | **Anywhere** (`0.0.0.0/0`) | Permite que cualquier persona en internet pueda ver tu sitio web si instalas un servidor web. |

### Paso 7: Lanzar la Instancia
1. Deja la configuración de almacenamiento por defecto (8 GB o 30 GB de tipo gp3/gp2 son suficientes para pruebas y entran en la capa gratuita).
2. En el panel lateral derecho, revisa el resumen y haz click en **Launch instance**.
3. ¡Listo! En un par de minutos, el estado de tu instancia pasará de *Pending* a *Running*.


## 3. Conexión SSH

Una vez que tu instancia esté en estado **Running**, puedes conectarte a ella de forma remota utilizando la terminal y la clave privada (`.pem`) que descargaste previamente.

### 3.1 Configura los permisos de tu clave privada (Linux / macOS)

Por seguridad, SSH exige que tu archivo de clave privada tenga permisos sumamente restrictivos. Si el archivo es accesible por otros usuarios de tu sistema, la conexión será rechazada.

1. Abre tu terminal.
2. Navega al directorio donde guardaste tu archivo `.pem` (por ejemplo, `Downloads` o `Descargas`):
   ```bash
   cd ~/Downloads

   chmod 400 mi-clave-aws.pem



## 5. Buenas prácticas

### Seguridad

- **Usa el principio de mínimo privilegio en los Security Groups:** Nunca abras puertos de administración (como el 22 para SSH o el 3389 para RDP) a todo internet (`0.0.0.0/0`). Limítalos únicamente a tu dirección IP pública o al rango de red de tu organización.
- **Evita el uso de contraseñas para acceder por SSH:** Confía siempre en la autenticación por llaves criptográficas (Key Pairs) y resguarda tu clave privada en un lugar seguro y con permisos correctos (`chmod 400`).
- **Asigna Roles IAM en lugar de Access Keys:** Si tu aplicación dentro de la instancia EC2 necesita interactuar con otros servicios de AWS (como leer archivos de un bucket S3), asígnale un rol de IAM a la instancia. Jamás guardes tus credenciales de usuario dentro del servidor.

### Costos

- **Detén las instancias que no utilices:** Si estás utilizando instancias en entornos de desarrollo o aprendizaje, apágalas (Stop) al terminar tu jornada para evitar que consuman tu cuota mensual de la capa gratuita o generen cargos inesperados.
- **Monitorea con alarmas de facturación:** Configura alertas que te notifiquen si tus recursos de EC2 comienzan a exceder el presupuesto establecido.
- **Elimina recursos huérfanos:** Al terminar con una instancia que ya no necesitas, asegúrate de eliminarla permanentemente (Terminate). Revisa si quedaron volúmenes de almacenamiento (EBS) o direcciones IP elásticas asociadas sin usar, ya que AWS cobra por ellas aunque la instancia esté apagada.

### Monitoreo y Mantenimiento

- **Usa etiquetas (Tags):** Facilita la administración de tus servidores asignándoles etiquetas que identifiquen su función, entorno y propietario (p. ej., `Env: Dev`, `App: WebServer`).
- **Automatiza tareas de inicio:** Utiliza la sección de *User Data* al lanzar una instancia para automatizar la instalación de software y configuraciones iniciales, reduciendo el trabajo manual una vez dentro del servidor.

---

## Siguientes pasos

Con tu primera instancia de EC2 configurada y comprendida, el siguiente paso natural es aprender a almacenar y organizar archivos en la nube:

- [Fundamentos de S3 (Simple Storage Service)](../04-s3/README.md)

---

## Recursos oficiales

- [Documentación oficial de Amazon EC2](https://docs.aws.amazon.com/ec2)
- [Precios de Amazon EC2](https://aws.amazon.com/ec2/pricing)
- [Capa gratuita de AWS](https://aws.amazon.com/free)

---

> 📝 **Nota:** Los links a otros tutoriales estarán disponibles conforme se publiquen en este repositorio.
