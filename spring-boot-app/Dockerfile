# Stage 1: Build the JAR file using Gradle
FROM gradle:7.2-jdk11 as build

WORKDIR /app

COPY --chown=gradle:gradle . /app

RUN gradle build --no-daemon

# Stage 2: Create the final runtime image
FROM openjdk:11-jre-slim

WORKDIR /app

COPY --from=build /app/build/libs/demo-0.0.1-SNAPSHOT.jar /app/app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "/app/app.jar"]