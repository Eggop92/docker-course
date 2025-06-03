docker run <image name> <command!>
    ejecuta la imagen y crea un contenedor especifico. Se puede adjuntar un comando para lanzar el contenedor que sobreescriba el que viene por defecto en la imagen
    docker run busybox echo hi
    docker run -it busybox sh   
        Lanza el contenedor pero no ejecuta el comando por defecto. Esto puede hacer que no se lanza los posibles servidores.

docker ps
    muestra una lista con los contenedores activos en ese momento.

docker ps --all
    muestra el historico de contenedores ejecutados.

docker create <image name>
    crea pero no inicia un contnedor. Esto reserva los recursos necesarios pero no ejecuta nada

docker start -a <container id>
    Ejecuta un contenedor previamente creado. Esto lanza el comando inicial en el contenedor para comenzar a ejecutarlo.
    -a => adjunta el contenedor en la terminal. En la practica, se ve el resultado en la terminal en lugar de hacerlo en el background.

docker system prune
    Elimina los contenedores parados y mas cosas como builds cacheadas o conexiones a red sin usar

docker logs <container id>
    muestra los logs y outputs que ocurran dentro del contenedor. Util si se nos olvida el -a en el start.

docker stop <container id>
    Manda la señal de parar para que pare cuando le sea posible. Permite al sistema guardar el progreso o finalizar adecuadamente. Solo dispone de 10secs para parar o se le mandara el kill.

docker kill <container id>
    MAnda la señal de parar para que pare ahora mismo. Cuando el sistema no responde.

docker exec -it <container id> <command>
    Permite ejecutar un comando dentro de un contenedor especifico.    
    -it nos deja el resultado en primer plano, para poder interactuar con él. Sin el no tenemos acceso ni vemos lo impreso en consola.

docker exec -it <container id> sh 
    accede a una terminal dentro del contenedor, de manera que podemos ejecutar comandos ahi dentro. util para debug.

docker build -t eggop1992/<project name>:latest .
    crea una nueva imagen a partir de un dockerfile. El . se usa para incluir el contexto en el build, que sera agregado a la imagen.
    -t xxx es una etiqueta para no tener que copiar el id extraño a cada rato. tiene como formato mi docker id (eggop1992), el repo/projecto que usamos (redis) y la version (latest | 1.0) 

docker tag <container id> <tag>   
    el tag tiene que seguir la convencion de antes: mi docker id (eggop1992), el repo/projecto que usamos (redis) y la version (latest | 1.0) 

docker commit -c 'CMD ["<project name>"]' <container id>
    crear una imagen a partir de un contendor existente al cual le hemos hecho un cambio en los ficheros y actualizamos el comando inicial. No es una practica comun.


----------------------------------------------------
anotaciones del curso
-----------------------------------------------------


In the upcoming lecture, we will be running a command to create a new image using docker commit with this command:

docker commit -c 'CMD ["redis-server"]' CONTAINERID

If you are a Windows user you may get an error like "/bin/sh: [redis-server]: not found" or "No Such Container"

Instead, try running the command like this:

docker commit -c "CMD 'redis-server'" CONTAINERID


Students who have the most recent versions of Docker will now have Buildkit enabled by default. If so, you will notice a slightly different output in your terminal when building from a Dockerfile.

The most important difference for students will be the final step in the build process. As shown in the lecture the last step would say:

---> fc60771eaa08
Successfully Built fc60771eaa08
 
Now, with Buildkit, the final step would say:

 => => exporting layers                                                      
0.0s => => writing image sha256:ee59c34ada9890ca09145cc88ccb25d32b677fc3b61e921  0.0s
 
Both fc60771eaa08 and ee59c34ada9890ca09145cc88ccb25d32b677fc3b61e921 are the resulting image ID's that you would use to run a container.

eg:

docker run fc60771eaa08
or

docker run ee59c34ada9890ca09145cc88ccb25d32b677fc3b61e921


Also, the CMD instruction is now omitted from the visible output. This change is rather unimportant and does not affect the image in any way.

Using the Progress Flag to see more verbose output
Buildkit will hide away much of its progress which is something the legacy builder did not do. We will be discussing some messages and errors later in Section 4 that will be hidden by default. To see this output, you will want to pass the progress flag to the build command:

docker build --progress=plain .

Additionally, you can pass the no-cache flag to disable any caching:

docker build --no-cache --progress=plain .

Note - Do not try to use the no-cache flag with Lecture 47 Minimizing Cache Busting

Disabling Buildkit to match course output
To disable Buildkit, you can just pass the following variable to the build command:

DOCKER_BUILDKIT=0 docker build .


