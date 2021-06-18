# Jenkins-Pipeline-PHP-App

E-Commerce Shopping Application

![https://res.cloudinary.com/readycle-com/image/upload/v1624006140/Readycle/Jenkins-ECommerce/jenkins-ecommerce_full_mnt3ct.gif](https://res.cloudinary.com/readycle-com/image/upload/v1624006140/Readycle/Jenkins-ECommerce/jenkins-ecommerce_full_mnt3ct.gif)

Jenkinsfile

```shell
node{

    stage('SCM Checkout')
    {
        git credentialsId: '4cc785e9-441d-4818-a248-2bfb2148004d', url: 'https://github.com/manojpannala/Jenkins_pipeline_php_app.git'
    }
    
    stage('Run Docker Compose File')
    {
        sh 'docker-compose build'
        sh 'docker-compose up -d'
    }
    stage('PUSH image to Docker Hub')
    {
        withCredentials([string(credentialsId: 'DockerHubPassword', variable: 'DHPWD')]) 
        {
            sh "docker login -u manojpannala -p ${DHPWD}"
        }
        sh 'docker push manojpannala/phpmysql_jenkins_ecommerce'
    }
}
```

docker-compose.yml

```yaml
version: '2'
services:
  web:
       build:
               context: ./php/online-shopping-system
               dockerfile: Dockerfile
       container_name: php_app
       volumes:
          - ./php/online-shopping-system:/var/www/html/
       depends_on: 
         - db  
       ports:
         - 8008:80
  db:
          container_name: mysqli
          image: mysql:latest
          command: --default-authentication-plugin=mysql_native_password
          restart: always  
          volumes:
             - ./php/online-shopping-system/database/onlineshop.sql:/docker-entrypoint-initdb.d/onlineshop.sql
          environment:
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: database
            MYSQL_USER: edureka
            MYSQL_PASSWORD: admin
          ports:
           - 6033:3306
```

Dockerfile

```docker
FROM php:7.3.3-apache
RUN apt-get update && apt-get upgrade -y
RUN docker-php-ext-install mysqli
EXPOSE 80
```
