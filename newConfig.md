### Guide to Creating a Laravel Project

#### Prerequisites

Before starting your first Laravel project, ensure that your local machine has PHP, Composer, Node, and NPM installed.

#### Step 1: System Update and Upgrade

First, update and upgrade your system to ensure you have the latest packages:

```bash
sudo apt update && sudo apt upgrade -y
```

#### Step 2: Install PHP, Composer, Node, and NPM

Use the following command to install PHP, Composer, Node, and NPM:

```bash
sudo apt install php composer nodejs npm -y
```

Also install needed for Laravel:

```bash
sudo apt-get install php-xml
sudo apt install -y php8.1-curl
```

#### Step 3: Install MariaDB (Optional)

**Step 1 — Installing MariaDB**

```bash
sudo apt update
```

Then install the package:

```bash
sudo apt install mariadb-server
```

These commands will install MariaDB, but will not prompt you to set a password or make any other configuration changes. Because the default configuration leaves your installation of MariaDB insecure, you will use a script that the mariadb-server package provides to restrict access to the server and remove unused accounts.

**Step 2 — Configuring MariaDB**

For new MariaDB installations, the next step is to run the included security script. This script changes some of the less secure default options for things like remote root logins and sample users.

Run the security script:

```bash
sudo mysql_secure_installation
```

This will take you through a series of prompts where you can make some changes to your MariaDB installation’s security options. The first prompt will ask you to enter the current database root password. Since you have not set one up yet, press ENTER to indicate “none”.

Output:
```
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, you'll need the current
password for the root user.  If you've just installed MariaDB, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none):
```

You’ll be asked if you want to switch to unix socket authentication. Since you already have a protected root account, you can skip this step. Type n and then press ENTER.

Output:
```
. . .
Setting the root password or using the unix_socket ensures that nobody
can log into the MariaDB root user without the proper authorisation.

You already have your root account protected, so you can safely answer 'n'.

Switch to unix_socket authentication [Y/n] n
```

The next prompt asks you whether you’d like to set up a database root password. On Ubuntu, the root account for MariaDB is tied closely to automated system maintenance, so you should not change the configured authentication methods for that account.

Doing so would make it possible for a package update to break the database system by removing access to the administrative account. Type n and then press ENTER.

Output:
```
. . .
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.

Set root password? [Y/n] n
```

Later, you’ll go over how to set up an additional administrative account for password access if socket authentication is not appropriate for your use case.

From there, you can press Y and then ENTER to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MariaDB immediately implements the changes you have made.

With that, you’ve finished MariaDB’s initial security configuration. The next step is an optional one, though you should follow it if you prefer to authenticate to your MariaDB server with a password.

**Step 3 — (Optional) Creating an Administrative User that Employs Password Authentication**

On Ubuntu systems running MariaDB 10.5, the root MariaDB user is set to authenticate using the unix_socket plugin by default rather than with a password. This allows for some greater security and usability in many cases, but it can also complicate things when you need to allow an external program (e.g., phpMyAdmin) administrative rights.

Because the server uses the root account for tasks like log rotation and starting and stopping the server, it is best not to change the root account’s authentication details. Changing credentials in the /etc/mysql/debian.cnf configuration file may work initially, but package updates could potentially overwrite those changes. Instead of modifying the root account, the package maintainers recommend creating a separate administrative account for password-based access.

To this end, we will create a new account called admin with the same capabilities as the root account, but configured for password authentication. Open up the MariaDB prompt from your terminal:

```bash
sudo mariadb
```

Then create a new user with root privileges and password-based access. Be sure to change the username and password to match your preferences:

```sql
GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

Flush the privileges to ensure that they are saved and available in the current session:

```sql
FLUSH PRIVILEGES;
```

Following this, exit the MariaDB shell:

```bash
exit
```

Finally, let’s test the MariaDB installation.

**Step 4 — Testing MariaDB**

When installed from the default repositories, MariaDB will start running automatically. To test this, check its status.

```bash
sudo systemctl status mariadb
```

If MariaDB isn’t running, you can start it with the command:

```bash
sudo systemctl start mariadb
```

For an additional check, you can try connecting to the database using the mysqladmin tool, which is a client that allows you to run administrative commands. For example, this command says to connect to MariaDB as root using the Unix socket and return the version:

```bash
sudo mysqladmin version
```

#### Step 4: Install Laravel Installer

Install the Laravel installer globally via Composer. This tool allows you to select your preferred testing framework, database, and starter kit when creating new applications:

```bash
composer global require laravel/installer
```

#### Step 5: Create a New Laravel Project

Create a new Laravel project using the Laravel installer:

```bash
laravel new example-app
```

If laravel not founded:

A) Make sure you have the latest Laravel installer:

```bash
composer global require laravel/installer
```

B) Add composer bin folder to your $PATH:

Edit your .bashrc file: gedit $HOME/.bashrc

Add the following line: export PATH="$PATH:$HOME/.config/composer/vendor/bin"

C) Use the source command to force Ubuntu to reload your .bashrc file:

source $HOME/.bashrc

D) Try to output Laravel installer's version:

```bash
laravel -V
```

#### Step 6: Start Laravel's Local Development Server

Navigate to your project directory and start the local development server using Laravel Artisan's serve command:

```bash
cd example-app
php artisan serve
```

#### Step 7: Access Your Application

Once the Artisan development server is running, your application will be accessible in your web browser at:

[http://localhost:8000](http://localhost:8000)

#### Next Steps

You're now ready to start exploring the Laravel ecosystem. You may want to configure a database to begin developing your application further. Enjoy your Laravel journey!
