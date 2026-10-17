# Spring Boot Backend — Java 21

## Requirements

- JDK 21 for local builds and Jenkins build agents.
- Docker for container builds.
- Kubernetes access for deployment.

> Jenkins runtime compatibility is separate from the Java version used to compile this application. Current Jenkins LTS releases beginning with 2.555.1 require Java 21 or Java 25 for the controller and agents. This project is built with Java 21.

## Verify Java and Maven

```bash
java -version
./mvnw -version
```

The output should identify Java 21. The Maven Wrapper downloads and uses the Maven version pinned in `.mvn/wrapper/maven-wrapper.properties`.

## Build and test

From the `backend/` directory:

```bash
./mvnw -B clean verify
```

The JAR is created at:

```text
target/student-registration-backend-0.0.1-SNAPSHOT.jar
```

## Run locally

Configure the database connection in `src/main/resources/application.properties` for your environment, then run:

```bash
java -jar target/student-registration-backend-0.0.1-SNAPSHOT.jar
```

The application listens on port `8080`.

## Build the Java 21 container image

From the `backend/` directory:

```bash
docker build -t student-registration-backend:java21 .
```

The Dockerfile builds with a Maven/JDK 21 image and runs the application on a Java 21 JRE image.

## Kubernetes

Update `image` in `k8s/deployment.yml` to the actual registry image URI before applying the manifest. Ensure the database URL and credentials point to a reachable database; `localhost` inside a pod refers to that pod, not an external database.

```bash
kubectl apply -f k8s/deployment.yml
kubectl apply -f k8s/service.yml
kubectl rollout status deployment/student-app
kubectl get pods,svc
```

## Jenkins agent requirement

The included `mypipeline26.jdp` pipeline assumes a Debian/Ubuntu agent with JDK 21 at `/usr/lib/jvm/java-21-openjdk-amd64`, plus AWS CLI and the configured SonarQube integration. Adjust the JDK path if your agent uses a different installation location. Keep the Jenkins controller and all agents on Java versions supported by your Jenkins release.
