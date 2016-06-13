```shell
Description "Demonio para mi aplicación en Node.js"
author "Capy - http://ecapy.com"

env LOG_FILE=/var/log/node/miapp.log
env APP_DIR=/var/node/miapp
env APP=app.js
env PID_NAME=miapp.pid
env USER=www-data
env GROUP=www-data
env POST_START_MESSAGE_TO_LOG="miapp ha sido iniciada."
env NODE_BIN=/usr/local/bin/node
env PID_PATH=/var/opt/node/run
env SERVER_ENV="production"

######################################################

start on runlevel [2345]
stop on runlevel [016]

respawn
respawn limit 99 5

pre-start script
    mkdir -p $PID_PATH
    mkdir -p /var/log/node
end script

script
    export NODE_ENV=$SERVER_ENV
    exec start-stop-daemon --start --chuid $USER:$GROUP --make-pidfile --pidfile $PID_PATH/$PID_NAME --chdir $APP_DIR --exec $NODE_BIN -- $APP >> $LOG_FILE 2>&1
end script

post-start script
	echo $POST_START_MESSAGE_TO_LOG >> $LOG_FILE
end script
```

Por cada aplicación en nodejs que quieras montar tenes que crear un archivo en /etc/init/ por ejemplo: miapp-service.conf

Un par de aclaraciones:  
***env APP=app.js*** define el nombre del “index” de tu aplicación, y por lo general se usan o app.js o server.js.  

Pon el nombre de tu app sin el path hasta ella. Si tu aplicación lleva parámetros podes encerrar en comillas dobles algo asi como
***“env APP=app.js –extrasettings ../settrings.json”***  
***env APP_DIR=/var/node/miapp*** el path hasta tu aplicación
***env PID_NAME=miapp.pid*** pon el nombre que quieras, pero que sea único. por ejemplo, si tu app es una pagina web pon el nombre de tu pagina “mipaginaweb_com.pid” o algo así.  

***env USER=www-data*** y ***env GROUP=www-data*** no sería muy responsable usar root para ejecutar tu aplicación salvo que esta si que necesite estar en root, pero en el caso de que sea una pagina web, usa el usuario y grupo www-data así podes unificar criterios.   

Es solo una sugerencia, yo para mi app uso www-data aunque podes usar el grupo y usuario que te parezca mejor.
***POST_START_MESSAGE_TO_LOG*** es solo un mensaje que se envía al log de la app cuando esta arranca.  

***NODE_BIN*** indica donde esta ubicado node, por lo general está en /usr/local/bin/node aunque si no estuviera allí podes cambiar este parámetro.
env SERVER_ENV=”production” Posiblemente tu aplicación web utilice entornos de “development“, “test” y “production“. Bien, especificalo acá.

Cuando hayas acabado ya vas a disponer de tu servicio y tratarlo como cualquier otro:  

```shell
start miapp-service
stop miapp-service
restart miapp-service
status miapp-service
```


Esta nota es copiada de [aca](http://ecapy.com/desplegar-una-aplicacion-node-js-como-servicio-demonio/).
