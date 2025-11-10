# Instalación de ERP Odoo con la herramienta Docker Compose

En este README utilizaremos la herramienta Docker Compose para crear un varios contenedores y poner en marchar un servicio de Odoo con un solo archivo YML.

<br><br>

## Preparación de el ejercicio:

<details><summary><h3>Instalación y puesta en marcha del IDE PyCharm</h3></summary>
  
  Se realizó la instalación de PyCharm y se instalaron los siguientes Plugins: 
  <br><br>
  ![PyCharm_plugins](imagenes/1.png)
  <br><br>
  ![PyCharm_plugins](imagenes/2.png)

  | Plugin        | Descripción                                                                                          |
   | ------------------- | ----------------------------------------------------------------------------------------------------- |
   | Docker               | Plugin que facilita la gestión de contenedores y compose. |
   | .env files           | Plugin que realiza autollenado de variables de entornos utilizadas en tu archivo .env                                      |

Como se puede observar en la siguiente imagen, el plugin de Docker autocompleta su lenguaje de marcado:
 ![PyCharm_plugins](imagenes/3.png)

</details>

<details><summary><h3>Contenedor del servicio Prestashop</h3></summary>
  
  Para configurar el servicio de Prestashop hacemos uso de los siguientes atributos: 
  <br><br>
  ![Prestashop](Imagenes/2.png)
  <br><br>

  | Atributo        | Valor                | Descripción                                                                                          |
   | ------------------- | ------------------------- | ----------------------------------------------------------------------------------------------------- |
   | image               | prestashop/prestashop:latest | Especifica la imagen en la que se basa el contenedor, en este caso es la ultima versión de Prestashop. |
   | container_name      | prestashop_app            | Especifica un nombre personalizado para el contenedor, en este caso es "prestashop_app", si no se especifica Docker genera uno automáticamanete.   |
   | restart             | always                    | Indica cuando debe reiniciarse el contenedor, sus valores pueden ser: no (no se reinicia), always (se reincia siempre que el contenedor se detenga), on-failure (se reinicia solo si falla), unless-stopped (se reinicia siempre a menos que se detenga manualmente). |
   | depends_on          | db                        | El atributo depends_on asegura que un contenedor se inicie antes que otro, pero no garantiza que el servicio dentro del contenedor esté realmente listo y funcionando, en este caso el servicio depende de "db" y para solucionar este problema db tiene el atributo healtcheck. |
   | condition           | service_healthy           | Con condition: service_healthy, le indicamos a Docker Compose que no inicie el contenedor de wordpress hasta que el healthcheck del contenedor db sea exitoso. |
   | ports               | "8080:80"                 | Ports mapea puertos entre el host y el contenedor. En este caso mapea el puerto 8080 del contenedor con el 80 del host. |
   | environment         |                           | Es el atributo en la que se ván a especificar las distintas variables de entorno para el correcto funcionamiento del contenedor. |
   | DB_SERVER           | db                        | Aquí se establece la variable de entorno de donde se cogerá la base de datos, en este ejemplo se usa el contenedor "db". |
   | DB_NAME             | ${MYSQL_DATABASE}         | Aquí se especifica el nombre de la base de datos, en este caso está codificado para que coja el valor de un archivo .env|
   | DB_USER             | ${MYSQL_USER}             | Aquí se especifíca el usuario de la base de datos, en el ejemplo está codificado para buscar el valor en el archivo .env|
   | DB_PASSWD           | ${MYSQL_PASSWORD}         | Aquí se especifíca la contraseña de la base de datos, en el ejemplo está codificado para buscar el valor en el archivo .env |
   | PS_INSTALL_AUTO     | 1                         | Este entorno de variable le indica al servicio que realice una instalación automática.                 |
   | PS_DOMAIN           | ${PS_DOMAIN}              | Este entorno de variable indica cual es la dirección dominio por la cuál va a ser accesible el servicio. |
   | PS_COUNTRY          | "es"                      | Esta variable cambia el país por defecto en el que se instala PrestaShop, en el ejemplo cambiamos el país a España. |
   | PS_LANGUAGE         | "es"                      | Esta variable cambia el lenguaje por defecto en el que se instala PrestaShop, en el ejemplo cambiamos a español.    |
   | ADMIN_MAIL          | ${ADMIN_MAIL}             | Esta variable sobreescribe el email por defecto del usuario admin, en este ejemplo está codificado en el archivo .env |
   | ADMIN_PASSWD        | ${ADMIN_PASSWD}           | Esta variable sobreescribe la clave por defecto del usuario admin, en este ejemplo está codificado en el archivo .env |
   | PS_FOLDER_ADMIN     | admin4577                 | Esta variable cambia el nombre de la carpeta de administrador, en nuestro caso la renombramos "admin4577".            |
   | PS_FOLDER_INSTALL   | install4577               | Esta variable cambia el nombre de la carpeta de instalación en donde se instala todo el programa de PrestaShop que se encuentra en el contenedor, en nuestro caso se renombró "install4577" |
   | volumes             | prestashop_data:/var/www/html | Permite definir una lista de volúmenes, que pueden ser bind mount o un volumen docker. Para reutilizar un volumen en múltiples servicios, se debe definir fuera del bloque services. |
   | networks            | prestashop_network        | Define las redes que se van a crear y que podrán ser usadas por los servicios.                         |
  
</details>

<details><summary><h3>Contenedor del servicio phpMyAdmin</h3></summary>
  
  Para configurar el servicio de PhpMyAdmin hacemos uso de los siguientes atributos: 
  <br><br>
  ![phpmyadmin](Imagenes/3.png)
  <br><br>

  | Atributo        | Valor                | Descripción                                                                                          |
   | ------------------- | ------------------------- | ----------------------------------------------------------------------------------------------------- |
   | image               | phpmyadmin:5              | Especifica la imagen en la que se basa el contenedor, en este caso utilizamos la versión 5 de phpmyadmin. |
   | container_name      | prestashop_phpmyadmin     | Especifica un nombre personalizado para el contenedor, en este caso es "prestashop_phpmyadmin", si no se especifica Docker genera uno automáticamanete.   |
   | restart             | always                    | Indica cuando debe reiniciarse el contenedor, sus valores pueden ser: no (no se reinicia), always (se reincia siempre que el contenedor se detenga), on-failure (se reinicia solo si falla), unless-stopped (se reinicia siempre a menos que se detenga manualmente). |
   | depends_on          | db                        | El atributo depends_on asegura que un contenedor se inicie antes que otro, pero no garantiza que el servicio dentro del contenedor esté realmente listo y funcionando, en este caso el servicio depende de "db" y para solucionar este problema db tiene el atributo healtcheck. |
   | condition           | service_healthy           | Con condition: service_healthy, le indicamos a Docker Compose que no inicie el contenedor de wordpress hasta que el healthcheck del contenedor db sea exitoso. |
   | ports               | "8081:80"                 | Ports mapea puertos entre el host y el contenedor. En este caso mapea el puerto 8081 del contenedor con el 80 del host. |
   | environment         |                           | Es el atributo en la que se ván a especificar las distintas variables de entorno para el correcto funcionamiento del contenedor. |
   | PMA_HOST            | db                        | Esta variable define la dirección/host name del servidor de la base de datos de MySQL, en este ejemplo se usa el contenedor "db". |
   | PMA_USER            | ${MYSQL_USER}             | Aquí se especifíca el usuario de la base de datos para que se abra automáticamente el servicio sin solicitar el usuario, en el ejemplo está codificado para buscar el valor en el archivo .env|
   | PMA_PASSWORD        | ${MYSQL_PASSWORD}         | Aquí se especifíca la contraseña de la base de datos para que se abra automáticamente el servicio sin solicitar la contraseña, en el ejemplo está codificado para buscar el valor en el archivo .env |
   | networks            | prestashop_network        | Define las redes que se van a crear y que podrán ser usadas por los servicios.                         |

- Para reutilizar las redes y los volumenes en multiples servicios hay que declarar las variables fuera del bloque "services" como se puede ver en la imagen.
</details>

<details><summary><h3>Creación del fichero .env</h3></summary>
  
  Para crear el archivo .env solo se tiene que crear el fichero sin nombre antes del punto: 
  <br><br>
  ![envFile](Imagenes/4.png)
  <br><br>
  En este fichero se declaran todas las variables que utilizará el compose.yml para asignarlas a sus variables de entorno.

  | Variable        | Valor                | Descripción                                                                                          |
   | ------------------- | ------------------------- | ----------------------------------------------------------------------------------------------------- |
   | MYSQL_DATABASE      | ps_sxe2526                | Nombre de la base de datos MySQL.                                                                     |
   | MYSQL_USER          | gregorioSXE               | Nombre del usuario administrador de la base de datos MySQL.                                           |
   | MYSQL_PASSWORD      | ClaveSuperFuerte1234SXE   | Contraseña de la base de datos MySQL.                                                                  |
   | MYSQL_ROOT_PASSWORD | claveSuperSegura1234      | Contraseña del usuario root de la base de datos MySQL.                                                 |
   | PS_INSTALL_AUTO     | 0                         | Variable que se le pasa a Prestashop para realizar la instalación automática, en caso de "0" Prestashop no realiza la instalación automática. |
   | PS_LANGUAGE         | es                        | Variable del lenguaje en el que se instalará el Prestashop.                                            |
   | PS_COUNTRY          | es                        | Variable del pais en el que se instalará el Prestashop.                                            |
   | ADMIN_EMAIL         | greg@prestashop.com       | Esta variable define el correo del administrador de Prestashop.                                        |
   | ADMIN_PASSWORD      | Admin1234                 | Esta variable define la contraseña del administrador de Prestashop.                                    |
   | PS_DOMAIN           | localhost:8080            | Esta variable indica cual es la dirección dominio por la cuál va a ser accesible el servicio.          |

</details>

<details><summary><h3>Capturas de puesta en marcha</h3></summary>
  
  Aquí se puede ver que phpmyadmin cargó las tablas de la base de datos correctamente y PrestaShop realizó la instalación automática sin ningún inconveniente: 
  <br><br>
  ![servicios](Imagenes/5.png)
  <br><br>
  ![servicios](Imagenes/6.png)

</details>

## Contacto
José Gregorio Cámara Depablos - [@CabbaGG](https://x.com/Geek_Cabagge) - JOS.95camara@gmail.com

Project Link: ([Instalación de servicio Prestashop con Docker Compose](https://github.com/CabbaGG2/SXE---PracticaDockerComposePrestoshop))

## Documentación

* [Documentación de Docker](https://docs.docker.com/get-started/)
