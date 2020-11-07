## Investigación de los componentes

Se genera una red con 15 containers, con el único punto de entrada del sistema siendo `edge-router`. Éste es un servicio de load balancing (Traefik), que se encarga de redireccionar los requests que lleguen sobre su puerto `80` hacia el container de `front-end`, el cual actúa en forma de API Gateway.

### ¿Por qué cree usted que se está utilizando repositorios separados para el código y/o la configuración del sistema? Explique puntos a favor y en contra.

Se están utilizando repos distintos, ya que de esta forma se puede mantener trazabilidad de los cambios que se realicen a servicios individuales. A su vez, se pueden generar pipelines separadas para cada uno, lo que permite desplegarlos de forma independiente.

Por otro lado, este enfoque introduce complejidad en la administración de los repositorios.

### Cuando ejecuto `curl http://localhost/customers` ¿Cuál de todos los servicios está procesando la operación?

Primero, la request llega a `localhost/` en donde se encuentra `edge-router`. Desde allí, es redireccionada hacia el servicio `front-end`, el API Gateway. Luego, ésta temina siendo procesada por el servicio `user`.

### ¿Y para `curl http://localhost/catalogue` y `curl http://localhost/tags`?

Estas requests siguen el mismo recorrido, con la diferencia de que al llegar al API Gateway, terminan siendo procesadas por el servicio `catalogue`.

### ¿Cómo persisten los datos los servicios?

Cada servicio que requiere persistencia tiene un container acompañante, en el cual persiste sus datos. En este ejemplo se utiliza mongodb, principalmente.

### ¿Cuál es el componente encargado del procesamiento de la cola de mensajes?

`queue-master`.

### ¿Qué tipo de interfaz utilizan estos microservicios para comunicarse?

Se comunican entre sí a partir de REST.