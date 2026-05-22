# Creación de cuenta en AWS

---

## Introducción

AWS es la plataforma de nube más utilizada en la industria.
Antes de poder usar cualquier servicio, necesitas crear una cuenta. Este tutorial
te guiará por el proceso y te dejará con una cuenta segura desde el primer día.

---

## Contenido

- [Registro de la cuenta](#1-registro-de-la-cuenta)
- [Verificación de identidad](#2-verificación-de-identidad)
- [Selección de plan](#3-selección-de-plan)
- [Configuración de seguridad inicial](#4-configuración-de-seguridad-inicial)
- [Buenas prácticas](#5-buenas-prácticas)

---

## 1. Registro de la cuenta

Ingresa a [aws.amazon.com](https://aws.amazon.com) y haz click en **Create an AWS Account**.

Completa los siguientes campos:

| Campo | Descripción |
|-------|-------------|
| Email address | Usa un correo corporativo o dedicado exclusivamente a AWS |
| Password | Mínimo 8 caracteres, combina mayúsculas, números y símbolos |
| AWS account name | Nombre descriptivo, por ejemplo: `miempresa-aws-prod` |

> ⚠️ **Importante:** Usa un correo al que siempre tengas acceso. AWS lo usará
> para alertas críticas de seguridad y facturación.

---

## 2. Verificación de identidad

AWS solicitará verificar tu identidad en dos etapas:

### Verificación de número telefónico

1. Selecciona tu país y escribe tu número
2. Elige entre recibir un **SMS** o una **llamada**
3. Ingresa el código de 4 dígitos que recibirás

### Verificación de tarjeta de crédito/débito

AWS realiza un cobro temporal de **$1 USD** para validar la tarjeta. Este cargo
se revierte automáticamente en los siguientes días.

> 💡 **Nota:** Aunque te registres en el plan gratuito, AWS requiere una tarjeta
> válida. No se te cobrará mientras te mantengas dentro del
> [Free Tier](https://aws.amazon.com/free).
>
> 
---

## 3. Selección de plan

AWS ofrece tres planes de soporte. Para comenzar, el plan gratuito es suficiente:

| Plan | Costo | Recomendado para |
|------|-------|-----------------|
| Basic | Gratis | Aprendizaje y proyectos personales |
| Developer | $29 USD/mes | Entornos de desarrollo |
| Business | $100 USD/mes | Cargas de trabajo en producción |

> 💡 **Recomendación:** Selecciona **Basic** para comenzar. Puedes cambiar
> de plan en cualquier momento desde la consola de AWS.

---

## 4. Configuración de seguridad inicial

Una vez creada la cuenta, realiza estos pasos **antes de cualquier otra cosa**.

> ⚠️ **Crítico:** La cuenta que acabas de crear es el usuario **root**. Este usuario
> tiene acceso total e irrestricto a todos los servicios y no debe usarse
> para tareas del día a día.

*Estas son buenas prácticas en torno a identidad, accesos y permisos, más adelante se profundizará en el tema (IAM)*

### 4.1 Activa MFA en el usuario root

El MFA (Multi-Factor Authentication) agrega una capa extra de seguridad.
Si alguien obtiene tu contraseña, no podrá acceder sin el segundo factor.

1. Ve a la consola de AWS → click en tu nombre de cuenta (esquina superior derecha)
2. Selecciona **Security credentials**
3. En la sección **Multi-factor authentication (MFA)** → click **Assign MFA device**
4. Elige **Authenticator app** y sigue las instrucciones
5. Escanea el código QR con una app como Google Authenticator o Authy

### 4.2 Crea un usuario IAM para uso diario

1. Ve al servicio **IAM** desde la consola
2. Selecciona **Users** → **Create user**
3. Asígnale un nombre descriptivo, por ejemplo: `admin-personal`
4. Activa **"Provide user access to the AWS Management Console"**
5. Adjunta la política **AdministratorAccess**

> ⚠️ **Importante:** A partir de este punto, usa siempre el usuario IAM
> y nunca el usuario root para operar en AWS.

---


## 5. Buenas prácticas


### Facturación

- Activa las alertas de facturación en **Billing → Budgets** para recibir
  notificaciones si tus costos superan un límite definido
- Revisa el **Free Tier Usage** periódicamente para evitar cargos inesperados

### Seguridad

- Nunca compartas las credenciales del usuario root
- Nunca escribas access keys directamente en tu código
- Activa MFA también en tu usuario IAM, no solo en root
- Revisa periódicamente **IAM → Credential report** para auditar accesos

### Organización

- Usa **tags** en todos tus recursos desde el inicio, por ejemplo:
  `Environment: dev`, `Project: mi-proyecto`
- Establece un nombre de cuenta descriptivo que identifique el ambiente,
  por ejemplo: `miempresa-dev` o `miempresa-prod`


---

## Siguientes pasos

Con tu cuenta creada y asegurada, puedes continuar con:

- [Fundamentos de IAM](../02-iam/README.md)
- [Lanzar tu primera instancia EC2](../03-ec2/README.md)

---

## Recursos oficiales

- [Documentación oficial de AWS](https://docs.aws.amazon.com)
- [AWS Free Tier](https://aws.amazon.com/free)
- [AWS IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

> 📝 **Nota:** Los links a otros tutoriales estarán disponibles conforme
> se publiquen en este repositorio.
