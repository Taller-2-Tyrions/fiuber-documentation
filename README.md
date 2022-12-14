# Documentacion

## Esquema de arquitectura

A continuación mostramos un esquema básico de la arquitectura de nuestro backend. El mismo sigue el patrón de arquitectura API Gateway. Todas las consultas realizadas tanto por el front mobile, como por el backoffice de administradores, son a endpoints publicados en el gateway. 

El servicio gateway verifica los parámetros y además se encarga de verificar que las consultas sean realizadas por usuarios que están autorizados a hacerlas. Un administrador tiene permisos para modificar las reglas de cotización, pero un pasajero no. Luego delega la ejecución de la consulta HTTP a los correspondientes microservicios. 

Decidimos desplegar los siguientes microservicios: Users, Voyage, Pricing, Payments y Metrics. Además, nos nutrimos de distintos servicios externos como lo son Mongo Atlas, Firebase, Google Maps, Rabit Queues. A continuación explicaremos cada uno de los microservicios implementados y justificaremos porqué cada uno de estos es necesario para asegurar la menor contención entre los mismos.

## MicroServicio Users
Es el encargado de manejar la lógica de usuarios y delega la autenticación (y recuperación de contraseña) en Firebase. Los endpoints que publica se pueden observar haciendo click [aca](https://fiuber-users-new.herokuapp.com/docs "Documentación Usuarios OpenAPI").

El almacenamiento consistente de los datos personales de los usuarios y sus permisos los mantenemos en una base de datos de Mongo, desplegada con Mongo Atlas. Las fotos de perfil, las almacenamos en Firebase, utilizando el servicio Firebase Storage.

Decidimos crear tres tipos de roles no excluyentes entre sí: pasajero, conductor y administrador. En concordancia con los mismos, implementamos distintos esquemas para organizar la estructura de la base de datos.
## Microservicio Voyage
Chamuyo + diagrama

## Microservicio Pricing
Chamuyo + diagrama

## Microservicio Payments
Chamuyo + diagrama

## Microservicio Metrics
Chamuyo + diagrama
