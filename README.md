# Proyecto Innovatech Chile

Proyecto académico de **Introducción a Herramientas DevOps** orientado a
la contenerización, integración continua y despliegue de una aplicación
basada en frontend y microservicios sobre AWS.

## Descripción

Innovatech Chile implementa una solución para la gestión de ventas y
despachos. La aplicación está compuesta por un frontend desarrollado con
React/Vite y dos servicios backend desarrollados con Spring Boot.

La solución fue contenerizada con Docker y desplegada sobre instancias
Amazon EC2. Nginx actúa como servidor web y proxy inverso para
centralizar la comunicación del frontend con las APIs REST.

## Arquitectura implementada

``` text
Internet
   |
   v
EC2 Frontend
React + Vite + Nginx + Docker
Puerto 80
   |
   +-- /ventas-api/ ------> Backend Ventas Spring Boot :8082
   |
   +-- /despachos-api/ ---> Backend Despachos Spring Boot :8081
                                  |
                                  v
                         Base de datos relacional
```

La infraestructura se ejecuta dentro de una VPC de AWS con segmentación
mediante subredes públicas y privadas.

## Componentes

  Componente          Tecnología                    Puerto
  ------------------- -------------------------- ---------
  Frontend            React, Vite, Nginx                80
  Backend Ventas      Java, Spring Boot               8082
  Backend Despachos   Java, Spring Boot               8081
  Persistencia        Base de datos relacional     Interno

## Estructura del repositorio

``` text
proyectoInnovatech/
├── .github/
│   └── workflows/
│       ├── backend.yml
│       └── frontend.yml
├── back-Despachos_SpringBoot/
├── back-Ventas_SpringBoot/
├── front_despacho/
├── docker-compose.yml
└── README.md
```

## Docker

Los componentes utilizan Docker para disponer de entornos reproducibles
y aislados. Los Dockerfiles aplican construcción multietapa y los
archivos `.dockerignore` reducen el contexto de construcción.

### Ejecución con Docker Compose

``` bash
docker compose config
docker compose build
docker compose up -d
docker compose ps
```

Para revisar logs:

``` bash
docker compose logs --tail 100
```

Para detener los servicios:

``` bash
docker compose down
```

## APIs REST

### Backend Ventas

Ruta base: `/api/v1/ventas`

-   `GET /api/v1/ventas`
-   `GET /api/v1/ventas/{idVenta}`
-   `POST /api/v1/ventas`
-   `PUT /api/v1/ventas/{idVenta}`
-   `DELETE /api/v1/ventas/{idVenta}`

### Backend Despachos

Ruta base: `/api/v1/despachos`

-   `GET /api/v1/despachos`
-   `GET /api/v1/despachos/{idDespacho}`
-   `POST /api/v1/despachos`
-   `PUT /api/v1/despachos/{idDespacho}`
-   `DELETE /api/v1/despachos/{idDespacho}`

## Nginx como proxy inverso

El frontend consume las APIs mediante rutas relativas:

``` text
/ventas-api/      -> Backend Ventas
/despachos-api/   -> Backend Despachos
```

Esta configuración evita mantener direcciones IP de backend directamente
en React y centraliza el direccionamiento de las solicitudes.

## CI/CD con GitHub Actions

El repositorio contiene workflows independientes para frontend y
backend. El repositorio público muestra `.github/workflows` junto con
los componentes de Ventas, Despachos y Frontend.

### Backend CI

Se activa ante modificaciones en los directorios de los dos backends. El
pipeline realiza checkout, autenticación mediante secretos, construcción
de las imágenes Docker y publicación de las imágenes.

### Frontend CI

Se activa ante modificaciones en `front_despacho/**` y construye y
publica la imagen Docker del frontend.

Las credenciales se administran mediante **GitHub Secrets**.

## Despliegue AWS

La implementación funcional utiliza Amazon EC2 y Docker:

-   Frontend React/Vite servido por Nginx.
-   Backend Ventas en contenedor Spring Boot.
-   Backend Despachos en contenedor Spring Boot.
-   Comunicación validada mediante HTTP y logs de Nginx.
-   Políticas de reinicio de contenedores para continuidad operativa.

## Observabilidad

``` bash
docker ps
docker logs frontend_despacho
docker logs backend_ventas
docker logs backend_despachos
```

Los logs permiten validar solicitudes HTTP y analizar excepciones de
Spring Boot/Hibernate.

## Restricciones del entorno AWS Academy

Durante el proyecto se evaluó Amazon EKS y Amazon ECR. La cuenta AWS
Academy Learner Lab presentó políticas IAM con `explicit deny` para
operaciones requeridas por ECR y determinadas modificaciones de red.

Por este motivo, EKS, HPA, Cluster Autoscaler y ECR se documentan como
**arquitectura objetivo o mejora futura**, no como recursos
implementados. La solución funcional demostrada corresponde a **Amazon
EC2 y Docker**.

## Mejoras futuras

-   Migrar los contenedores a Amazon EKS en un entorno con permisos
    suficientes.
-   Incorporar Horizontal Pod Autoscaler y Metrics Server.
-   Utilizar Amazon ECR como registro privado.
-   Automatizar completamente el redespliegue en EC2.
-   Endurecer Security Groups aplicando mínimo privilegio.
-   Incorporar pruebas automatizadas en los workflows.
-   Centralizar logs y métricas mediante Amazon CloudWatch.

## Equipo

-   Mauro Opazo
-   Scarlette López

## Contexto académico

Proyecto desarrollado para la asignatura **Introducción a Herramientas
DevOps**, Duoc UC.

------------------------------------------------------------------------

**Proyecto Innovatech Chile --- DevOps, Docker, GitHub Actions y AWS**
