# Pacman Game Platform

A JavaFX-based Pacman game extended with user authentication, an administrative dashboard, MySQL-backed user management, maze generation, and graph-based ghost pathfinding.

The project combines a playable Pacman experience with a small account-management and analytics layer. Players can register and log in, while administrators can inspect and manage users through dedicated JavaFX interfaces.

## Features

### Game
- Playable Pacman implementation built with JavaFX.
- Two ghost behaviors:
  - **Standard ghosts** move according to rule-based movement logic.
  - **Directed ghosts** periodically recompute a shortest path toward Pacman.
- BFS-based shortest-path search over a graph representation of the maze.
- Maze layouts selected from predefined interior-wall configurations and mirrored across the board.
- Pellets, power pellets, gates, win/lose states, animations, and sound effects.

### User Management
- User registration and login through JavaFX/FXML interfaces.
- Separate user and administrator login modes.
- MySQL persistence through JDBC.
- Administrator interface for:
  - listing registered users,
  - searching users by username or name,
  - adding and deleting users,
  - navigating to an analytics dashboard.

### Analytics Dashboard
- Displays the total number of registered users.
- Generates a JavaFX pie chart from user data stored in MySQL.
- Uses SQL aggregation to summarize users by gender.

## Technical Highlights

### BFS Ghost Pathfinding

The maze is represented internally as a graph through an adjacency matrix. Each traversable grid cell corresponds to a graph node, with edges connecting neighboring cells that are not walls.

For directed ghosts, the project:

1. converts the ghost and Pacman grid positions into graph-node indices,
2. runs **Breadth-First Search (BFS)** to obtain a shortest path,
3. reconstructs the path using parent pointers,
4. converts the path back to maze coordinates,
5. updates the ghost movement toward Pacman's current position.

The directed ghost periodically recomputes the path as Pacman moves.

### Maze Generation

The maze is created programmatically from predefined wall patterns. A layout is randomly selected, then mirrored across the board to create the final structure. The game subsequently places pellets, power pellets, gates, and constructs the graph used by the pathfinding logic.

### Database Integration

The application connects to a local MySQL database using JDBC. Database credentials are read from:

```text
src/main/resources/config.properties
```

The application performs database operations for registration, authentication, user administration, searching, deletion, and analytics.

## Tech Stack

- **Java 21**
- **JavaFX 21**
- **FXML**
- **Maven**
- **MySQL**
- **JDBC**
- **ControlsFX**
- **Lombok**
- **Graph algorithms / BFS**

## Project Structure

```text
src/main/java/
├── com/example/demo_2/
│   ├── AppMain.java
│   ├── DatabaseConnection.java
│   ├── UserAccount.java
│   └── controller/
│       ├── AddController.java
│       ├── AdminController.java
│       ├── AdminDashboardController.java
│       ├── CommonController.java
│       ├── LoginController.java
│       ├── SignupController.java
│       └── UserHomeController.java
│
├── com/example/gamedemo/
│   ├── Character.java
│   ├── Direction.java
│   ├── GamePane.java
│   ├── GameSounds.java
│   ├── Ghost.java
│   ├── Main.java
│   ├── Maze.java
│   ├── MazeView.java
│   └── PacMan.java
│
└── module-info.java

src/main/resources/
├── com/example/demo_2/    # FXML views
├── com/example/css/       # UI styles
├── com/example/img/       # Interface assets
├── config.properties      # Database configuration
└── game assets            # Images, GIFs, audio and video
```

## Getting Started

### Prerequisites

You will need:

- JDK 21
- Maven
- MySQL Server
- a MySQL database matching the application's expected schema

### Database Configuration

Edit:

```text
src/main/resources/config.properties
```

and configure your local database connection:

```properties
username=root
password=YOUR_PASSWORD
databaseName=demo_db
```

The code expects at least the following database tables to exist:

- `useraccount`
- `admin`

Based on the queries used by the application, `useraccount` contains fields such as:

```text
userID
username
password
firstName
lastName
gender
dob
createdDate
```

The `admin` table is expected to contain administrator credentials and an `adminID` field.

> The repository does not currently include a SQL schema or migration script, so the database must be created separately before the account-management features can run.

### Dependencies

The source code references the MySQL Connector/J driver and Lombok through `module-info.java`. If your local Maven configuration does not already provide them, ensure the corresponding dependencies are present in `pom.xml` before compiling.

### Run

From the project root, use the Maven wrapper on Windows:

```bash
mvnw.cmd clean javafx:run
```

or Maven directly when available:

```bash
mvn clean javafx:run
```

## Implementation Notes

This repository combines two main parts:

1. the Pacman gameplay code under `com.example.gamedemo`, and
2. the account, database, and administration interfaces under `com.example.demo_2`.

The project extends the gameplay application with additional software-engineering features including authentication, persistent user data, administrative tooling, analytics, and algorithmic ghost behavior.

## Known Limitations

- No SQL schema or database initialization script is included in the repository.
- One administrator controller currently contains a database connection configured directly in source code; this should be moved to centralized configuration before production use.
- Passwords are compared and stored directly by the current implementation. A production system should use salted password hashing and stronger authentication practices.
- Some SQL search logic is assembled dynamically and should be migrated fully to parameterized queries for stronger security.
- The adjacency-matrix graph representation is simple and appropriate for this small maze, but an adjacency-list representation would scale more efficiently to larger maps.

## Possible Improvements

- Add a reproducible SQL initialization/migration script.
- Centralize all database configuration.
- Add secure password hashing.
- Add automated unit and integration tests.
- Replace remaining dynamically assembled SQL with parameterized queries.
- Persist gameplay statistics such as scores, wins, play time, and session history.
- Expand the analytics dashboard with player-performance metrics.
- Introduce additional ghost strategies and pathfinding algorithms.

## Context

Academic software-engineering project developed in **June 2024**.

The project demonstrates work across Java application development, GUI integration, relational databases, graph algorithms, and extension of an existing codebase with new gameplay and administrative features.
