# 🔐 IAM - Identity and Access Management

---

## Introducción

IAM es el servicio de AWS que te permite controlar **quién** puede acceder
a tus recursos y **qué** puede hacer con ellos. *p. ej. ¿Quien  puede terminanr una instancia de EC2?* Es la base de seguridad
de cualquier arquitectura en AWS y uno de los servicios que debes dominar
antes que cualquier otro.

Con IAM podrás hacer:
- Administrar de forma centralizada la autenticación y el acceso a los recursos de AWS.
- Crear usuarios, grupos y roles.
- Aplicarles políticas para controlar su acceso a los recursos de AWS.


---

## Contenido

- [Conceptos fundamentales](#1-conceptos-fundamentales)
- [MFA en IAM](#2-mfa-en-iam)
- [Access Keys](#3-access-keys)
- [Permission Boundaries](#4-permission-boundaries)
- [Buenas prácticas](#5-buenas-prácticas)

--- 
---

## 1. Conceptos fundamentales

IAM se construye sobre cuatro elementos principales que trabajan juntos:

### 1.1 Usuarios

Un usuario IAM representa a **una persona o aplicación** que interactúa con AWS.
Cada usuario tiene credenciales propias y permisos específicos.

- Evita compartir usuarios entre personas o aplicaciones
- Cada entidad debe tener su propio usuario con los permisos mínimos necesarios

### 1.2 Grupos

Un grupo es una **colección de usuarios** que comparten los mismos permisos.
En lugar de asignar permisos uno por uno, los asignas al grupo y todos sus
miembros los heredan automáticamente.

- Un usuario puede pertenecer a varios grupos simultáneamente
- Los grupos no pueden contener otros grupos

### 1.3 Políticas

Una política es un **documento que define permisos**. Especifica qué acciones
están permitidas o denegadas sobre qué recursos.

Existen dos tipos principales:

| Tipo | Descripción |
|------|-------------|
| Administradas por AWS | Creadas y mantenidas por AWS, listas para usar |
| Administradas por el cliente | Las creas tú según tus necesidades específicas |

### 1.4 Roles

Un rol es similar a un usuario, pero está diseñado para ser **asumido temporalmente**
por servicios de AWS, aplicaciones o usuarios de otras cuentas.

- Una instancia EC2 puede asumir un rol para acceder a S3 sin necesidad de access keys
- Un usuario de otra cuenta AWS puede asumir un rol para acceder a recursos de tu cuenta

> 💡 **Clave:** Siempre que una aplicación o servicio necesite acceder a AWS,
> usa un rol en lugar de crear un usuario con access keys.


---
---

## 2. MFA en IAM

El MFA (Multi-Factor Authentication) agrega una segunda capa de verificación
al iniciar sesión. En IAM puedes activarlo a nivel de usuario individual.

### 2.1 Tipos de MFA disponibles

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| Aplicación de autenticación | Genera códigos temporales cada 30 segundos | Google Authenticator, Authy |
| Llave de seguridad física | Dispositivo USB que debes tener físicamente | YubiKey |
| MFA por hardware | Dispositivo dedicado provisto por AWS | Gemalto token |

### 2.2 Cómo activar MFA en un usuario IAM

1. Ve a **IAM → Users** y selecciona el usuario
2. Click en la pestaña **Security credentials**
3. En la sección **Multi-factor authentication** → click **Assign MFA device**
4. Selecciona el tipo de MFA y sigue las instrucciones

> ⚠️ **Importante:** Activa MFA tanto en el usuario root como en todos los
> usuarios IAM con permisos administrativos. Un usuario sin MFA es un
> riesgo de seguridad aunque tenga una contraseña robusta.

### 2.3 Forzar MFA mediante política

Puedes crear una política que **obligue** a los usuarios a tener MFA activo
antes de poder realizar cualquier acción en AWS. Sin MFA habilitado, el usuario
solo podrá activarlo pero no podrá hacer nada más.

> 💡 **Recomendación:** Aplica esta política a todos los usuarios de tu cuenta
> desde el primer día. Es una de las medidas de seguridad más efectivas y
> fáciles de implementar.
---

## 3. Access Keys

Las access keys son credenciales que permiten acceder a AWS de forma
programática, es decir, desde la terminal, un script o una aplicación,
en lugar de hacerlo desde la consola web.

Una access key se compone de dos partes:

| Componente | Descripción |
|------------|-------------|
| Access Key ID | Identificador público, similar a un nombre de usuario |
| Secret Access Key | Clave privada, similar a una contraseña |

> ⚠️ **Crítico:** La Secret Access Key solo se muestra **una vez** en el momento
> de su creación. Si la pierdes, deberás eliminar la access key y crear una nueva.

### 3.1 Cuándo usar access keys

- Acceso desde la **AWS CLI** en tu terminal
- Aplicaciones o scripts que interactúan con AWS
- Herramientas de infraestructura como código como **Terraform** o **Pulumi**

### 3.2 Cuándo NO usar access keys

- Nunca en una instancia EC2 → usa un **rol IAM** en su lugar
- Nunca en una función Lambda → usa un **rol de ejecución**
- Nunca dentro de código que se sube a un repositorio público

### 3.3 Cómo crear una access key

1. Ve a **IAM → Users** y selecciona el usuario
2. Click en la pestaña **Security credentials**
3. En la sección **Access keys** → click **Create access key**
4. Selecciona el caso de uso y sigue las instrucciones
5. Descarga el archivo `.csv` o copia las credenciales en ese momento

> 💡 **Buena práctica:** Rota tus access keys periódicamente. IAM te muestra
> la fecha del último uso de cada key en **IAM → Users → Security credentials**,
> lo que te ayuda a identificar keys que ya no se usan y deben eliminarse.

---

---

## 4. Permission Boundaries

Un permission boundary es una política que define el **límite máximo de permisos**
que un usuario o rol puede tener, independientemente de las políticas que tenga
asignadas.

> 💡 **Analogía:** Imagina que tienes un empleado con acceso a toda la oficina
> (sus políticas), pero solo puede entrar al edificio en horario laboral
> (su permission boundary). El boundary no le da acceso, solo lo limita.

### 4.1 ¿Cómo funcionan?

Los permisos efectivos de un usuario son la **intersección** entre sus políticas
asignadas y su permission boundary:

| El usuario tiene en su política | Está en el boundary | ¿Puede hacerlo? |
|---------------------------------|---------------------|-----------------|
| ✅ Permitido                    | ✅ Permitido        | ✅ Sí           |
| ✅ Permitido                    | ❌ No incluido      | ❌ No           |
| ❌ No incluido                  | ✅ Permitido        | ❌ No           |

### 4.2 ¿Cuándo usarlos?

- Cuando delegas la administración de IAM a otro equipo y quieres asegurarte
  de que no puedan crear usuarios con más permisos de los que ellos mismos tienen
- Cuando quieres limitar el alcance de un rol sin modificar sus políticas directamente
- En entornos con múltiples equipos donde cada uno administra sus propios recursos

> ⚠️ **Importante:** Los permission boundaries no reemplazan a las políticas,
> trabajan en conjunto con ellas. Un boundary por sí solo no otorga ningún permiso.

---

---

## 5. Buenas prácticas

### Principio de mínimo privilegio

Otorga únicamente los permisos necesarios para realizar una tarea específica.
Si un usuario solo necesita leer objetos de S3, no le des acceso de escritura.

### Auditoría y monitoreo

- Revisa **IAM → Credential report** periódicamente para identificar usuarios
  con credenciales antiguas o sin MFA activo
- Activa **AWS CloudTrail** para registrar todas las acciones realizadas
  en tu cuenta con detalle de quién, qué y cuándo
- Revisa **IAM → Access Advisor** para identificar permisos que no se han
  usado y eliminarlos

### Organización

- Asigna permisos siempre a **grupos**, nunca directamente a usuarios
- Usa nombres descriptivos y consistentes para usuarios, grupos y roles:

| ❌ Evitar | ✅ Preferir |
|-----------|------------|
| `user1` | `ana.garcia` |
| `admin` | `equipo-devops-admin` |
| `role1` | `rol-ec2-acceso-s3` |

---

## Siguientes pasos

Con IAM configurado correctamente, puedes continuar con:

- [Lanzar tu primera instancia EC2](../03-ec2/README.md)

---

## Recursos oficiales

- [Documentación oficial de IAM](https://docs.aws.amazon.com/iam)
- [Guía de buenas prácticas de IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS CloudTrail](https://docs.aws.amazon.com/cloudtrail)

---

> 📝 **Nota:** Los links a otros tutoriales estarán disponibles conforme
> se publiquen en este repositorio.
