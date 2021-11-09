# Mastering Microservices with Python, Flask, and Docker
Interested in microservices, and how they can be used for increased agility and scalability?

Microservices is an architectural style and pattern that structures an application as a collection of coherent services. Each service is highly maintainable, testable, loosely coupled, independently deployable, and precisely focused.

This [course](https://cloudacademy.com/course/mastering-microservices-with-python-flask-docker-1118) takes a hands-on look at microservices using Python, Flask, and Docker. You'll learn how Flask can be used to quickly prototype and build microservices, as well as how to use Docker to host and deploy them.

:metal:

## Project Structure
The Python Flask based microservices project is composed of the following 4 projects: 
* [frontend](https://github.com/cloudacademy/python-flask-microservices/tree/master/frontend)
* [user-service](https://github.com/cloudacademy/python-flask-microservices/tree/master/user-service)
* [product-service](https://github.com/cloudacademy/python-flask-microservices/tree/master/product-service)
* [order-service](https://github.com/cloudacademy/python-flask-microservices/tree/master/order-service)

## Microservices Setup and Configuration
To launch the end-to-end microservices application perform the following:

### Step 1.
Navigate into the [frontend](https://github.com/cloudacademy/python-flask-microservices/tree/master/frontend) directory, and confirm the presence of the ```docker-compose.deploy.yml``` file:
```
cd frontend
ls -la
```

### Step 2.
Create a new Docker network and name it ```micro_network```:
```
docker network create micro_network
```

### Step 3.
Build each of the microservice Docker container images:
```
docker-compose -f docker-compose.deploy.yml build
docker images
```

### Step 4.
Launch the microservice environment:
```
docker-compose -f docker-compose.deploy.yml -d up
docker ps -a
```

### Step 5.
Prepare each microservice mysql database:
```
for service in corder-service cproduct-service cuser-service;
do 
 docker exec -it $service flask db init
 docker exec -it $service flask db migrate
 docker exec -it $service flask db upgrade
done
```

### Step 6.
Populate the product database:
```
curl -i -d "name=prod1&slug=prod1&image=product1.jpg&price=100" -X POST localhost:5002/api/product/create
curl -i -d "name=prod2&slug=prod2&image=product2.jpg&price=200" -X POST localhost:5002/api/product/create
```

### Step 7.
Using your workstations browser - navigate to the following URL and register:
```
http://localhost:5000/register
```

### Step 8.
Back within your terminal, use a mysql client to confirm that a new user registration record was created:
```
mysql --host=127.0.0.1 --port=32000 --user=cloudacademy --password=pfm_2020
mysql> show databases;
mysql> use user;
mysql> show tables;
mysql> select * from user;
mysql> exit
```

### Step 9.
Using your workstations browser - login, and add products into your cart, and then finally click the checkout option
```
http://localhost:5000/login
```

### Step 10.
Back within your terminal, use a mysql client to confirm that a new order has been created:
```
mysql --host=127.0.0.1 --port=32002 --user=cloudacademy --password=pfm_2020
mysql> show databases;
mysql> use order;
mysql> show tables;
mysql> select * from order.order;
mysql> select * from order.order_item;
mysql> exit
```

## Microservices Teardown
Perform the following steps to teardown the microservices environment:

### Step 1.
Create a new Docker network and name it ```micro_network```:
```
for container in cuser-service cproduct-service corder-service cproduct_dbase cfrontend-app cuser_dbase corder_dbase;
do
 docker stop $container
 docker rm $container
done
```

### Step 2.
Remove the container volumes
```
for vol in frontend_orderdb_vol frontend_productdb_vol frontend_userdb_vol;
do
 docker volume rm $vol
done
```

### Step 3.
Remove the container network
```
docker network rm micro_network
```

## Python extensions reference
The following Python extensions were used:

* Flask-SQLAlchemy: https://flask-sqlalchemy.palletsprojects.com/en/2.x/
* Flask-Login: https://flask-login.readthedocs.io/en/latest/
* Flask-Migrate: https://github.com/miguelgrinberg/flask-migrate/
* Requests: https://requests.readthedocs.io/en/master/
