# Despliegue de NodePop API (Curso DevOps) Mobile Bootcamp VI



## Ejercicio 1 

**URL de acceso al API REST**

https://nodepop.primarycode.net/apiv1

**URL de acceso al elemento estático servido directamente por nginx**

https://nodepop.primarycode.net/public/droids.jpg

Aunque no se solictaba en la práctica se realizado la configuración del certificado SSL para securizar la conexión

## Ejercicio 2

**URL de acceso de página de plantilla de StartBootstrap.com alojada en el mismo servidor**

http://54.156.21.144

Se ha tenido que mantener el puerto 80 abierto para poder seguir dando servicio por IP publica directa, sin estar asociada a un dominio.

# Manual de uso de API REST NodePop 

## Consideraciones previas

La API sobreentiende que el motor de base de datos Mongo está en ejecución previamente con un script similar la que se incluye a continuación:

```
./bin/mongod --dbpath ./data/db --directoryperdb
```

El nombre que se le ha dado a la base de datos es: **nodepop**

## Consideraciones previas para despliegue

Copiar el fichero .env.example a .env y revisar los valores ahí definidos


## Procedimiento para inicializar la base de datos

Desde consola ejecutar el siguiente comando 
```
npm run installDB
```

Reiniciará las colecciones Anuncios y Usuarios presentes en la Base de Datos NodePop

Para ello ejecutará internamente el comando 

```
install_db.js
```

Presente en el directorio init

## Procedimiento para arrancar API en modo single

Desde consola ejecutar el siguiente comando 

```
npm run start
```

## Procedimiento para arrancar API en modo single y conexión https

Desde consola ejecutar el siguiente comando 

```
npm run starthttps
```

## Procedimiento para arrancar API en modo cluster

Desde consola ejecutar el siguiente comando 

```
npm run cluster
```


## Métodos disponibles en la API

## Asociados a usuarios
Ruta:
**/apiv1/usuarios**

Tipo: `POST /usuarios`

Descripción: Crea un usuaurio en base al nombre, email y clave introducido por el cliente

Parametros que se le pueden pasar:

 * *nombre*: Nombre del usuario
 * *email*: Email del usuario (será el usuario empleado para hacer login)
 * *clave*: Clave a asignar al usuario que estamos registrando. En base de datos no se
almacenará nunca la clave propiamente dicha del usuario usuario, sino el hash de dicha *clave, como medida de seguridad. Esto garantiza la no reversibilidad del datos almacenado 
 
Prerequisitos para su invocación:
* Para dar de alta un nuevo usaurio se deberán proporcionar los 3 campos de forma obligatorioa
* El campo email es único y no se puede repetir en base de datos

## Asociados a anuncios
Ruta:
**/apiv1/anuncios**

Tipo: `GET /anuncios`

Descripción:Obtener una lista de anuncios en base a unos filtros proporcionados por el usuario

Parametros que se le pueden pasar:

 * *tag*: indicaremos una tag cualquiera de las presentes en los anuncios incorporados en base de datos (inicialmente se parten solamente de 4 pero el sistema no está limitado a ese número)
 * *nombre*: Filtará los anuncios por cualquier de los anuncios presentes en base de datos cuyo nombre de producto comience por el texto indicado
 * *venta*: Podrá tomar el valor true si se trata de anuncios de venta o false si se trata de  anuncios de demanda
 * *precio*: se podrá especificar el rango de precio del producto siguiendo este patrón para indicarlo inicio-fin :
    Por ejemplo:
    10-50  buscará  anuncios  con  precio  incluido  entre  estos  valores 10 y 50
         {   precio: {   '$gte':   '10',   '$lte':   '50'   }   }
    10-   buscará   los   que   tengan   precio   mayor   que   10 
         {   precio:   {   '$gte':'10'   }   }
    -50  buscará  los  que  tengan  precio  menor  de  50
         {   precio:   {   '$lte':'50'   }   }
    50  buscará  los  que  tengan  precio  igual  a  50
         {   precio:   '50'   } 
* *sort*: Permitirá indicar el nombre del campo por el que deseamos que sean ordenados los resultados
* *skip*: Permitirá indicar a partir de que registro queremos mostrar de entre todos los resultados, este parametro en conjunción con limit nos permitirá ir paginando los resultados
* *limit*: Permitirá indicar el número de anuncios que queremos que devuelva la consulta, en conjunción con skip nos permitirá ir paginando los resultados
* *token*: En cada petición que realice deberá de proporcionar el token previamente facilitado al realizar la autenticación en el sistems (email + clave)

Ruta:
**/apiv1/anuncios/tags**

Tipo: `GET /anuncios/tags`

Descripción: Obtener una lista de las distintas tags presentes en todos los anuncios almacenados en base de datos.

Parametros que se le pueden pasar:

No requiere de ningún parametro


#Asociados a autenticación

**/apiv1/authenticate**

Tipo: `POST /authenticate`

Descripción: Permite realizar la validación de un usuario y una constraseña para poder permitir el acceso a los datos almacenados en base de datos. Siguiendo el requerimiento establecido todas las operaciones que realicen a través del API han de ser previamente autenticadas haciendo uso de JWT. Lo cuál permitirá asignar un token de autenticación al usuario y si el email y la clave proporcionada son válidas.

Parametros que se le pueden pasar:

* *email*: Email del usuario
* *clave*: Clave del usuario, a la cual se le calculará el hash y se comparará con el hash almacenado en base de datos previo a dar por autenticado al usuario.
