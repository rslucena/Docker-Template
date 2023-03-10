# Docker Template
_A simple structure used as a basis for creating new projects. The main idea is to have a quick project start and evolve as needed._
>Note: Under construction

### Tech

- [Docker](https://www.docker.com/): Is a platform designed to help developers build, share, and run modern applications. We handle the tedious setup, so you can focus on the code.

- [Nginx(stable-alpine)](https://www.nginx.com/): Nginx is a lightweight HTTP server, reverse proxy, IMAP/POP3 mail proxy, made by Igor Sysoev in 2005, under BSD-like 2-clause license.

- [PHP (8.2-FPM)](https://www.php.net/): A popular general-purpose scripting language that is especially suited to web development.

- [Redis (stack-server)](https://redis.io/): Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, and message broker.

- [Mysql (latest)](https://www.mysql.com/): Is a database management system, which uses the SQL language as an interface.

>Note: The choices were born out of experience and common usage in most of the projects I've been involved with.

## Structure

| Folder               | Description                                                |
| ----------------- | ---------------------------------------------------------------- |
| .docker       | All configuration files separated by services |
| application       | All application files |

|   Folder    | Description |   Nginx/PHP   |
| ----------  | ------------ | ------------  |
|   application/App/multithread.php  | Swoole PHP Files (multithread) | 88:1215 |
| application/App/singlethread.php  | PHP Runtime Files (singlethread) | 85:9000 |

| Folder | Description | Nginx |
| ----------  | ------------ | ------------  |
| application/Public  | Static files or public files | 80:80 |
| application/Logs  | Separate log files per service | - |
| application/Vendor  | Third-party package files | - |

## Startup

> You first need to have [Docker](https://www.docker.com/get-started/) installed.

After installing and having Docker running, it is necessary to create the necessary files to use `docker-compose`

```shell
cp exemple.env .env
rm exemple.env
```
Fill in all the variables according to your needs

```dotenv
#Used to further define dynamic properties between development and production.
ENV= 

### PHP

#Specifies the maximum amount of memory that a script is allowed to use
CONF_MEMORYLIMIT= 

#Used to set the default locale
APP_LOCALE= 

#Used to specify which error levels
CONF_REPORTING= 

#Enable or disable the saving of logs
CONF_SAVELOGS=

#Is a custom configuration variable that can be used to set the time limit of the session 
CONF_TIMESESSION= 

### MYSQL

#Name server
MYSQL_SERVER=

#Database user name
MYSQL_USER= 

#Database password
MYSQL_PASSWORD= 

#Database name
MYSQL_DATABASE= 

#The entrypoint script automatically loads the timezone data needed for the CONVERT_TZ() function. If it is not needed, any non-empty value disables timezone loading.
MYSQL_INITDB_SKIP_TZINFO= 

#Dictates the size in bytes of the commit log files stored by MySQL.
MYSQL_INNODB_LOG_FILE_SIZE= 

#The MySQL configuration parameter that specifies the amount of memory allocated to the InnoDB buffer pool by MySQL.
MYSQL_INNODB_BUFFER_POOL_SIZE= 

#MySQL maximum connection limit.
MYSQL_MAX_CONNECTIONS=

#Set it to true to allow the container to be started with a blank password for the root user.
MYSQL_ALLOW_EMPTY_PASSWORD= 

#Root password to access and manipulate MySQL settings.
MYSQL_ROOT_PASSWORD= 

## ENV REDIS

#'Name server'
REDIS_SERVER= 

#'Redis Authentication Password'
REDIS_PASS= 
```

## Creation of containers

In the root of the project, after having configured all the necessary environment variables, just run the command and wait for all the additional files to be created

```sh
 docker-compose up -d --build
```

If you want to follow the creation process to check possible errors, just run the command removing the `-d`

```sh
 docker-compose up --build
```

# Functions
Reserved part to exemplify how to use reserved functions

### Redis
_simply create a new RedisService object._

```php
$Redis = new RedisService();

$Redis->get('test');
$Redis->set('test', []);
$Redis->del('test');
$Redis->exists('test');
```

To add other methods to the service just add the name of the desired method to its constructor - [Documentation](https://github.com/ukko/phpredis-phpdoc/blob/master/src/Redis.php). Examples:

```php
/**
    * @method get(string $key)
    * @method set(string $key, mixed $value, int $timeout = 1)
    * @method del(int|string|array $keys)
*/
```

### Routes
_simply create a new RouterBootstrap object._
```php
$Routes = new RouterBootstrap();

$Routes->get('/exemple', function (){echo 'Welcome to the homepage';});
$Routes->get('/users', 'App\Controllers\UserController::viewProfile');

#Middleware
$Routes->get('/users/{id}', 'App\Controllers\UserController::viewProfile', ['App\Middlewares\AuthenticationMiddleware']);
$Routes->put('/users/{id}', 'App\Controllers\UserController::updateProfile', ['App\Middlewares\AuthenticationMiddleware']);
$Routes->delete('/users/{id}', 'App\Controllers\UserController::deleteUser', ['App\Middlewares\AuthenticationMiddleware']);
$Routes->options('/users/{id}', 'App\Controllers\UserController::getAllowedMethods', ['App\Middlewares\AuthenticationMiddleware']);


```
Available methods
```php
fn get(string $Slug, callable $Function, array $Middlewares = [])
fn post(string $Slug, callable $Function, array $Middlewares = [])
fn put(string $Slug, callable $Function, array $Middlewares)
fn delete(string $Slug, callable $Function, array $Middlewares)
fn options(string $Slug, callable $Function, array $Middlewares)
```

### Request [Middleware]
_simply create a new RequestMiddleware object._
```php
$Request = new RequestMiddleware();
$Request->get(); //magic method for get $_GET
$Request->post(); //magic method for get $_POST
$Request->post(['name' => 'value']); //magic method for set $_POST

```
Available methods
```php
* fn get(mixed $arguments = [])
* fn post(mixed $arguments = [])
* fn files(mixed $arguments = [])
* fn server() //only read
* fn cookie() //only read
* fn phpInput() //only read
```

### Validator Fields [Providers]
_simply create a new ValidatorFieldsProviders object._
```php
$Errors = (new ValidatorFieldsProviders())
->roles([
    'first_name' => 'required|string|min:3|max:100',
    'last_name' => 'required|string|min:3|max:100',
    'email' => 'email|max:255',
    'password' => 'required|string|min:8|max:20'
])
->values([
    'name' => 'test first name',
    'last_name' => 'test last name',
    'email' => 'test@localhost.test',
    'password' => rand(1000000000,9999999999),
])
->validate()

$Errors->showErrors() //returns all errors

$Errors->isValid() //returns only if the data is valid or invalid
```

## Development

Some of the configurations still need to be tested or implemented. If you are interested in helping the process, feel free to suggest updates. The more people working for the next one, the better! :)

You will also notice that most PHP extensions are not installed, this is by design. The main idea is to make the project as clean as possible, without harming the application.