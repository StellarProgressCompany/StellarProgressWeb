Here's a step-by-step guide to set up a Laravel full stack application using React with Tailwind CSS for the frontend, Laravel for the middleware, Redis for the backend, and Docker for containerization, all on Kali Linux.

### Prerequisites
1. **Install Docker**: Ensure Docker and Docker Compose are installed on your Kali Linux.
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose
   sudo systemctl start docker
   sudo systemctl enable docker
   ```

2. **Install Node.js and npm**: Make sure Node.js and npm are installed.
   ```bash
   sudo apt update
   sudo apt install nodejs npm
   ```

3. **Install Composer**: Ensure Composer is installed.
   ```bash
   sudo apt install composer
   ```

### Step 1: Set Up Laravel Project
1. **Create a new Laravel project**:
   ```bash
   composer create-project --prefer-dist laravel/laravel laravel-app
   cd laravel-app
   ```

2. **Set Up Laravel Sail** (for Docker):
   ```bash
   composer require laravel/sail --dev
   php artisan sail:install --with=mysql,redis
   ```

3. **Start Docker Containers**:
   ```bash
   ./vendor/bin/sail up -d
   ```

### Step 2: Set Up React with Tailwind CSS
1. **Install Laravel Mix**:
   ```bash
   composer require laravel/ui
   php artisan ui react
   npm install
   ```

2. **Install Tailwind CSS**:
   ```bash
   npm install -D tailwindcss postcss autoprefixer
   npx tailwindcss init -p
   ```

3. **Configure Tailwind**:
   Update the `tailwind.config.js` file to look like this:
   ```javascript
   /** @type {import('tailwindcss').Config} */
   module.exports = {
     content: [
       './resources/**/*.blade.php',
       './resources/**/*.js',
       './resources/**/*.vue',
     ],
     theme: {
       extend: {},
     },
     plugins: [],
   }
   ```

4. **Add Tailwind to CSS**:
   Replace the content of `resources/css/app.css` with:
   ```css
   @tailwind base;
   @tailwind components;
   @tailwind utilities;
   ```

5. **Build Assets**:
   ```bash
   npm run dev
   ```

### Step 3: Configure Docker
1. **Dockerfile**: Ensure you have a `Dockerfile` in the root directory with the following content:
   ```dockerfile
   FROM node:14-alpine as node
   WORKDIR /var/www/html
   COPY package*.json ./
   RUN npm install
   COPY . .
   RUN npm run dev

   FROM php:8.1-fpm
   WORKDIR /var/www/html
   RUN apt-get update && apt-get install -y libpng-dev libjpeg-dev libfreetype6-dev && docker-php-ext-install pdo pdo_mysql gd
   COPY --from=node /var/www/html /var/www/html
   COPY . .

   RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache

   CMD ["php-fpm"]
   ```

2. **docker-compose.yml**: Ensure you have a `docker-compose.yml` file in the root directory:
   ```yaml
   version: '3.8'

   services:
     laravel-app:
       build:
         context: .
       ports:
         - "8000:80"
       volumes:
         - .:/var/www/html
       depends_on:
         - mysql
         - redis

     mysql:
       image: mysql:5.7
       ports:
         - "3306:3306"
       environment:
         MYSQL_ROOT_PASSWORD: root
         MYSQL_DATABASE: laravel
         MYSQL_USER: laravel
         MYSQL_PASSWORD: secret
       volumes:
         - mysql-data:/var/lib/mysql

     redis:
       image: redis:alpine
       ports:
         - "6379:6379"
       volumes:
         - redis-data:/data

   volumes:
     mysql-data:
     redis-data:
   ```

### Step 4: Configure Laravel for Redis
1. **Update `.env`**:
   ```plaintext
   REDIS_HOST=redis
   REDIS_PASSWORD=null
   REDIS_PORT=6379
   ```

2. **Cache and Queue Configuration**:
   Update `config/cache.php` and `config/queue.php` to use Redis.

### Step 5: Running the Application
1. **Start Docker**:
   ```bash
   docker-compose up -d
   ```

2. **Access the Application**:
   Open your browser and navigate to `http://localhost:8000`.

### Step 6: Additional Configuration
1. **Setup Xdebug for PHP** if needed for debugging.
2. **Run Migrations**:
   ```bash
   ./vendor/bin/sail artisan migrate
   ```

### Step 7: Developing and Testing
1. **Develop** your application by editing files within your local directory.
2. **Test** using PHPUnit and Laravel Dusk.

By following these steps, you'll have a fully containerized Laravel application with React and Tailwind CSS for the frontend, Redis for the backend, and Docker handling the environment. This setup should help mitigate dependency issues with React, and ensure everything works smoothly on Kali Linux.
