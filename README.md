# traefik-docker-setup
Este repositorio contiene una guía paso a paso para configurar y acceder al dashboard de Traefik utilizando Docker en un servidor remoto. Incluye todos los comandos necesarios y las configuraciones para asegurar un despliegue exitoso.

### Pasos para la Configuración

#### 1. Conectarse al Servidor Remoto

Usar SSH para conectarse al servidor remoto:
```
ssh user@server_address
```
Reemplaza user con tu nombre de usuario y server_address con la dirección IP o el nombre de dominio de tu servidor.

#### 2. Crear el Archivo de Configuración de Traefik

Crear un archivo de configuración llamado traefik.yml en el servidor:

``` entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"

providers:
  docker:
    exposedByDefault: false

api:
  dashboard: true
  insecure: true

Guardar este archivo en el directorio de trabajo actual, por ejemplo, /home/user/traefik.
``` 

#### 3. Ejecutar el Contenedor de Traefik

Detener y eliminar cualquier contenedor existente que pueda estar en conflicto:
```
sudo docker stop zen_mayer
sudo docker rm zen_mayer
```

Ejecutar el contenedor de Traefik con acceso al socket de Docker:
```
sudo docker run -d -p 81:81 -p 443:443 -p 8080:8080 \
  -v $PWD/traefik.yml:/etc/traefik/traefik.yml \
  -v /var/run/docker.sock:/var/run/docker.sock \
  traefik:v3.1
```

#### 4. Verificar que el Contenedor Está en Ejecución

Verificar que el contenedor de Traefik está en ejecución y que los puertos están correctamente mapeados:
```
sudo docker ps
```

Salida esperada:
```
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                                                                                                         NAMES
c19c558276b8   traefik:v3.1   "/entrypoint.sh trae…"   12 seconds ago   Up 11 seconds   0.0.0.0:81->81/tcp, :::81->81/tcp, 0.0.0.0:443->443/tcp, :::443->443/tcp, 80/tcp, 0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   great_roentgen
```

#### 5. Acceder al Dashboard de Traefik

Abrir el navegador web y navegar a:
```
http://server_address:8080/dashboard/
```

Reemplaza server_address con la dirección IP o el nombre de dominio de tu servidor.
Usar SSH Port Forwarding

Si el acceso directo no funciona, usa SSH Port Forwarding:
    En tu máquina local, establece el reenvío de puertos:
```
ssh -L 8080:localhost:8080 user@server_address
```

Abre el navegador web y navega a:

    http://localhost:8080/dashboard/


#### 6. Verificar Configuración del Firewall

Asegúrate de que no haya reglas de firewall bloqueando el puerto 8080. Verifica y permite el puerto si es necesario:
```
sudo ufw status
```

Si el puerto 8080 no está permitido, agrega la regla:
```
sudo ufw allow 8080/tcp
```

#### 7. Verificar Logs del Contenedor de Traefik

Revisa los logs del contenedor de Traefik para cualquier error o advertencia:
```
sudo docker logs great_roentgen
```
