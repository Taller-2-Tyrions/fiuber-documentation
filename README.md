# Documentacion

## Esquema de arquitectura

A continuación mostramos un esquema básico de la arquitectura de nuestro backend. El mismo sigue el patrón de arquitectura API Gateway. Todas las consultas realizadas tanto por el front mobile, como por el backoffice de administradores, son a endpoints publicados en el gateway. 

El servicio gateway verifica los parámetros y además se encarga de verificar que las consultas sean realizadas por usuarios que están autorizados a hacerlas. Un administrador tiene permisos para modificar las reglas de cotización, pero un pasajero no. Luego delega la ejecución de la consulta HTTP a los correspondientes microservicios. 

Decidimos desplegar los siguientes microservicios: Users, Voyage, Pricing, Payments y Metrics. Además, nos nutrimos de distintos servicios externos como lo son Mongo Atlas, Firebase, Google Maps, Rabit Queues. A continuación explicaremos cada uno de los microservicios implementados y justificaremos porqué cada uno de estos es necesario para asegurar la menor contención entre los mismos.

## MicroServicio Users
Es el encargado de manejar la lógica de usuarios y delega la autenticación (y recuperación de contraseña) en Firebase. Los endpoints que publica se pueden observar haciendo click [aca](https://fiuber-users-new.herokuapp.com/docs "Documentación Usuarios OpenAPI").

El almacenamiento consistente de los datos personales de los usuarios y sus permisos los mantenemos en una base de datos de Mongo, desplegada con Mongo Atlas. Las fotos de perfil, las almacenamos en Firebase, utilizando el servicio Firebase Storage.

Decidimos crear tres tipos de roles no excluyentes entre sí: pasajero, conductor y administrador. En concordancia con los mismos, implementamos distintos esquemas para organizar la estructura de la base de datos.

![image](https://user-images.githubusercontent.com/65830097/207718460-c220df40-5797-420a-bf8d-80094ce51845.png)


## Microservicio Voyage
Chamuyo
![secuencia_sn](https://user-images.githubusercontent.com/71950097/207716377-5d29a7de-1f28-4dbb-b590-3abfeca0e41a.png)

## Microservicio Pricing
Chamuyo + diagrama

## Microservicio Payments
Chamuyo + diagrama

## Microservicio Metrics
Chamuyo + diagrama

## Organización de los repositorios y esquema de trabajo

Cada microservicio desplegado fue implementado en un repositorio de github dentro de una organización. Principalmente usamos Python junto con FastAPI para el desarrollo de los microservicios. El servicio Payments decidimos implementarlo en Javascript con Express.

A excepción del Gateway, todos los microservicios están testeados con un coverage de al menos 70%. Esta medida se puede observar en el README.md de cada repositorio. Utilizamos Codecov como servicio externo que mide la covertura de código.

Por otro lado, todos los servicios están desplegados en la nube en la plataforma heroku. El deploy se hizo con integración continua mediante workflows de github actions. Cada aplicación tiene su respectivo Dockerfile, el cual nos permite abstraernos de la arquitectura y del sistema operativo, para asegurar el funcionamiento de los mismos.
