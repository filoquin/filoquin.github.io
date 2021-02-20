# Crear patch desde un PR

Me gusta git-aggregator para mantener la lista de dependencias y repositorios pero **para los gustos colores** ¿No?

Varias veces me preguntaron como mesclar un pr en miramaen produccion a la manera de los *Indiana Coders*
Por ejemplo yo este repositorio clonado

```console
git clone -b 13.0 https://github.com/ingadhoc/odoo-argentina-ce
```
Y quiero aplicar el PR de QR que esta en esta URL. https://github.com/ingadhoc/odoo-argentina-ce/pull/17
solo debo agregar al PR una extención .patch y guandar el archivo
```console
cd ~
wget https://github.com/ingadhoc/odoo-argentina-ce/pull/17.patch
```
despues volver a la carpeta *pachear* mi modulo.

```console
cd odoo-argentina-ce
git am ~/17.patch
```
Claro que cuando quiera volver a la version estable debo hacer un checkuot de mi rama- 


```console
git checkout origin 13.0
```
Siempre es más elegante y recomendable usar Git Aggregator.
