Cookbook: Migrating Spring Boot Microservice from Java 11 to Java 21, Spring Boot 2.7.4 to 3.4.2, and Gradle 8.3 to 8.12.1

This cookbook provides a structured approach to migrating a Spring Boot microservice from Java 11 to Java 21, upgrading Spring Boot 2.7.4 to 3.4.2, and Gradle 8.3 to 8.12.1.


---

1. Pre-Migration Checklist

Before you begin:

Ensure your code is committed to a version control system (e.g., Git).

Run all unit and integration tests on the existing setup to establish a baseline.

Verify all dependencies are up-to-date and compatible.



---

2. Upgrade Java 11 to Java 21

2.1. Update Java Version

Change the Java version in build.gradle:

java {
    toolchain {
        languageVersion.set(JavaLanguageVersion.of(21))
    }
}

If using a system-wide installation, install JDK 21 and set the environment variable:

export JAVA_HOME=/path/to/jdk-21


2.2. Java 21 Changes

Remove deprecated APIs: Check for deprecated methods from Java 11 and update accordingly.

Enable preview features (if needed):

tasks.withType(JavaCompile).configureEach {
    options.compilerArgs += "--enable-preview"
}

Check reflection usage: Java 21 enhances security, requiring --add-opens for reflection-heavy libraries.



---

3. Upgrade Spring Boot 2.7.4 to 3.4.2

3.1. Update Dependencies

Modify build.gradle:

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-security'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'
}

In gradle.properties:

springBootVersion=3.4.2

3.2. Major Spring Boot 3.x Changes

3.2.1. Jakarta EE Migration

Spring Boot 3.x uses Jakarta EE 9+, replacing javax.* with jakarta.*:

Update imports:

import jakarta.persistence.Entity;
import jakarta.validation.constraints.NotNull;

Modify @WebServlet, @WebFilter, and @WebListener annotations accordingly.



3.2.2. Hibernate 6 Changes

Update HQL Queries:

SELECT u FROM User u WHERE u.email = :email

Instead of:

SELECT * FROM users WHERE email = :email

Datetime Handling Changes:

Ensure @Temporal(TemporalType.TIMESTAMP) is used explicitly.



3.2.3. Security Updates

Spring Security WebSecurityConfigurerAdapter is removed

Replace with:

@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http.authorizeHttpRequests(auth -> auth
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
        )
        .formLogin(withDefaults());
        return http.build();
    }
}



3.2.4. Actuator Endpoint Changes

Some endpoints are now disabled by default. Enable them explicitly in application.yml:

management:
  endpoints:
    web:
      exposure:
        include: "health,info,metrics"
  endpoint:
    health:
      show-details: always


3.2.5. YAML and Properties Validation

Ensure that property keys match the correct naming convention in application.yml.



---

4. Upgrade Gradle 8.3 to 8.12.1

4.1. Update Gradle Wrapper

Run:

./gradlew wrapper --gradle-version=8.12.1

Or modify gradle/wrapper/gradle-wrapper.properties:

distributionUrl=https\://services.gradle.org/distributions/gradle-8.12.1-bin.zip

4.2. Gradle Compatibility Check

Verify Gradle tasks:

./gradlew clean build

If using Kotlin DSL, update build.gradle.kts accordingly.



---

5. Testing and Verification

5.1. Unit & Integration Tests

Run:

./gradlew test

Fix any test failures related to:

Spring Boot 3.x changes (jakarta.*, Security, Hibernate)

Java 21 stricter type checks

Gradle changes affecting dependency resolution


5.2. Application Startup

Start the application:

./gradlew bootRun

Check for deprecation warnings or exceptions in logs.


5.3. API & Functional Testing

Use Postman or cURL to verify API endpoints.

Ensure database queries execute correctly.

Verify Spring Security authorization works as expected.



---

6. Deployment

6.1. Docker Update (If Applicable)

Modify Dockerfile to use JDK 21:

FROM eclipse-temurin:21-jdk
WORKDIR /app
COPY build/libs/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]

6.2. Kubernetes/Cloud Deployment

Update Kubernetes manifests (Deployment.yaml):

spec:
  containers:
    - name: app
      image: my-app:latest
      env:
        - name: JAVA_OPTS
          value: "-XX:+UseG1GC"

For Spring Boot 3.x, ensure management.endpoints.web.exposure.include=* is enabled in ConfigMaps.



---

7. Post-Migration Validation

7.1. Performance Benchmarking

Compare memory and CPU usage before and after migration.

Profile using:

jcmd <PID> VM.native_memory summary


7.2. Error Monitoring

Check logs for runtime errors:

tail -f logs/application.log

Ensure Observability with Spring Boot Actuator & Prometheus.



---

Conclusion

This migration roadmap ensures a smooth upgrade from Java 11 to Java 21, Spring Boot 2.7.4 to 3.4.2, and Gradle 8.3 to 8.12.1. Following this step-by-step approach minimizes downtime, fixes compatibility issues, and optimizes application performance.

