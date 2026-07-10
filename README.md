# piattaforma-ricette-cucina-web

## Setup Instructions

### Prerequisites
- Docker
- Docker Compose

### Running the Application

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd piattaforma-ricette-cucina-web
   ```

2. Copy the `.env.example` to `.env` and configure your environment variables:
   ```bash
   cp .env.example .env
   ```

3. Build and start the Docker containers:
   ```bash
   docker-compose up --build
   ```

4. Access the Laravel backend at `http://localhost` and the Vue.js frontend at `http://localhost:8080`.

5. The MySQL database is accessible at `db:3306` with username `root` and password `root`.

6. The S3-compatible storage is accessible at `http://localhost:9000` with access key `minioadmin` and secret key `minioadmin`.

### Stopping the Application

To stop the application, run:
```bash
docker-compose down
```

### Additional Commands

- To run artisan commands, use:
  ```bash
  docker-compose exec app php artisan <command>
  ```

- To install new npm packages for the frontend, use:
  ```bash
  docker-compose exec frontend npm install <package-name>
  ```
