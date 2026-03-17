# iTravel Backend

Backend API for iTravel, built with Laravel 12 and JWT authentication.

## Current Version Information

- PHP: `8.2` (using Docker image `php:8.2-fpm`)
- Laravel Framework: `^12.0`
- Node.js tooling: Vite `^7.0.7`, Tailwind CSS `^4.0.0`
- Docker Compose file format: `3.8`

## Main Libraries in Use

### Backend (Composer)

- `laravel/framework:^12.0`
- `php-open-source-saver/jwt-auth:^2.2` (JWT auth)
- `artesaos/seotools:^1.3` (SEO tools)
- `cviebrock/eloquent-sluggable:^12.0` (model slugs)
- `maatwebsite/excel:^3.1` (Excel import/export)
- `league/flysystem-aws-s3-v3:^3.32` (S3 storage, used with MinIO)

## Docker System

The current `docker-compose.yml` provisions these services:

- `app`: PHP-FPM running Laravel (`laravel_app`)
- `web`: Nginx exposed on port `8000` (`laravel_nginx`)
- `db`: MySQL 8.0 on port `3306` (`laravel_mysql`)
- `redis`: Redis on port `6379` (`laravel_redis`)
- `mailhog`: SMTP testing + UI on `1025/8025` (`laravel_mailhog`)
- `minio`: S3-compatible storage on `9000/8900` (`laravel_minio`)

## Quick Docker Setup

### 1. Create environment file

```bash
cp .env.example .env
```

If you are using PowerShell on Windows:

```powershell
Copy-Item .env.example .env
```

### 2. Update `.env` for Docker

Set the following important variables:

```env
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=secret_password

REDIS_HOST=redis
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null

FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=minio_admin
AWS_SECRET_ACCESS_KEY=minio_password
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=itravel
AWS_ENDPOINT=http://minio:9000
AWS_URL=http://localhost:9000
AWS_USE_PATH_STYLE_ENDPOINT=true
```

### 3. Build and run containers

```bash
docker compose up -d --build
```

If your machine only supports the legacy command:

```bash
docker-compose up -d --build
```

### 4. Install dependencies and initialize the app in container

```bash
docker compose exec app composer install
docker compose exec app php artisan key:generate
docker compose exec app php artisan migrate
```

### 5. JWT setup (run once)

```bash
docker compose exec app php artisan vendor:publish --provider="PHPOpenSourceSaver\JWTAuth\Providers\LaravelServiceProvider"
docker compose exec app php artisan jwt:secret
```

### 6. Access services

- API/Laravel: `http://localhost:8000`
- Mailhog UI: `http://localhost:8025`
- MinIO Console: `http://localhost:8900`
- MinIO S3 Endpoint: `http://localhost:9000`

## Useful Commands

```bash
docker compose ps
docker compose logs -f app
docker compose exec app php artisan route:list
docker compose down
```

## Notes

- Nginx config is located at `docker/nginx/default.conf`.
- PHP Docker image config is located at `docker/php/Dockerfile`.
- If you need to create the storage symlink:

```bash
docker compose exec app php artisan storage:link
```
