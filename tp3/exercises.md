_Altough the rest of the documents in this repository are in english, this one's in spanish, for convenience' sake. Apologies for the inconsistency._

## 1- Sistema distribuido simple

* ¿Cuáles puertos están abiertos?

```sh
$ docker ps -f 'network=mybridge'

CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                    NAMES
5679a157a789        alexisfr/flask-app:latest   "python /app.py"         5 minutes ago       Up 5 minutes        0.0.0.0:5000->5000/tcp   web
fcb35a7ecd0c        redis:alpine                "docker-entrypoint.s…"   6 minutes ago       Up 6 minutes        6379/tcp                 db
```

* Mostrar detalles de la red mybridge con Docker.

```sh
$ docker network inspect mybridge

[
    {
        "Name": "mybridge",
        "Id": "38458b189f8a3ba99e01b74096c04c29117b9ad306084f10d4fd6a81fcf8e7b0",
        "Created": "2020-08-30T21:59:13.681391336-03:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "5679a157a789106540289bb450fcb00abeafeba4c7790d3683e8482a8d5a22e6": {
                "Name": "web",
                "EndpointID": "298b30d64292308de7c1f69d2d680f48eb0809a4c6538d5f073f85e622b057b7",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "fcb35a7ecd0c1b09dcd129423403f763cf90f358eed225d9078fa86b2d5c3a47": {
                "Name": "db",
                "EndpointID": "63fc59ea123ca21449719e69697c81a9c7103a91ef406b45a93ae09cbd98d0dc",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

## 2- Análisis del sistema

* Explicar cómo funciona el sistema

Cuando se corre el container con la imagen `alexisfr/flask-app:latest`, se genera un web server hecho en Flask, el cual se conecta con la base de datos de Redis que se encuentra corriendo en el otro container de la red (`db`). De allí en más, cada vez que se reciba una request HTTP GET en la ruta `/`, la aplicación se conecta con con la base de datos de Redis e incrementa el valor de una key, que después muestra en un texto.

* ¿Para qué se sirven y porque están los parámetros -e en el segundo Docker run del ejercicio 1?

Son variables de entorno. Le indican al container que se corrió con la imagen `alexisfr/flask-app:latest` dónde se encuentra la base de datos de Redis a la que se debe conectar.

* ¿Qué pasa si ejecuta `docker rm -f web` y vuelve a correr `docker run -d --net mybridge -e REDIS_HOST=db -e REDIS_PORT=6379 -p 5000:5000 --name web alexisfr/flask-app:latest`?

Se obtiene el mismo resultado, aún persistido en la db.

* ¿Qué occure en la página web cuando borro el contenedor de Redis con `docker rm -f db`?

Crashea con un error de Redis.

* Y si lo levanto nuevamente con `docker run -d --net mybridge --name db redis:alpine`?

El contador se reinicia, ya que la db solo se encuentra persistida dentro del contexto del container.

* ¿Qué considera usted que haría falta para no perder la cuenta de las visitas?

Generar un volumen que persista los datos del container que se genera con la imagen `redis:alpine`

## 3- Utilizando docker compose

* ¿Qué hizo Docker Compose por nosotros? Explicar con detalle.

Automatizó todos los pasos que realizamos en el punto anterior, utilizando las instrucciones que detallamos en `docker-compose.yml` como una receta. Creó cada container utilizando las imagenes correspondientes y una network `bridge` por default, que les permite comunicarse entre sí. Además, generó un volumen `redis_data` que persiste la carpeta `/data` del container que utiliza la imagen de Redis, de modo que ahora el contador de accesos no se va a reiniciar aunque borremos el container db.

## 4- Aumentando la complejidad, análisis de otro sistema distribuido.

* Explicar como está configurado el sistema ([dockersamples/example-voting-app](https://github.com/dockersamples/example-voting-app)), puertos, volumenes, componenetes involucrados. Utilizar el Docker compose como guía.

El sistema utiliza dos networks: 

* `front-tier`, a través de la cuál se interconectan las apps de votación y resultados, utilizando websockets.
* `back-tier`, a través de la cuál éstas se comunican con redis y postgres. A su vez, el worker de java se comunica con estas últimas, actualizando postgres a partir de redis.

La persistencia de datos de postgres se realiza sobre el volumen `db-data`. 

Ambas aplicaciones web exponen su puerto 80 interno a través de los puestros 5000 y 5001 del host, respectivamente.