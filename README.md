# Exp-04-Spring-Boot-with-REST-API-and-Hibernate-Integration

## AIM:
To develop a Spring Boot application to store and retrieve data from a Movies database using Object Relational Mapping (ORM) with Hibernate and expose it via REST APIs.

## ALGORITHM:
Create Spring Boot project with dependencies:

Spring Web

Spring Data JPA

H2 or MySQL Database

Configure application.properties with DB connection and JPA settings.

Create Movie entity with fields like id, title, genre, rating, and year.

Create MovieRepository interface extending JpaRepository.

Create MovieController to define REST endpoints for CRUD operations:

GET /movies

GET /movies/{id}

POST /movies

PUT /movies/{id}

DELETE /movies/{id}


## PROGRAM CODE (Main Files):

### application.properties
```
spring.application.name=EXPT4

spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

spring.h2.console.enabled=true
```

### pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">

	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.3.5</version>
		<relativePath/>
	</parent>

	<groupId>com.example</groupId>
	<artifactId>EXPT4</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<properties>
		<java.version>21</java.version>
	</properties>

	<dependencies>

		<!-- Web -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<!-- JPA -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<!-- H2 Database -->
		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>runtime</scope>
		</dependency>

		<!-- Test -->
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
			</plugin>
		</plugins>
	</build>

</project>
```

### Movie.java

```
package com.example.EXPT4.entity;

import jakarta.persistence.*;

@Entity
public class Movie {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String genre;

    // FIXED: avoid H2 conflict with "year"
    @Column(name = "release_year")
    private int year;

    private double rating;

    // Default constructor (REQUIRED)
    public Movie() {
    }

    // Parameterized constructor
    public Movie(Long id, String title, String genre, int year, double rating) {
        this.id = id;
        this.title = title;
        this.genre = genre;
        this.year = year;
        this.rating = rating;
    }

    // Getters and Setters

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getGenre() {
        return genre;
    }

    public void setGenre(String genre) {
        this.genre = genre;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    public double getRating() {
        return rating;
    }

    public void setRating(double rating) {
        this.rating = rating;
    }
}
```
### MovieRepository.java

```
package com.example.EXPT4.repository;

import com.example.EXPT4.entity.Movie;
import org.springframework.data.jpa.repository.JpaRepository;

public interface MovieRepository extends JpaRepository<Movie, Long> {
}
```

### MovieController.java

```
package com.example.EXPT4.controller;

import com.example.EXPT4.entity.Movie;
import com.example.EXPT4.repository.MovieRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/movies")
public class MovieController {

    @Autowired
    private MovieRepository repo;

    // CREATE
    @PostMapping
    public Movie addMovie(@RequestBody Movie movie) {
        return repo.save(movie);
    }

    // READ ALL
    @GetMapping
    public List<Movie> getAllMovies() {
        return repo.findAll();
    }

    // READ BY ID
    @GetMapping("/{id}")
    public Optional<Movie> getMovie(@PathVariable Long id) {
        return repo.findById(id);
    }

    // UPDATE
    @PutMapping("/{id}")
    public ResponseEntity<Movie> updateMovie(@PathVariable Long id,
                                             @RequestBody Movie newMovie) {

        return repo.findById(id).map(movie -> {

            movie.setTitle(newMovie.getTitle());
            movie.setGenre(newMovie.getGenre());
            movie.setYear(newMovie.getYear());
            movie.setRating(newMovie.getRating());

            return ResponseEntity.ok(repo.save(movie));

        }).orElse(ResponseEntity.notFound().build());
    }

    // DELETE
    @DeleteMapping("/{id}")
    public ResponseEntity<String> deleteMovie(@PathVariable Long id) {

        if (!repo.existsById(id)) {
            return ResponseEntity.notFound().build();
        }

        repo.deleteById(id);
        return ResponseEntity.ok("Movie deleted successfully");
    }
}
```

### EXPT4Applicatio.java
```
package com.example.EXPT4;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Expt4Application {
	public static void main(String[] args) {
		SpringApplication.run(Expt4Application.class, args);
	}
}
```

### Output
<img width="1092" height="765" alt="image" src="https://github.com/user-attachments/assets/3053ecb2-2e22-42b6-a948-13cae781e3ea" />
<img width="1088" height="783" alt="image" src="https://github.com/user-attachments/assets/1b92ff98-5e6f-4ccb-be9c-705ec757dd45" />
<img width="1099" height="795" alt="image" src="https://github.com/user-attachments/assets/b6438419-beca-41aa-abb7-60d7d79d183a" />
<img width="1095" height="667" alt="image" src="https://github.com/user-attachments/assets/8df54106-75a2-4076-bb25-8e8a9088525d" />

### Result
Thus, the Spring Boot application using Hibernate and REST APIs was successfully developed and executed for performing CRUD operations on the Movies database.
The application successfully stores, retrieves, updates, and deletes movie records using Spring Data JPA and H2/MySQL database.
