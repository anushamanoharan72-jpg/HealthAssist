# Health Assistance Web App - Part 1: Structure, Configuration, and Backend Core

This is the first part of the full-stack Spring Boot project. It covers the project structure, Maven dependencies, configuration, and the Core Domain Entities.

## 1. Project Folder Structure

```text
health-assistance-app/
├── pom.xml
└── src/
    ├── main/
        ├── java/
        │   └── com/healthapp/
        │       ├── HealthAssistanceApplication.java
        │       ├── config/
        │       │   └── SecurityConfig.java
        │       ├── controller/
        │       ├── entity/
        │       │   ├── User.java
        │       │   ├── Doctor.java
        │       │   ├── Appointment.java
        │       │   ├── HealthTip.java
        │       │   └── Medicine.java
        │       ├── repository/
        │       ├── service/
        │       └── util/
        └── resources/
            ├── application.properties
            ├── static/
            │   ├── css/
            │   │   └── style.css
            │   ├── js/
            │   │   └── script.js
            │   └── images/
            └── templates/
                ├── layout/
                ├── admin/
                ├── user/
                └── public/
```

## 2. pom.xml
**File Path:** `health-assistance-app/pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.4</version>
        <relativePath/> 
    </parent>
    <groupId>com.healthapp</groupId>
    <artifactId>health-assistance</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Health Assistance Web App</name>
    <description>College Final Year Project - Health Assistance System</description>

    <properties>
        <java.version>21</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity6</artifactId>
        </dependency>
        <dependency>
            <groupId>com.mysql</groupId>
            <artifactId>mysql-connector-j</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

## 3. application.properties
**File Path:** `src/main/resources/application.properties`

```properties
# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/health_app_db?createDatabaseIfNotExist=true
spring.datasource.username=${DB_USERNAME:root}
spring.datasource.password=${DB_PASSWORD:password}
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect

# Security & Thymeleaf
spring.thymeleaf.cache=false
```

## 4. Main Application Class
**File Path:** `src/main/java/com/healthapp/HealthAssistanceApplication.java`

```java
package com.healthapp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HealthAssistanceApplication {
    public static void main(String[] args) {
        SpringApplication.run(HealthAssistanceApplication.class, args);
    }
}
```

## 5. Domain Entities (Core Models)

### User Entity
**File Path:** `src/main/java/com/healthapp/entity/User.java`

```java
package com.healthapp.entity;

import jakarta.persistence.*;
import jakarta.validation.constraints.*;
import lombok.*;
import java.util.Set;

@Entity
@Table(name = "users")
@Getter @Setter @NoArgsConstructor @AllArgsConstructor
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email format")
    @Column(unique = true)
    private String email;

    @NotBlank(message = "Phone is required")
    private String phone;

    @NotBlank(message = "Password is required")
    @Size(min = 6)
    private String password;

    private String role; // ROLE_USER, ROLE_ADMIN
}
```

### Doctor Entity
**File Path:** `src/main/java/com/healthapp/entity/Doctor.java`

```java
package com.healthapp.entity;

import jakarta.persistence.*;
import lombok.*;

@Entity
@Table(name = "doctors")
@Getter @Setter @NoArgsConstructor @AllArgsConstructor
public class Doctor {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String specialization;
    private String hospital;
    private int experience;
    private String availableSlots; // e.g., "10:00 AM - 02:00 PM"
    private String imagePath;
}
```

### Appointment Entity
**File Path:** `src/main/java/com/healthapp/entity/Appointment.java`

```java
package com.healthapp.entity;

import jakarta.persistence.*;
import lombok.*;
import java.time.LocalDate;

@Entity
@Table(name = "appointments")
@Getter @Setter @NoArgsConstructor @AllArgsConstructor
public class Appointment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;

    @ManyToOne
    @JoinColumn(name = "doctor_id")
    private Doctor doctor;

    private LocalDate appointmentDate;
    private String timeSlot;
    private String status; // PENDING, CONFIRMED, CANCELLED
}
```

---
