Certainly! Here’s the guide with a detailed file structure overview at the end of each step:

---

# Full Stack Laravel Development Guide

## Detailed Explanation
### Docker:
  Containers for isolating environments.
### Laravel: 
  PHP framework for backend logic.
### React: 
  JavaScript library for building user interfaces.
### Tailwind CSS: 
  Utility-first CSS framework for styling.
### Redis: 
  In-memory data structure store for caching and message brokering.

## Step-by-step guide on how to set up everything

### Step 1: Setting Up Laravel Backend with Docker

1. **Install Docker and Docker Compose**:
   Ensure Docker and Docker Compose are installed on your Kali Linux machine.

   ```sh
   sudo apt-get update
   sudo apt-get install -y docker.io docker-compose
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

2. **Create a new Laravel project**:
   Create a directory for your project and navigate into it.

   ```sh
   mkdir restaurant-prototype
   cd restaurant-prototype
   ```

3. **Initialize a Laravel project**:
   Use Docker to create a new Laravel project.

   ```sh
   docker run --rm -v $(pwd):/app composer create-project --prefer-dist laravel/laravel backend
   ```

4. **Set up Docker for Laravel**:
   Create a `Dockerfile` for Laravel.

```dockerfile
# Dockerfile
FROM php:8.0-fpm
 
RUN apt-get update && apt-get install -y \
    build-essential \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    libonig-dev
 
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd
 
RUN pecl install redis \
&& docker-php-ext-enable redis
 
WORKDIR /var/www
 
COPY . /var/www
 
RUN chown -R www-data:www-data /var/www
 
EXPOSE 9000
CMD ["php-fpm"]
```

5. **Create a `docker-compose.yml` file**:

   ```yaml
   version: '3.8'
   services:
     app:
       build:
         context: ./backend
         dockerfile: Dockerfile
       image: laravel-app
       container_name: laravel-app
       restart: unless-stopped
       tty: true
       environment:
         SERVICE_NAME: app
         SERVICE_TAGS: dev
       working_dir: /var/www
       volumes:
         - ./backend:/var/www
         - ./backend/php.ini:/usr/local/etc/php/php.ini
       ports:
         - "8000:80"
       networks:
         - app-network

     redis:
       image: redis:alpine
       container_name: redis
       restart: unless-stopped
       ports:
         - "6379:6379"
       networks:
         - app-network

   networks:
     app-network:
       driver: bridge
   ```

6. **Start the Docker containers**:

   ```sh
   docker-compose up -d
   ```

**File Structure after Step 1:**

```
restaurant-prototype/
├── backend/
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   ├── database/
│   ├── public/
│   ├── resources/
│   ├── routes/
│   ├── storage/
│   ├── tests/
│   ├── .env
│   ├── artisan
│   ├── composer.json
│   ├── composer.lock
│   ├── Dockerfile
│   └── ...
├── docker-compose.yml
└── php.ini (if created)
```

### Step 2: Setting Up React Frontend with Docker

1. **Create a React app**:

   ```sh
   npx create-react-app frontend
   cd frontend
   ```

2. **Add Tailwind CSS to your React app**:

   ```sh
   npm install -D tailwindcss postcss autoprefixer
   npx tailwindcss init -p
   ```

3. **Configure Tailwind CSS**:
   Update `tailwind.config.js`:

   ```js
   module.exports = {
     purge: ['./src/**/*.{js,jsx,ts,tsx}', './public/index.html'],
     darkMode: false,
     theme: {
       extend: {},
     },
     variants: {
       extend: {},
     },
     plugins: [],
   }
   ```

   Add the following to `src/index.css`:

   ```css
   @tailwind base;
   @tailwind components;
   @tailwind utilities;
   ```

4. **Create a `Dockerfile` for React**:

   ```dockerfile
   # Dockerfile
   FROM node:14

   WORKDIR /usr/src/app

   COPY package*.json ./
   RUN npm install
   COPY . .

   EXPOSE 3000
   CMD ["npm", "start"]
   ```

5. **Add the frontend service to `docker-compose.yml`, it should look like this**:

   ```yaml
   version: '3.8'
   services:
     app:
       build:
         context: ./backend
         dockerfile: Dockerfile
       image: laravel-app
       container_name: laravel-app
       restart: unless-stopped
       tty: true
       environment:
         SERVICE_NAME: app
         SERVICE_TAGS: dev
       working_dir: /var/www
       volumes:
         - ./backend:/var/www
         - ./backend/php.ini:/usr/local/etc/php/php.ini
       ports:
         - "8000:80"
       networks:
         - app-network

     redis:
       image: redis:alpine
       container_name: redis
       restart: unless-stopped
       ports:
         - "6379:6379"
       networks:
         - app-network

     frontend:
       build:
         context: ./frontend
         dockerfile: Dockerfile
       image: react-frontend
       container_name: react-frontend
       restart: unless-stopped
       tty: true
       ports:
         - "3000:3000"
       volumes:
         - ./frontend:/usr/src/app
       networks:
         - app-network

   networks:
     app-network:
       driver: bridge
   ```

6. **Start the frontend container**:

   ```sh
   docker-compose up -d
   ```

**File Structure after Step 2:**

```
restaurant-prototype/
├── backend/
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   ├── database/
│   ├── public/
│   ├── resources/
│   ├── routes/
│   ├── storage/
│   ├── tests/
│   ├── .env
│   ├── artisan
│   ├── composer.json
│   ├── composer.lock
│   ├── Dockerfile
│   └── ...
├── frontend/
│   ├── node_modules/
│   ├── public/
│   ├── src/
│   ├── .gitignore
│   ├── Dockerfile
│   ├── package.json
│   ├── tailwind.config.js
│   └── ...
├── docker-compose.yml
└── php.ini (if created)
```

### Step 3: Connecting Laravel Backend with React Frontend

1. **Set up CORS in Laravel**:
   Install the CORS package:

   ```sh
   docker-compose exec app composer require fruitcake/laravel-cors
   ```

   Add the service provider in `config/app.php`:

   ```php
   'providers' => [
       Fruitcake\Cors\CorsServiceProvider::class,
   ],
   ```

   Publish the configuration file:

   ```sh
   docker-compose exec app php artisan vendor:publish --provider="Fruitcake\Cors\CorsServiceProvider"
   ```

   Configure CORS in `config/cors.php` according to your needs.

2. **Make API requests from React**:
   Use `axios` or `fetch` to call Laravel API endpoints from your React frontend.

**File Structure after Step 3:**

```
restaurant-prototype/
├── backend/
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   │   ├── app.php
│   │   ├── cors.php
│   ├── database/
│   ├── public/
│   ├── resources/
│   ├── routes/
│   ├── storage/
│   ├── tests/
│   ├── .env
│   ├── artisan
│   ├── composer.json
│   ├── composer.lock
│   ├── Dockerfile
│   └── ...
├── frontend/
│   ├── node_modules/
│   ├── public/
│   ├── src/
│   ├── .gitignore
│   ├── Dockerfile
│   ├── package.json
│   ├── tailwind.config.js
│   └── ...
├── docker-compose.yml
└── php.ini (if created)
```

### Step 4: Using Redis in Laravel

1. **Configure Redis in Laravel**:
  

 Update your `.env` file to use Redis for caching and queues.

   ```env
   CACHE_DRIVER=redis
   QUEUE_CONNECTION=redis
   ```

2. **Test Redis**:
   Write a simple route to test Redis caching.

   ```php
   Route::get('/cache', function () {
       Cache::put('key', 'value', 600);
       return Cache::get('key');
   });
   ```

**File Structure after Step 4:**

```
restaurant-prototype/
├── backend/
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   │   ├── app.php
│   │   ├── cors.php
│   ├── database/
│   ├── public/
│   ├── resources/
│   ├── routes/
│   │   ├── web.php
│   ├── storage/
│   ├── tests/
│   ├── .env
│   ├── artisan
│   ├── composer.json
│   ├── composer.lock
│   ├── Dockerfile
│   └── ...
├── frontend/
│   ├── node_modules/
│   ├── public/
│   ├── src/
│   ├── .gitignore
│   ├── Dockerfile
│   ├── package.json
│   ├── tailwind.config.js
│   └── ...
├── docker-compose.yml
└── php.ini (if created)
```

### Step 5: Final Steps

1. **Run Migrations**:
   Run Laravel migrations to set up your database.

   ```sh
   docker-compose exec app php artisan migrate
   ```

2. **Access your application**:
   - Laravel backend: http://localhost:8000
   - React frontend: http://localhost:3000

**Final File Structure:**

```
restaurant-prototype/
├── backend/
│   ├── app/
│   ├── bootstrap/
│   ├── config/
│   │   ├── app.php
│   │   ├── cors.php
│   ├── database/
│   ├── public/
│   ├── resources/
│   ├── routes/
│   │   ├── web.php
│   ├── storage/
│   ├── tests/
│   ├── .env
│   ├── artisan
│   ├── composer.json
│   ├── composer.lock
│   ├── Dockerfile
│   └── ...
├── frontend/
│   ├── node_modules/
│   ├── public/
│   ├── src/
│   ├── .gitignore
│   ├── Dockerfile
│   ├── package.json
│   ├── tailwind.config.js
│   └── ...
├── docker-compose.yml
└── php.ini (if created)
```

By following these steps, you'll have a Dockerized full-stack application with Laravel as the backend, React and Tailwind CSS for the frontend, and Redis for caching. This setup provides a modular and scalable foundation for developing and deploying your restaurant web application.
