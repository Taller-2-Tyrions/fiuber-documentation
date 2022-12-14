# Documentacion

## Esquema de arquitectura

A continuación mostramos un esquema básico de la arquitectura de nuestro backend. El mismo sigue el patrón de arquitectura API Gateway. Todas las consultas realizadas tanto por el front mobile, como por el backoffice de administradores, son a endpoints publicados en el gateway. 

El servicio gateway verifica los parámetros y además se encarga de verificar que las consultas sean realizadas por usuarios que están autorizados a hacerlas. Un administrador tiene permisos para modificar las reglas de cotización, pero un pasajero no. Luego delega la ejecución de la consulta HTTP a los correspondientes microservicios. 

Decidimos desplegar los siguientes microservicios: Users, Voyage, Pricing, Payments y Metrics. Además, nos nutrimos de distintos servicios externos como lo son Mongo Atlas, Firebase, Google Maps, Rabit Queues. A continuación explicaremos cada uno de los microservicios implementados y justificaremos porqué cada uno de estos es necesario para asegurar la menor contención entre los mismos.

![Arch Taller2-Página-2](https://user-images.githubusercontent.com/74473002/207724586-339f74e0-0987-4590-a79c-c0eec67a22e6.jpg)


## MicroServicio Users
Es el encargado de manejar la lógica de usuarios y delega la autenticación (y recuperación de contraseña) en Firebase. Los endpoints que publica se pueden observar haciendo click [aca](https://fiuber-users-new.herokuapp.com/docs "Documentación Usuarios OpenAPI").

El almacenamiento consistente de los datos personales de los usuarios y sus permisos los mantenemos en una base de datos de Mongo, desplegada con Mongo Atlas. Las fotos de perfil, las almacenamos en Firebase, utilizando el servicio Firebase Storage.

Decidimos crear tres tipos de roles no excluyentes entre sí: pasajero, conductor y administrador. En concordancia con los mismos, implementamos distintos esquemas para organizar la estructura de la base de datos.

A modo de ejemplo, presentamos los siguientes diagramas de secuencia. Ambos muestran casos de un pedido de bloqueo por parte de un usuario a otro usuario del sistema. El primero, con rol de Pasajero, no tiene permisos para realizar el bloqueo. Por lo tanto, recibe un código de retorno 401, indicandole que no está autorizado para realizar la acción. Por su parte el segundo, al tratarse de un administrador, efectúa el bloqueo del usuario.


![image](https://user-images.githubusercontent.com/65830097/207720756-befae9e0-8d45-4d14-852e-fa60aa3d7316.png)

*Diagrama de Secuencia: Pasajero no está autorizado para bloquear a un usuario*


![image](https://user-images.githubusercontent.com/65830097/207719047-a2963331-2415-4157-a10b-0546adeb4dab.png)

*Diagrama de Secuencia: Administrador tiene permisos para bloquear a un usuario*


Vale la pena remarcar que en el primer diagrama se hace una consulta al servicio externo de Firebase para validar el token, y otra consulta a la base de datos de Mongo para conocer los roles del usuario. Al necesitar de ambos datos en cada validación de una consulta, nos pareció necesario que este microservicio conozca tanto de usuarios y sus roles, como también sea responsable de la autenticación.  


## Microservicio Pricing
Este microservicio lo utilizamos principalmente para manejar los atributos de configuración de cotizaciones que permiten calcular el precio de cada viaje. Estas constantes son almacenadas en una base de datos de Mongo. Los endpoints que publica se pueden ver [aca](https://fiuber-pricing-new.herokuapp.com/docs "Documentación Cotización OpenAPI"). 


Por un lado, permite la modificación de los mismos a través de un endpoint accesible únicamente por los administradores. Además, es el servicio que se encarga de calcular los precios de los viajes. Dado que los mismos dependen del tiempo y la distancia que recorre el conductor, se realizan consultas al servicio externo que nos ofrece Google Maps.  


## Microservicio Voyage

![secuencia_sn](https://user-images.githubusercontent.com/71950097/207716377-5d29a7de-1f28-4dbb-b590-3abfeca0e41a.png)

## Microservicio Payments
Chamuyo + diagrama

## Microservicio Metrics
El microservicio de metricas se utiliza para obtener diferentes datos provenientes del gateway con el fin de procesarlos para poder crear metricas sobre usuarios, pagos y viajes.

Para enviar datos desde el gateway al microservicio se utiliza una cola de RabbitMQ, esto se debe a que la cantidad de eventos en el gateway puede ser muy alta y si enviamos todos ellos mediante llamadas a la API de metricas podria llegar a saturarse el microservicio, lo que logramos con una queue es que el gateway encole tantos mensajes como sea necesario en la misma y luego el microservicio ira desencolando y procesando dichos mensajes cuando sea capaz de hacerlo.

Por otro lado, para obtener las metricas, se exponen 3 endpoints los cuales si son consultados mediante el gateway haciendo uso de la API. Dichos endpoints se encuentran documentados [aca](https://fiuber-metrics-new.herokuapp.com/docs "Documentación Metricas OpenAPI").

![image](https://user-images.githubusercontent.com/74473002/207731178-33ccc151-48e7-40d6-8bac-de28b19502ba.png)




## Organización de los repositorios y esquema de trabajo

Cada microservicio desplegado fue implementado en un repositorio de github dentro de una organización. Principalmente usamos Python junto con FastAPI para el desarrollo de los microservicios. El servicio Payments decidimos implementarlo en Javascript con Express.

A excepción del Gateway, todos los microservicios están testeados con un coverage de al menos 70%. Esta medida se puede observar en el README.md de cada repositorio. Utilizamos Codecov como servicio externo que mide la covertura de código.

Por otro lado, todos los servicios están desplegados en la nube en la plataforma heroku. El deploy se hizo con integración continua mediante workflows de github actions. Cada aplicación tiene su respectivo Dockerfile, el cual nos permite abstraernos de la arquitectura y del sistema operativo, para asegurar el funcionamiento de los mismos.
