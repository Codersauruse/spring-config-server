# ☁️ Spring Cloud Config Server

This is a centralized configuration server built with Spring Boot and Spring Cloud Config. It manages external configuration for distributed systems in a secure, scalable, and version-controlled way.

---

## 📌 What This Project Does

- Serves configuration files for multiple microservices
- Pulls config files from a **private GitHub repository**
- Uses environment variables to keep credentials secure
- Easily extendable and Docker-compatible

---

## 📂 Repository Structure

```
spring-cloud-config-server/
├── src/main/resources/
│   └── bootstrap.yml         # Main config for the config server
├── .env                      # Contains GitHub credentials (NOT pushed to Git)
├── .gitignore               # Ensures .env is not tracked
├── Dockerfile               # Optional - for Docker builds
├── docker-compose.yml       # Optional - for containerized deployment
├── pom.xml
└── README.md               # You're here!
```

---

## 🔐 Security: Handling GitHub Credentials

Since your config files are stored in a **private GitHub repo**, you need authentication.

### ✅ Recommended: Use `.env` file

Create a `.env` file in the root of your project:

```env
GIT_USERNAME=your-github-username
GIT_PASSWORD=your-personal-access-token
```

❗ **Never push .env to GitHub.** Add it to `.gitignore`:

```bash
echo ".env" >> .gitignore
```

---

## 🧾 Sample bootstrap.yml

```yaml
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/Codersauruse/bus-config.git
          username: ${GIT_USERNAME}
          password: ${GIT_PASSWORD}
          clone-on-start: true
          search-paths: timetable-service
          default-label: main

server:
  port: 8071
```

---

## ▶️ How to Run (Dev Environment)

```bash
# Load environment variables and start the app
set -o allexport
source .env
set +o allexport
./mvnw spring-boot:run
```

Or use the shell shortcut:

```bash
bash run.sh
```

*(Create a `run.sh` with the above script for convenience.)*

---

## 🐳 Docker Support (Optional)

### docker-compose.yml

```yaml
version: "3.8"
services:
  config-server:
    build: .
    ports:
      - "8071:8071"
    environment:
      - GIT_USERNAME=${GIT_USERNAME}
      - GIT_PASSWORD=${GIT_PASSWORD}
```

### Run it

```bash
docker-compose up --build
```

*Make sure your `.env` file is in the same directory.*

---

## 🔗 Example Usage

A microservice (like `timetable-service`) can access its config by calling:

```bash
# Default profile
http://localhost:8071/timetable-service/default

# Development profile
http://localhost:8071/timetable-service/dev

# As YAML format
http://localhost:8071/timetable-service-default.yml

# As Properties format
http://localhost:8071/timetable-service-default.properties
```

### Expected Response Format

```json
{
  "name": "timetable-service",
  "profiles": ["default"],
  "label": null,
  "version": "70e990cdcaf8ab5d747600cf7a187d96cef76bb4",
  "state": "",
  "propertySources": [{
    "name": "https://github.com/Codersauruse/bus-config.git/timetable-service/timetable-service.properties",
    "source": {
      "spring.application.name": "timetable-service",
      "spring.data.mongodb.uri": "mongodb+srv://...",
      "spring.data.mongodb.database": "astraDB"
    }
  }]
}
```

---

## 📁 Config Repository Structure

Make sure your `bus-config` GitHub repo contains:

```
bus-config/
└── timetable-service/
    ├── timetable-service.properties          # default profile
    ├── timetable-service-dev.properties      # dev profile
    └── timetable-service-prod.properties     # prod profile
```

---

## 🛠️ Client Application Setup

For microservices to consume configurations:

### 1. Add dependency

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

### 2. Create bootstrap.yml in client app

```yaml
spring:
  application:
    name: timetable-service
  cloud:
    config:
      uri: http://localhost:8071
```

---

## 📘 Resources

- [Spring Cloud Config Docs](https://docs.spring.io/spring-cloud-config/docs/current/reference/html/)
- [Create GitHub PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [Secure Spring Boot Config](https://spring.io/guides/gs/centralized-configuration/)

---

## 👨‍💻 Author

Made by @Codersauruse ❤️
