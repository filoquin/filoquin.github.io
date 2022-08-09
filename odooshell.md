# Odoo shell es tu amigo.

[![Odoo shell es tu amigo](https://img.youtube.com/vi/ocpN98EDI0c/hqdefault.jpg)](https://youtu.be/ocpN98EDI0c)

# Porque usar odoo Shell y no otros metodos.
- Trabajamos mediante el ORM
- Es Transaccional, podemos volver atras sin peligo.
- Es replicable y versionable, usando scripts y archivos de texto... o simplemete copy paste si "replicable y versionable" no me parece una ventaja.
- No agregamos "artefactos artificiales" en la instalacion productiva, como una acccion programada que se ejecutara solo una vez y no me tengo que olvidar de eliminar.
- A diferencia de SQL aseguramos la integridad de los datos.
- Podemos sentirnos "Carlos Calvo el hackers" typeando alegremente en la consola de linux.


# Como ingresamos al shell de odoo.
En instalaciones planas o via virtual_env simplemente mediante el comando "odoo shell -d nombre_base_datos"

En docker depende la manera en que este construido pero... 

```console
# Ingreso al bash de docker
foo@bar:~$ sudo docker exec - it mi_docker bash
docker_id:~$ odoo shell - -no-http - d mi_db - c / opt/odoo/etc/odoo.conf - -db_host db - -db_password odoo - -db_user odoo
# O simplemente 
docker_id:~$ odoo shell -d nombre_base_datos
```
Una ves adentro trabajo como si fuera el usuario super_admin.
Tengo disponibles todas las clases, objetos, modelos y datos, para transaccionar.  
Cuando termino mi tarea, si quiero guardar los cambios, escribo self._cr.commit()

En este ejemplo, una migracion fallida, dejo todas las imagenes en el modelo product.image y no en el modelo product.template. 
Yo quiero obtener la primer imagen por producto, insertarla en el modelo correcto y luego borrarla de product.image (para que no quede repetida)

- Primero ejecuto un query para obtener los ids de la primer imagen por producto. Esto es mucho más perfomante hacerlo por sql que mediante el ORM.
- Recorro el result set y realizo un browse del producto y de la imagen.
- Asigno a image_1920 de producto el valor del mismo campo del registro de la imagen
- Borro el registro de la imagen
- Al final del loop "commiteo" mis cambios

```
self._cr.execute('select min(id), product_tmpl_id from product_image group by product_tmpl_id')
for images_id in self._cr.fetchall():
    product_id = self.env['product.template'].browse(images_id[1])
    image_id = self.env['product.image'].browse(images_id[0])
    product_id.image_1920 = image_id.image_1920
    image_id.unlink()
self._cr.commit()
```

**¡Elegante!** 

Por ultimo cuando todo esta bien en mis tests, ejecuto esto en produccion desde un archivo:

```console
odoo shell -d nombre_base_datos < mi_elegante_script.py
```
