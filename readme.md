# NODEPOP

En esta práctica hemos realizado un API Rest en JS-NodeJS-Express-MongoDB. En este archivo indicaremos los métodos implementados en la API, así como los comandos necesarios para desplegar el servidor e inicializar la base de datos.

## Despliegue de la API Nodepop en una instancia en AWS
Para el despliegue de la aplicación hemos elegido la plataforma de Amazon, AWS, que es donde tenemos reservada nuestra instancia, instalada con Ubuntu. Como servidor de aplicaciones hemos usado Node, ya que nuestra aplicación está basada en dicho framework, y como servidor Web y proxy inverso usamos Nginx que es el encargado de recibir las peticiones Http y pasárselas a Node. También hemos alojado nuestra web en un subdominio de nuestro servidor. Las URL necesarias para acceder al sitio son:

* Si se quiere acceder a la Api de **Nodepop**, lo hacemos por la url: [https://nodepop.javierguerrero.info](https://nodepop.javierguerrero.info). Esta es la ruta principal, desde esta otra [http://nodepop.javierguerrero.info/apiv1/anuncios](https://nodepop.javierguerrero.info/apiv1/anuncios), podrías ver el listado de anuncios si estuvieses registrado como usuario. Al no estarlo no da un error 403 y puedes ver en la cabecera que responde **Express**: 

```{"message": "Lo sentimos, no tienes acceso, necesitas hacer login o registrarte"}```

* Para servir los archivos estáticos delegamos la tarea a **Nginx**, para comprobarlo en la siguiente dirección [http://nodepop.javierguerrero.info/images/caravana.jpg](http://nodepop.javierguerrero.info/images/caravana.jpg), puedes observar como la cabecera Http ***X-Owner*** es mi nombre de usuario de GitHub: JaviGuerCar.

* Por otro lado, hemos alojado nuestra web personal en un subdominio en nuestro servidor web, para verlo visita la dirección: [http://web.javierguerrero.info](http://web.javierguerrero.info) o por la IP del servidor [http://34.224.205.41](http://34.224.205.41).

#### SSL
Hemos aplicado certificados SSL a la API de NodePop, para el dominio [http://nodepop.javierguerrero.info](http://nodepop.javierguerrero.info), por lo que entrando por esa dirección te redirige siempre al servidor seguro [https://nodepop.javierguerrero.info](https://nodepop.javierguerrero.info).


## Instalación

### **Clonar el repositorio**

Clonar el repositorio, desde [https://github.com/JaviGuerCar/Nodepop.git](https://github.com/JaviGuerCar/Nodepop.git)

### **Instalar las dependencias**

Para comenzar, lo primero que debemos hacer es instalar las dependencias usadas en la API. Para ello desde la carpeta de Nodepop, ejecutamos en la consola el comando:

> ``` npm install ```

### **Inicializar la Base de Datos**

Para comenzar, debemos arrancar la base de datos y precargarla con unos datos para probar que todo funciona correctamente. La cargamos con datos extraidos de archivos Json. Cargamos tanto **usuarios, anuncios**. Para ello hemos implementado un script llamado **install_db.js**. Por tanto, para arrancar la base de datos debemos escribir el siguiente código:

> ``` npm run installDB ```

Esto hace que se ejecuten varias funciones definidas en archivos que borran datos de la base de datos si los hubiera y carga en ella los datos extraidos de los Json correspondientes. Estas funciones son **cargaAnuncios.js, cargaUsuarios.js**.

### **Arrancar el servidor**

Para arrancar el servidor debemos utilizar el siguiente comando:

> ``` npm start ```

Con esto arrancamos el servidor y ya lo tenemos listo para realizar las consultas requeridas sobre nuestra Api.

## **Autenticación de Usuarios**

### **Registro**

Para poder acceder al listado de anuncios, el usuario previamente se ha debido registrar en el servidor. Para probar esta funcionalidad lo hacemos a través de ***Postman***, haciendo una petición de tipo **POST** a la ruta: 
> ```http://localhost:3000/apiv1/registroUsuario```

Rellenamos los campos necesarios, como nombre, email y clave. Al mandar la petición nos creará un token con nuestra clave hasheada. Este token lo guardamos pues nos será necesario más adelante. El token tiene la siguiente apariencia: 

> ```eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiI1OTFkY2I0Y2RlYzY1ZDEyZjVkNzhiZWUiLCJpYXQiOjE0OTUxMjQ4MTIsImV4cCI6MTQ5NjMzNDQxMn0.JtAABjc5CIp92MqfkT90wfRPlckYQ_A2s_DgD6FgFoQ```

### **Login**

Para hacer login el usuario se ha tenido que registrar previamente. Para probarlo debemos ir a la ruta:
> ```http://localhost:3000/apiv1/loginUsuario```

Mediante *Postman* lo probamos haciendo una petición de tipo **POST**, introduciendo en el body los campos nombre y clave. Si reconoce estos campos en la base de datos, nos da el token necesario para autenticarnos y ya podemos acceder.

## **Listado de anuncios**

Para poder ver el listado de anuncios, y habiéndonos registrado previamente, de nuevo con ***Postman***, realizamos una nueva consulta de tipo **GET** a la ruta ```http://localhost:3000/apiv1/anuncios``` y en la cabecera debemos crear una clave **Authorization** y como valor introducimos **Bearer +token**, donde el token es el proporcionado en el registro de Usuario. Si no estamos logueados, nos aparecerá un mensaje de que no tenemos acceso. Si hemos introducido bien el token, nos mostrará el listado de anuncios.

### **Listado de anuncios filtrada**

Podemos usar filtros para listar los anuncios de determinada manera. Estos filtros utilizados son:

* Nombre: lista por nombre. Ej: `http://localhost:3000/apiv1/anuncios?nombre=Javier`
* Venta: si el producto está en venta o no. Ej: `http://localhost:3000/apiv1/anuncios?venta=true`
* Limit: limita la cantidad de anuncios a listar. Por ejemplo 2: `http://localhost:3000/apiv1/anuncios?limit:2`
* Skip: salta la cantidad de anuncios que le pidamos. Ej: `http://localhost:3000/apiv1/anuncios?skip:1`
* Fields: filtra por campos. Muestra los anuncios por un determinado campo. Ej: `http://localhost:3000/apiv1/anuncios?fields=nombre`
* Sort: ordena los anuncios por un campo determinado. Ej: `http://localhost:3000/apiv1/anuncios?sort=precio` Esto ordenaría por precio.
* Tags: lista por tipo de tags. Podemos poner más de uno separando por coma. Ej: `http://localhost:3000/apiv1/anuncios?tags=mobile,work`
* Precio: listamos por rangos de precios. Hemos establecido tres rangos:
	* Precios entre un rango inferior y superior. Ejemplo para precios entre 10 y 50. Ej: `http://localhost:3000/apiv1/anuncios?precio=10-50`
	* Precios mayores que un número dado. Por ejemplo precio mayor que 10. Ej: `http://localhost:3000/apiv1/anuncios?precio=10-`
	* Precios menores que que un número dado. Por ejemplo precio menor que 50. Ej: `http://localhost:3000/apiv1/anuncios?precio=-50`
	* Listar por cualquier precio: Por ejemplo precio igual a 10. Ej: `http://localhost:3000/apiv1/anuncios?precio=10`


### **Listado de tags**

Podemos listar los tags, tanto los introducidos en los anuncios en la base de datos, como un listado de los permitidos por la API. Para ello haríamos.

* Listar tags de la BD: ```http://localhost:3000/apiv1/anuncios/tags```
* Listar tags de un objeto con los tags permitidos por la API: ```http://localhost:3000/apiv1/tags```
