docker run -p <incoming port>:<inside port> -v <directory to reference>:<directory referenced> <image name> <command!>
    ejecuta la imagen y crea un contenedor especifico. Se puede adjuntar un comando para lanzar el contenedor que sobreescriba el que viene por defecto en la imagen
    docker run busybox echo hi
    docker run -it busybox sh   
        Lanza el contenedor pero no ejecuta el comando por defecto. Esto puede hacer que no se lanza los posibles servidores.
    con -p dirigimos el trafico de internet del puerto incoming al puerto dentro del contenedor, de esta manera podemos acceder al contenedor via web.
    con -v añadimos una referencia del contenedor hacia nuestro codigo, de manera que no haya que hacer un build cada vez que cambiamos el código
    por defecto podemos usar -v $(pwd):/app

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

docker attach <container id> 
    permite enviar el input de la terminal al contenedor indicado, pudiendo asi interactuar con el contenedor ejecutandose.
    No util con docker-compose

docker build -t eggop1992/<project name>:latest -f Dockerfile.dev . --progress=plain
    crea una nueva imagen a partir de un dockerfile. El . se usa para incluir el contexto en el build, que sera agregado a la imagen.
    -t xxx es una etiqueta para no tener que copiar el id extraño a cada rato. tiene como formato mi docker id (eggop1992), el repo/projecto que usamos (redis) y la version (latest | 1.0) 
    incluir el --progress=plain para que aparezca todo el contenido del comando ya que en las ultimas versiones se oculta gran parte. Asi coincide con la version del curso.
    cuando usas la version con alpine, esta no incluira bloatware, como otros gestores de contenido, git, etc, sera algo muy basico
    -f para incluir el nombre del dockerfile. cuando usamos 2 dockerfiles para dev/prod.

docker tag <container id> <tag>   
    el tag tiene que seguir la convencion de antes: mi docker id (eggop1992), el repo/projecto que usamos (redis) y la version (latest | 1.0) 

docker commit -c 'CMD ["<project name>"]' <container id>
    crear una imagen a partir de un contendor existente al cual le hemos hecho un cambio en los ficheros y actualizamos el comando inicial. No es una practica comun.



docker-compose up -d
    es el equivalente a docker run. Ejecuta el fichero que esta en el directorio.
    -d sirve para lanzarlo en background, asi tenemos la terminal libre para ejecutar otros comandos

docker-compose up -f docker-compose-dev.yml --build
    es el equivalente a docker build. Construye los containers y los ejecuta siguiendo la descripcion del docker-compose-yml
    incluye el -f nombre.yml para cambiar el fichero default del compose

docker-compose down 
    sirve para parar un conjunto de containers.

docker-compose ps
    es el equivalente al docker ps, pero solo incluye aquellos contenedores listados en el docker-compose.yml  



Podemos crear dos Dockerfile, uno para dev y otro para prod, que ejecuten comandos distintos.
    Dockerfile.dev
    Dockerfile


npm run start 
    execute the server for dev
npm run test  
    run the tests
npm run build  
    get app ready for prod

----------------------------------------------------
anotaciones del curso
-----------------------------------------------------
As mentioned earlier, Buildkit will hide away much of its progress which is something the legacy builder did not do. In the upcoming lectures will be discussing some output that will be quickly hidden by default. To see this output, you will want to pass the progress flag to the build command:

docker build --progress=plain .

Additionally, you can pass the no-cache flag to disable any caching:

docker build --no-cache --progress=plain .

Note - Do not try to use the no-cache flag with Lecture 47 Minimizing Cache Busting

Disabling Buildkit to match course output
To disable Buildkit, you can just pass the following variable to the build command:

DOCKER_BUILDKIT=0 docker build .
------------------------------------------------------------------------------


In the upcoming lecture, we will be running a command to create a new image using docker commit with this command:

docker commit -c 'CMD ["redis-server"]' CONTAINERID

If you are a Windows user you may get an error like "/bin/sh: [redis-server]: not found" or "No Such Container"

Instead, try running the command like this:

docker commit -c "CMD 'redis-server'" CONTAINERID
----------------------------------------------------------------------


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


