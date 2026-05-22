# ─────────────────────────────────────────
# STAGE 1: BUILD
# ─────────────────────────────────────────
FROM maven:3.9.6-eclipse-temurin-17 AS builder

WORKDIR /app

# Copiamos solo el pom primero para aprovechar caché de dependencias
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copiamos el resto del código fuente
COPY src ./src

# Compilamos y empaquetamos (sin tests para agilizar el build)
RUN mvn clean package -DskipTests -B

# ─────────────────────────────────────────
# STAGE 2: RUNTIME
# ─────────────────────────────────────────
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

# Creamos un usuario no-root por seguridad
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copiamos el .jar generado en el stage anterior
COPY --from=builder /app/target/*.jar app.jar

# Asignamos permisos al usuario no-root
RUN chown appuser:appgroup app.jar

# Ejecutamos como usuario no-root
USER appuser

# Puerto del servicio Despachos
EXPOSE 8081

# Variables de entorno esperadas (se inyectan en docker-compose o CI/CD)
ENV DB_ENDPOINT=""
ENV DB_PORT="3306"
ENV DB_NAME=""
ENV DB_USERNAME=""
ENV DB_PASSWORD=""

ENTRYPOINT ["java", "-jar", "app.jar"]