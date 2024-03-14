# UbuntuTestServer

Esta guía define los pasos a seguir para la configuración de un servidor de test en Ubuntu 22.04.
El servidor tiene: 

 - Dos servidores Mysql en diferentes puertos
 - Un servidor MariaDB
 - Una instalación de Odoo (con su propia base de datos en contenedor PostgreSQL)
 - La instalación de Java - OpenJDK



## Instalación de Docker Engine:

https://docs.docker.com/engine/install/ubuntu/

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo docker run hello-world
```


## Ejecutar contenedores de Mysql en puertos 5x000 del Host

https://hub.docker.com/_/mysql

```
sudo docker run --detach --name=test1-mysql -p 51000:3306  --env="MYSQL_ROOT_PASSWORD=mypassword" mysql
sudo docker run --detach --name=test2-mysql -p 52000:3306  --env="MYSQL_ROOT_PASSWORD=mypassword" mysql
```



## Ejecutar contenedor de MariaDB en puerto 53000 del Host

https://hub.docker.com/_/mariadb

```
sudo docker run --detach --name=mariadb1 -p 53000:3306 --env MARIADB_ROOT_PASSWORD=my-secret-pw  mariadb:latest
```


### INSTALACION DE contenedor de ODOO 

 - https://hub.docker.com/_/odoo

```
sudo docker run -d -e POSTGRES_USER=odoo -e POSTGRES_PASSWORD=odoo -e POSTGRES_DB=postgres --name db postgres:15
sudo docker run -v odoo-data:/var/lib/odoo -d -p 8069:8069 --name odoo --link db:db -t odoo
```


## Comprobación del estado de los contenedores

```
docker ps -a 
```

Instalamos también net-tools
```
sudo apt install net-tools
```
Y comprobamos los puertos abiertos
```
sudo netstat -ntlp
```

Veremos que aparecen todos los puertos disponibles.

```
Conexiones activas de Internet (solo servidores)
Proto  Recib Enviad Dirección local         Dirección remota       Estado       PID/Program name    
tcp        0      0 0.0.0.0:52000           0.0.0.0:*               ESCUCHAR    9065/docker-proxy   
tcp        0      0 0.0.0.0:53000           0.0.0.0:*               ESCUCHAR    9495/docker-proxy   
tcp        0      0 127.0.0.1:631           0.0.0.0:*               ESCUCHAR    806/cupsd           
tcp        0      0 0.0.0.0:51000           0.0.0.0:*               ESCUCHAR    8618/docker-proxy   
tcp        0      0 0.0.0.0:8069            0.0.0.0:*               ESCUCHAR    10060/docker-proxy  
tcp        0      0 127.0.0.53:53           0.0.0.0:*               ESCUCHAR    653/systemd-resolve 
tcp6       0      0 :::52000                :::*                    ESCUCHAR    9072/docker-proxy   
tcp6       0      0 ::1:631                 :::*                    ESCUCHAR    806/cupsd           
tcp6       0      0 :::53000                :::*                    ESCUCHAR    9501/docker-proxy   
tcp6       0      0 :::51000                :::*                    ESCUCHAR    8625/docker-proxy   
tcp6       0      0 :::8069                 :::*                    ESCUCHAR    10067/docker-proxy
```

Comprobamos también la instalación de Odoo

 - http://localhost:8069



## Instalación de Java

https://ubuntu.com/tutorials/install-jre#2-installing-openjdk-jre

```
sudo apt install default-jdk
sudo java -version
```














