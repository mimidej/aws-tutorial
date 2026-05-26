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




