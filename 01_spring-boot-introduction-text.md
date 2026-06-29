# Spring Boot — Introduction & Quick Start

## Overview

Spring Boot is a Java-based framework used to build production-ready applications, including web apps, REST APIs and microservices. It provides sensible defaults and a streamlined developer experience so you can get started quickly.

**Primary language:** Java (tested with JDK 17+)

---

## Prerequisites

- **JDK:** Java 17 or newer
- **Build tool:** Maven or Gradle (examples use Maven)
- (Optional) **IDE / tools:** Spring Tool Suite (STS), IntelliJ IDEA, Eclipse, VS Code

---

## Create a Spring Boot Application

There are several ways to create a starter project:

- Spring Initializr (https://start.spring.io) — recommended for quick setup
- IDE wizards: STS / Eclipse / IntelliJ / VS Code
- Command-line tools (curl + Initializr or Spring CLI)

### Quick steps (using STS or IDE wizard)

1. File → New → Spring Starter Project
2. Enter project name, choose **Maven**, select **Java 17**
3. Add dependency: **Spring Web**
4. Click Next → Finish

### Using Spring Initializr web

1. Open https://start.spring.io
2. Choose Project: **Maven Project**, Language: **Java**, Spring Boot version (stable)
3. Group/Artifact, Packaging: jar, Java: **17**
4. Add dependency: **Spring Web**
5. Generate and unzip the project

---

## Run the application

From the project root (Maven):

```bash
mvn spring-boot:run
```

Or build a jar and run:

```bash
mvn package
java -jar target/your-artifact-name.jar
```

By default the app runs on port `8080`. To change port, add to `src/main/resources/application.properties`:

```properties
server.port=8888
```

---

## Example: Simple REST endpoint

Create a controller that returns a small HTML/plain-text response. Use `@RestController` for convenience (combines `@Controller` + `@ResponseBody`).

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class AppController {

    @GetMapping("/info")
    public String info() {
        // Returning HTML is fine for a quick example; for APIs prefer JSON.
        return "<h3>Hello Friends</h3>"
             + "<p><em>API fetch the data</em></p>"
             + "<p><strong><u>as per the client need</u></strong></p>"
             + "<p><strong><em><u>Happy Coding</u></em></strong></p>";
    }
}
```

Call it in a browser or with curl:

```bash
curl http://localhost:8888/info
```

If you changed the port to `8888`, use that port; otherwise use `8080`.

---

## Notes & tips

- For a proper API, return JSON objects (use `@GetMapping` with POJOs and let Spring convert to JSON).
- Use `@Controller` + templates (Thymeleaf) for server-side HTML rendering.
- Keep dependencies minimal during learning: `spring-boot-starter-web` is usually sufficient.

---

## Next steps (suggested)

- Create a minimal project via Spring Initializr and paste the example controller into `src/main/java`.
- Try `mvn spring-boot:run` and open the `/info` endpoint.
- I can scaffold a complete sample project for you if you'd like.
