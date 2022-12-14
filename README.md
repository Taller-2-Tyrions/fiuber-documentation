# Documentacion

## Esquema de arquitectura

A continuación mostramos un esquema básico de la arquitectura de nuestro backend. El mismo sigue el patrón de arquitectura API Gateway. Todas las consultas realizadas tanto por el front mobile, como por el backoffice de administradores, son a endpoints publicados en el gateway. 

El servicio gateway verifica los parámetros y además se encarga de verificar que las consultas sean realizadas por usuarios que están autorizados a hacerlas. Un administrador tiene permisos para modificar las reglas de cotización, pero un pasajero no. Luego delega la ejecución de la consulta HTTP a los correspondientes microservicios. 

Decidimos desplegar los siguientes microservicios: Users, Voyage, Pricing, Payments y Metrics. Además, nos nutrimos de distintos servicios externos como lo son Mongo Atlas, Firebase, Google Maps, Rabit Queues. A continuación explicaremos cada uno de los microservicios implementados y justificaremos porqué cada uno de estos es necesario para asegurar la menor contención entre los mismos.

## MicroServicio Users
Chamuyo + diagrama

## Microservicio Voyage
Chamuyo + diagrama

## Microservicio Pricing
Chamuyo + diagrama

## Microservicio Payments
Chamuyo + diagrama

## Microservicio Metrics
Chamuyo + diagrama
