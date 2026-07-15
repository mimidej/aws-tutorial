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


---

## 1. Conceptos clave

Antes de lanzar una instancia, es importante entender los componentes
que intervienen en el proceso:

### AMI (Amazon Machine Image)

Plantilla que define el sistema operativo y configuración base de la instancia.
AWS ofrece AMIs oficiales de Amazon Linux, Ubuntu, Windows Server, entre otras.
También puedes crear tus propias AMIs a partir de una instancia existente.

### Tipos de instancia

Definen la capacidad de cómputo asignada a la instancia. Siguen la nomenclatura
`familia.tamaño`, por ejemplo:

| Instancia | vCPUs | RAM | Caso de uso típico |
|-----------|-------|-----|--------------------|
| `t3.micro` | 2 | 1 GB | Desarrollo y pruebas |
| `t3.medium` | 2 | 4 GB | Aplicaciones pequeñas |
| `m6i.large` | 2 | 8 GB | Cargas de trabajo generales |
| `c6i.xlarge` | 4 | 8 GB | Cómputo intensivo |

> 💡 **Tip:** Para el Free Tier de AWS usa siempre `t2.micro` o `t3.micro`.

### Key Pair

Par de llaves criptográficas para acceder a la instancia vía SSH. AWS almacena
la llave pública y tú descargas la llave privada (`.pem`). Sin ella no podrás
acceder a la instancia.

> ⚠️ **Importante:** La llave privada se descarga una sola vez. Si la pierdes
> deberás reemplazarla mediante un proceso manual en la instancia.

### Security Group

Actúa como firewall a nivel de instancia. Define qué tráfico de entrada
y salida está permitido mediante reglas de puerto, protocolo y origen.

### VPC y Subnets

Toda instancia se lanza dentro de una VPC en una subnet específica, lo que
determina su conectividad y visibilidad dentro de tu arquitectura de red.


---

## 2. Lanzar una instancia

1. Ve a la consola de AWS → servicio **EC2**
2. Click en **Launch instance**

### 2.1 Configuración básica

| Parámetro | Recomendación |
|-----------|---------------|
| Name | Nombre descriptivo, por ejemplo: `web-server-dev` |
| AMI | Amazon Linux 2023 o Ubuntu 22.04 LTS |
| Tipo de instancia | `t2.micro` o `t3.micro` para Free Tier |
| Key pair | Crea uno nuevo o selecciona uno existente |

### 2.2 Configuración de red

- Selecciona la **VPC** y **subnet** adecuadas para tu ambiente
- Habilita **Auto-assign public IP** si necesitas acceso desde internet
- En **Security Group**, abre únicamente los puertos necesarios:

| Puerto | Protocolo | Origen | Uso |
|--------|-----------|--------|-----|
| 22 | TCP | Tu IP | SSH |
| 80 | TCP | 0.0.0.0/0 | HTTP |
| 443 | TCP | 0.0.0.0/0 | HTTPS |

> ⚠️ **Importante:** Nunca abras el puerto 22 a `0.0.0.0/0` en ambientes
> productivos. Restringe el acceso SSH exclusivamente a tu IP o usa
> **AWS Systems Manager Session Manager** como alternativa sin necesidad
> de exponer el puerto 22.

### 2.3 Almacenamiento

El volumen raíz por defecto es suficiente para la mayoría de los casos de prueba.
Ajusta el tamaño según las necesidades de tu carga de trabajo.

> 💡 **Tip:** El Free Tier incluye hasta 30 GB de almacenamiento EBS.

3. Click en **Launch instance** y espera a que el estado cambie a `Running`

---

## 3. Conexión SSH

> ⚠️ **Nota:** Se recomienda usar la terminal nativa de tu sistema operativo
> o clientes como **iTerm2** (macOS) o **Windows Terminal** (Windows).
> Evita PuTTY; su formato de llaves (`.ppk`) agrega complejidad innecesaria
> cuando el estándar `.pem` funciona de forma nativa en cualquier terminal moderna.

### 3.1 Preparar la llave privada

Antes de conectarte, ajusta los permisos de tu archivo `.pem`. SSH rechazará
la conexión si la llave tiene permisos demasiado abiertos:

```bash
chmod 400 mi-llave.pem
```

### 3.2 Conectarse a la instancia

```bash
ssh -i "mi-llave.pem" usuario@ip-publica
```

El usuario por defecto depende de la AMI seleccionada:

| AMI | Usuario por defecto |
|-----|-------------------|
| Amazon Linux 2023 | `ec2-user` |
| Ubuntu | `ubuntu` |
| Debian | `admin` |
| RHEL | `ec2-user` |

Por ejemplo, para una instancia Amazon Linux:

```bash
ssh -i "mi-llave.pem" ec2-user@54.123.45.67
```

### 3.3 Obtener la IP pública

En la consola de EC2 → selecciona tu instancia → en el panel inferior
encontrarás el campo **Public IPv4 address**.

> 💡 **Tip:** Si vas a conectarte frecuentemente, agrega una entrada
> en tu archivo `~/.ssh/config` para simplificar la conexión:

```text
Host mi-instancia
    HostName 54.123.45.67
    User ec2-user
    IdentityFile ~/.ssh/mi-llave.pem
```

Con esa configuración puedes conectarte simplemente con:

```bash
ssh mi-instancia
```

---

## 4. Administración básica

### 4.1 Operaciones desde la consola de AWS

Desde la consola de EC2 puedes gestionar el ciclo de vida de tus instancias.
Selecciona la instancia y ve a **Instance state**:

| Acción | Descripción |
|--------|-------------|
| **Start** | Inicia una instancia detenida |
| **Stop** | Detiene la instancia, el almacenamiento EBS se conserva |
| **Reboot** | Reinicia la instancia sin perder la IP pública |
| **Terminate** | Elimina la instancia y su almacenamiento EBS permanentemente |

> ⚠️ **Importante:** Una instancia en estado `Stopped` no genera costo
> de cómputo, pero sí de almacenamiento EBS. Una instancia en `Terminated`
> no puede recuperarse.

### 4.2 Operaciones desde la instancia

Una vez conectado vía SSH, los comandos básicos de administración varían
según el sistema operativo:

**Actualizar el sistema:**

```bash
# Amazon Linux 2023 / RHEL
sudo dnf update -y

# Ubuntu / Debian
sudo apt update && sudo apt upgrade -y
```

**Revisar recursos:**

```bash
# Uso de CPU y memoria
top

# Uso de disco
df -h

# Memoria disponible
free -h
```

**Gestionar servicios:**

```bash
# Ver estado de un servicio
sudo systemctl status nombre-servicio

# Iniciar un servicio
sudo systemctl start nombre-servicio

# Habilitar un servicio al arranque
sudo systemctl enable nombre-servicio
```

### 4.3 Transferencia de archivos

Para copiar archivos entre tu máquina local y la instancia usa `scp`:

```bash
# Local → Instancia
scp -i "mi-llave.pem" archivo.txt ec2-user@54.123.45.67:/home/ec2-user/

# Instancia → Local
scp -i "mi-llave.pem" ec2-user@54.123.45.67:/home/ec2-user/archivo.txt .
```

---

## 5. Buenas prácticas

### Seguridad

- Asigna siempre un **rol IAM** a la instancia en lugar de configurar
  access keys directamente en ella
- Mantén el sistema operativo actualizado desde el primer día
- Restringe las reglas de los security groups al mínimo necesario
- Considera **AWS Systems Manager Session Manager** para acceso remoto
  sin necesidad de exponer el puerto 22

### Costos

- Detén las instancias que no estés usando activamente
- Revisa **EC2 → Instance types** y elige el tipo adecuado para tu carga,
  ni más ni menos capacidad de la necesaria
- Usa **AWS Cost Explorer** para monitorear el gasto de tus instancias

### Organización

- Usa tags consistentes en todas tus instancias:

| Tag | Ejemplo |
|-----|---------|
| `Name` | `api-server-prod` |
| `Environment` | `dev`, `staging`, `prod` |
| `Project` | `mi-proyecto` |
| `Owner` | `ana.garcia` |

- Usa nombres descriptivos que indiquen el rol y ambiente de la instancia,
  por ejemplo: `nginx-proxy-prod` en lugar de `servidor1`

---

## Siguientes pasos

Con tu instancia corriendo, puedes continuar con:

- [Funciones Lambda](../04-lambda/README.md)

---

## Recursos oficiales

- [Documentación oficial de EC2](https://docs.aws.amazon.com/ec2)
- [Tipos de instancia](https://aws.amazon.com/ec2/instance-types)
- [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

---

> 📝 **Nota:** Los links a otros tutoriales estarán disponibles conforme
> se publiquen en este repositorio.
