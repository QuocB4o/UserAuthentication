
# UserAuthenciationApp

A simple Spring Boot web application demonstrating form-based authentication with Spring Security. Built by following the [Spring "Securing a Web Application" guide](https://spring.io/guides/gs/securing-web).

## Features

- Public home page (`/`, `/home`)
- Protected greeting page (`/hello`) — requires login
- Custom login page with error and logout messages
- In-memory user store with BCrypt-encoded password
- Sign-out functionality

## Tech Stack

- Java 17
- Spring Boot 4.0.2
- Spring Web (MVC)
- Spring Security
- Thymeleaf (with Spring Security integration)
- Maven

## Project Structure

```
src/main/java/com/example/userauthenciationapp/
├── UserAuthenciationAppApplication.java   # Main entry point
├── MvcConfig.java                          # Maps URLs to views
└── WebSecurityConfig.java                  # Security rules, user store

src/main/resources/templates/
├── home.html      # Public landing page
├── hello.html     # Protected greeting page
└── login.html     # Custom login form
```

## Getting Started

### Prerequisites

- Java 17 or later
- Maven 3.5+ (or use the included `mvnw` / `mvnw.cmd` wrapper)

### Run the application

```bash
# Windows
.\mvnw.cmd spring-boot:run

# macOS/Linux
./mvnw spring-boot:run
```

Then open [http://localhost:8080](http://localhost:8080) in your browser.

### Build a JAR

```bash
.\mvnw.cmd clean package
java -jar target/UserAuthenciationApp-0.0.1-SNAPSHOT.jar
```

## Usage

1. Visit `http://localhost:8080` — you'll see the public home page.
2. Click the link to `/hello` — you'll be redirected to `/login` since it's protected.
3. Log in with:
    - **Username:** `user`
    - **Password:** `password`
4. After logging in, you'll see a personalized greeting and a **Sign Out** button.

## Security Configuration

- `/` and `/home` are open to everyone.
- All other routes (including `/hello`) require authentication.
- Passwords are hashed with `BCryptPasswordEncoder`.
- The demo user is stored in memory via `InMemoryUserDetailsManager` — **not suitable for production**. For a real application, replace this with a database-backed `UserDetailsService`.

## License

This project is for learning purposes, based on Spring's official guides (Apache License 2.0 for guide code).