# Etapa 1: Compilación de la aplicación Java
FROM eclipse-temurin:17-jdk-alpine AS build
WORKDIR /app

# Copiar el empaquetador de Maven y configuraciones preliminares
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:go-offline

# Copiar el código fuente y empaquetar el archivo .jar sin ejecutar los tests
COPY src ./src
RUN ./mvnw clean package -DskipTests

# Etapa 2: Entorno de ejecución ultra ligero y seguro
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Crear un usuario no-root por seguridad para la defensa técnica
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copiar únicamente el archivo compilado desde la etapa anterior
COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
