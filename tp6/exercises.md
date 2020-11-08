### Generar imagen de docker

![](https://github.com/FranPitri/ing_soft_3/blob/master/tp6/assets/run-output.png?raw=true)

### Dockerfiles Multi Etapas

La nueva Dockerfile, `Dockerfile.multistage`, utiliza imagenes base diferentes para buildear la app y para correrla. Primero utiliza `maven:3.5.2-jdk-8-alpine` para obtener todas las dependencias del proyecto a través del pom file y, posteriormente, buildearlo. Luego, en otra etapa con `java:8-jre-alpine` como base, copia el binario previamente generado y lo corre.

Adicionalmente, utiliza la instrucción HEALTHCHECK para establecer la forma en que docker debe comprobar si el container generado sigue vivo.

### Python Flask

Al correr `docker-compose up -d`, se generan containers a partir de la "receta" declarada en `docker-compose.yml`, así como también un volumen (utilizado por el container de redis) y una red que los conecta.

La key `build.context` en docker-compose.yml, determina el directorio en el cual se encuentra la Dockerfile a partir de la cual se debe buildear la imagen que utilizará el container.


### Imagen para aplicación web en Nodejs

![](https://github.com/FranPitri/ing_soft_3/blob/master/tp6/assets/docker-hub.png?raw=true)