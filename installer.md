I understand your frustration. Let's go through the process to install PHP 8.3 on your Ubuntu machine step-by-step. This will include adding the necessary repository, installing PHP 8.3, and making sure it is set as the default version.

### Step 1: Add the Ondřej Surý PPA Repository

First, add the repository that contains the latest PHP packages:
```bash
sudo add-apt-repository ppa:ondrej/php
sudo apt update
```

### Step 2: Install PHP 8.3 and Required Extensions

Now, install PHP 8.3 and the necessary extensions:
```bash
sudo apt install -y php8.3 php8.3-cli php8.3-common php8.3-mbstring php8.3-xml php8.3-curl php8.3-zip php8.3-mysql php8.3-intl php8.3-gd php8.3-bcmath php8.3-json
```

### Step 3: Update Alternatives to Use PHP 8.3

You need to update the alternatives system to use PHP 8.3 as the default PHP version:
```bash
sudo update-alternatives --set php /usr/bin/php8.3
```

### Step 4: Verify PHP Version

Check the PHP version to ensure PHP 8.3 is now the default:
```bash
php -v
```

### Step 5: Restart Web Server

If you are using a web server, restart it to apply the changes:
```bash
# For Apache
sudo systemctl restart apache2

# For Nginx (if using PHP-FPM)
sudo systemctl restart php8.3-fpm
sudo systemctl restart nginx
```

### Step 6: Reinstall Composer Dependencies

If you had any previous Composer issues, navigate to your project directory and install the dependencies again:
```bash
cd ~/Desktop/example-app
composer install
```

### Summary of All Commands

Here’s a summary of all the commands for quick reference:
```bash
# Update system
sudo apt update
sudo apt upgrade -y

# Add Ondřej Surý PPA repository
sudo add-apt-repository ppa:ondrej/php
sudo apt update

# Install PHP 8.3 and necessary extensions
sudo apt install -y php8.3 php8.3-cli php8.3-common php8.3-mbstring php8.3-xml php8.3-curl php8.3-zip php8.3-mysql php8.3-intl php8.3-gd php8.3-bcmath php8.3-json

# Update alternatives to use PHP 8.3 as default
sudo update-alternatives --set php /usr/bin/php8.3

# Verify PHP version
php -v

# Restart web server (choose the one you use)
sudo systemctl restart apache2  # For Apache
sudo systemctl restart php8.3-fpm nginx  # For Nginx

# Navigate to project directory and install Composer dependencies
cd ~/Desktop/example-app
composer install
```

By following these steps, you should be able to install and set PHP 8.3 as the default version on your Ubuntu machine, resolving any issues with older PHP versions. If you encounter any specific errors or need further assistance, feel free to ask!
