# Campus Connect

A simple Student & Notice management REST API (Spring Boot + Maven + H2), built as a
demonstration of the full **DevOps lifecycle**: Plan → Code → Build → Test → Release →
Deploy → Operate → Monitor.

## Project Structure

```
campus-connect/
├── pom.xml                          # Maven build config (dependencies, plugins, JaCoCo coverage)
├── src/main/java/com/campusconnect/
│   ├── CampusConnectApplication.java  # Spring Boot entry point
│   ├── controller/                    # REST endpoints
│   ├── service/                       # Business logic
│   ├── repository/                    # Spring Data JPA repositories
│   └── model/                         # JPA entities (Student, Notice)
├── src/main/resources/
│   └── application.properties         # H2 DB config, Actuator config
├── src/test/java/com/campusconnect/
│   └── StudentControllerTest.java     # Unit/integration tests (MockMvc)
├── Dockerfile                         # Multi-stage build -> lightweight runtime image
├── docker-compose.yml                 # Local container orchestration
├── Jenkinsfile                        # Jenkins CI/CD pipeline
├── .github/workflows/ci-cd.yml        # GitHub Actions CI/CD (alternative to Jenkins)
└── k8s/
    ├── deployment.yaml                # Kubernetes Deployment (replicas, probes)
    └── service.yaml                   # Kubernetes Service (LoadBalancer)
```

## How each file maps to the DevOps lifecycle

| Lifecycle Stage | Tool/File |
|---|---|
| **Plan** | Project backlog / Jira (not code — describe in your report) |
| **Code** | `src/main/java/...` — application source code |
| **Build** | `pom.xml` + `mvn clean compile` (Maven) |
| **Test** | `StudentControllerTest.java` + `mvn test` + JaCoCo coverage |
| **Release/Package** | `mvn package` → `target/campus-connect.jar` |
| **Containerize** | `Dockerfile`, `docker-compose.yml` |
| **CI/CD Automation** | `Jenkinsfile` (Jenkins) or `.github/workflows/ci-cd.yml` (GitHub Actions) |
| **Deploy** | `k8s/deployment.yaml`, `k8s/service.yaml` (Kubernetes) |
| **Operate/Monitor** | Spring Boot Actuator (`/actuator/health`, `/actuator/metrics`) — pair with Prometheus/Grafana if you want dashboards |

## Running locally

```bash
# Build and run tests
mvn clean install

# Run the app
mvn spring-boot:run
# App available at http://localhost:8080
# H2 console at http://localhost:8080/h2-console

# Build & run with Docker
docker build -t campus-connect .
docker run -p 8080:8080 campus-connect
# or
docker-compose up --build
```

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/students` | List all students |
| GET | `/api/students/{id}` | Get student by ID |
| POST | `/api/students` | Create a student |
| PUT | `/api/students/{id}` | Update a student |
| DELETE | `/api/students/{id}` | Delete a student |
| GET | `/api/notices` | List all notices |
| POST | `/api/notices` | Create a notice |
| DELETE | `/api/notices/{id}` | Delete a notice |
| GET | `/actuator/health` | Health check (used by Docker/K8s probes) |

## Setting up CI/CD

**Jenkins:** Create a Pipeline job pointing at this repo; Jenkins will auto-detect the
`Jenkinsfile`. You'll need Docker Hub credentials configured as `dockerhub-creds` and,
optionally, a SonarQube server for the static analysis stage.

**GitHub Actions:** Just push this repo to GitHub — `.github/workflows/ci-cd.yml` runs
automatically. Add `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` as repo secrets for the
Docker push/deploy jobs to work.

**Kubernetes:** Update the image name in `k8s/deployment.yaml` to your own Docker Hub
repo, then `kubectl apply -f k8s/`.
