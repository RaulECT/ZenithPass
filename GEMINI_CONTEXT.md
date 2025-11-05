Actúa como mi Project Manager y desarrollador senior de backend. Estamos construyendo un proyecto llamado "Cenitro".

**1. Contexto del Proyecto:**
Es una plataforma para el registro a un evento de aproximadamente 900 personas. El objetivo es gestionar el registro de usuarios, procesar pagos y permitir la subida de documentación requerida.

**2. Arquitectura del Sistema (Microservicios):**
El diseño del sistema que hemos aprobado incluye:
* **Punto de Entrada:** Load Balancer y un API Gateway (que será una app NestJS).
* **Microservicios Principales:**
    * `register-service`: Maneja la lógica de usuarios, registro al evento y pagos.
    * `documents-service`: Maneja la subida y validación de archivos.
    * `notification-service`: Maneja el envío de correos asíncronos.
* **Bases de Datos:** Una base de datos PostgreSQL principal (Write) con una réplica (Read) para consultas y reportes.
* **Almacenamiento de Archivos:** Un servicio de almacenamiento de objetos (Amazon S3).
* **Mensajería:** Una cola de mensajes (Amazon SQS) para desacoplar las notificaciones.
* **Workers:** Workers (Amazon Lambda o contenedores ECS) que cons<D-smen de la cola SQS para enviar correos.

**3. Stack Tecnológico:**
* **Backend:** Nest.JS
* **Gestión de Proyecto:** Monorepo gestionado con Nx.
* **Base de Datos:** PostgreSQL.
* **ORM:** TypeORM.
* **Entorno Local:** Docker y Docker Compose.
* **Cloud:** AWS (ECS/EKS, RDS, S3, SQS, Lambda).

**4. Estructura de la Base de Datos (PostgreSQL):**
Hemos definido el siguiente esquema relacional:
* `users`: Almacena la información de autenticación.
* `registrations`: Es la tabla central. Tiene una relación 1 a 1 con `users`. Contiene los campos `total_a_pagar` y `monto_pagado` para gestionar el estado financiero.
* `payments`: Tiene una relación **1 a Muchos** con `registrations`. Esto es clave para permitir que un usuario realice un pago inicial (depósito) y luego un pago de liquidación.
* `documents`: Tiene una relación **1 a 1** con `registrations` (para el MVP).

**5. Estado Actual del Proyecto (Local):**
* Ya tenemos la base del monorepo Nx.
* La estructura de carpetas es: `/apps/api-gateway` y `/apps/register-service`.
* Tenemos un `docker-compose.yml` funcional que levanta: `postgres_db`, `api_gateway` y `register-service`.
* Tenemos los `Dockerfile` para cada servicio y ya solucionamos los errores de build.
* Acabamos de ejecutar `docker-compose up --build` con éxito.
* **Tarea Actual:** Estamos conectando el `register-service` a la base de datos PostgreSQL.
* **Acciones Realizadas:**
    1.  Instalamos `npm install @nestjs/typeorm typeorm pg`.
    2.  Modificamos `apps/register-service/src/app/app.module.ts` para importar y configurar `TypeOrmModule.forRoot({...})`, leyendo la `DATABASE_URL` del entorno y usando `synchronize: true` (solo para desarrollo).
    3.  Creamos la entidad `User` en `libs/shared/src/database/entities/user.entity.ts`.

A partir de ahora, todas mis preguntas serán sobre este proyecto. ¿Entendido?
