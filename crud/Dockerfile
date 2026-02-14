# Container to build the application JAR and cache the .m2 repository
FROM maven:3-eclipse-temurin-21-alpine AS builder
WORKDIR /builder
COPY . .

# Build the app
RUN --mount=type=cache,target=/root/.m2 mvn package spring-boot:repackage -DskipTests

# Extract the dependencies (Spring Boot 3.2+ layered JAR)
RUN java -Djarmode=tools -jar target/crud-demo.jar extract --layers --destination extracted

# Analyze the dependencies for jlink
RUN jdeps --ignore-missing-deps -q \
    --print-module-deps \
    --recursive \
    --multi-release 21 \
    --class-path '/builder/extracted/dependencies/lib/*' \
    ./target/crud-demo.jar > deps.info && \
    sed 's/$/,jdk.naming.dns/g' deps.info > all_deps.info

# Create a custom JRE with only needed modules
RUN jlink \
    --verbose \
    --add-modules $(cat all_deps.info) \
    --strip-debug \
    --no-man-pages \
    --no-header-files \
    --output customjre


# Runtime container
FROM alpine:3.19

# Install minimal dependencies
RUN apk add --no-cache gcompat libstdc++

# Set the custom Java runtime
ENV JAVA_HOME=/jre
ENV PATH="${JAVA_HOME}/bin:${PATH}"

COPY --from=builder /builder/customjre /jre

# Copy the application and its dependencies
WORKDIR /app
COPY --from=builder /builder/extracted/dependencies/ ./
COPY --from=builder /builder/extracted/spring-boot-loader/ ./
COPY --from=builder /builder/extracted/snapshot-dependencies/ ./
COPY --from=builder /builder/extracted/application/ ./

EXPOSE 8080

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:8080/actuator/health || exit 1

ENTRYPOINT ["java", "-jar", "crud-demo.jar"]
