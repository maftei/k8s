# CRUD Demo - Spring Boot Application

A simple CRUD REST API built with Spring Boot and H2 in-memory database.

## Project Structure

```
crud/
├── pom.xml
├── Dockerfile
├── src/main/java/com/example/crud/
│   ├── CrudApplication.java      # Main entry point
│   ├── model/Item.java           # Entity
│   ├── repository/ItemRepository.java
│   ├── service/ItemService.java
│   └── controller/ItemController.java
└── src/main/resources/
    ├── application.yml           # Configuration
    └── data.sql                  # Sample data
```

## Run Locally (without Docker)

```bash
cd /Users/razvanmaftei/Documents/Projects/workflow-templates/Service/crud
mvn spring-boot:run
```

## Build & Run with Docker

```bash
cd /Users/razvanmaftei/Documents/Projects/workflow-templates/Service/crud

# Build the image
docker build -t crud-demo:latest .

# Run the container
docker run -d -p 8080:8080 --name crud-demo crud-demo:latest

# Check logs
docker logs -f crud-demo

# Stop and remove
docker stop crud-demo && docker rm crud-demo
```

## API Endpoints

| Method | Endpoint              | Description          |
|--------|----------------------|----------------------|
| GET    | /api/items           | Get all items        |
| GET    | /api/items/{id}      | Get item by ID       |
| GET    | /api/items/search?name=xxx | Search by name |
| POST   | /api/items           | Create new item      |
| PUT    | /api/items/{id}      | Update item          |
| DELETE | /api/items/{id}      | Delete item          |
| GET    | /actuator/health     | Health check         |
| GET    | /h2-console          | H2 Database console  |

## Test with curl

```bash
# Get all items
curl http://localhost:8080/api/items

# Get item by ID
curl http://localhost:8080/api/items/1

# Create new item
curl -X POST http://localhost:8080/api/items \
  -H "Content-Type: application/json" \
  -d '{"name":"Tablet","description":"10-inch tablet","price":599.99}'

# Update item
curl -X PUT http://localhost:8080/api/items/1 \
  -H "Content-Type: application/json" \
  -d '{"name":"Gaming Laptop","description":"High-end gaming laptop","price":1999.99}'

# Delete item
curl -X DELETE http://localhost:8080/api/items/1

# Health check
curl http://localhost:8080/actuator/health
```
