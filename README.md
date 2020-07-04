# docker_php_mysql_nginx_setup

## Requirements:
- Docker
- Git
- Using PHP 7.2 (specified inside Dockerfile)
- Using MySQL 5.7

## How to?:

* Clone the repository

* Use Docker's [composer](https://hub.docker.com/_/composer) image to mount the directories that you will need for your Laravel proejct and avoid the overhead of installing Composer globally
```
docker pull composer
```

* Move into the laravel-app directory and run the command below
```
docker run --rm -v $(pwd):/app composer install
```

* Move one up directory. change the owner of the laravel-app
```
sudo chown -R $USER: ~/laravel-app
```

* Go back inside the project directory. create a copy of .env-example file
```
cp .env-example .env
```

* When you run docker-compose up for the first time, it will download all of the necessary Docker images, which might take a while. Once the images are downloaded and stored in your local machine, Compose will create your containers. The -d flag daemonizes the process, running your containers in the background.
```
docker-compose up -d
```

Once the process is complete, user the following command to list all of the running containers:
```
docker ps
```

* Open the file using docker-compose exec, which allows you to run specific commands in containers. In this case, you are opening the file for editing:
```
docker-compose exec app bash
$> vim .env
```

* Find the block that specifies DB_CONNECTION and update it to reflect the specifics of your setup. You will modify the following fields: DB_HOST=db (container name specify in docker-compose.yml)
```
DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laraveluser
DB_PASSWORD=your_laravel_db_password
```

* Next, set the application key for the Laravel application with the php artisan key:generate command. This command will generate a key and copy it to your .env file, ensuring that your user sessions and encrypted data remain secure:
```
docker-compose exec app php artisan key:generate
```

* OPTIONAL: You now have the environment settings required to run your application. To cache these settings into a file, which will boost your application’s load speed, run: if you change your setting along the way make sure to clear the cache so that application can read the new values.
```
docker-compose exec app php artisan config:cache
```

* To create a new user, execute an interactive bash shell on the db container with docker-compose exec:
```
docker-compose exec db bash
```

* Inside the container, log into the MyQSL root administrative account:
```
mysql -u root -p
```

* Next, create the user account that will be allowed to access this database. Our username will be laraveluser, though you can replace this with another name if you’d prefer. Just be sure that your username and password here match the details you set in your .env file in the previous step

```
mysql> GRANT ALL ON laravel.* TO 'laraveluser'@'%' IDENTIFIED BY 'your_laravel_db_password';
mysql> FLUSH PRIVILEGES;
mysql> EXIT;
```

* As a final step, visit http://localhost:8080 (You can change the port by editing docker-compose.yml) in the browser. You must see the laravel home page application.

Credits to [digitalocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-laravel-nginx-and-mysql-with-docker-compose)
