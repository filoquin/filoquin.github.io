# Corregir errores luego de un downgrade.

# psycopg2.ProgrammingError: type "you_table" already exists



Algunas veces necesitamos hacer un downgrade de la versión de odoo por alguna feature nueva que tiene “conflicto” con alguna customización o módulo de 3ro.

Los problemas pueden ocurrir tiempo después  al volver a la versión estable.

En este caso no me al actualizar el log informaba 

```
psycopg2.ProgrammingError: type "crm_lead_scoring_frequency" already exists
HINT:  A relation has an associated type of the same name, so you must use a name that doesn't conflict with any existing type.
```

Osea: la tabla crm_lead_scoring_frequency no existe en postgresql pero “quedo” en algun lado. 

*“Que stackoverflow nos acompañe”*

Lo primero que hay que hacer es mediante psql y borrar lo que quedo por ahi 

```
delete  FROM pg_type where typname~'crm_lead_scoring_frequency';
```

(Antes podemos usar un select  para saber que estamos borrando)

Después un update all debería arreglar todo.

Se los errores persisten, puedo crear las tablas e índices manualmente. 
Esa información la podemos obtener de una base “SANA” mediante pg_dump

```
pg_dump -uodoo -t 'crm_lead_scoring_frequency' --schema-only database_name
```
De más está decir que antes de manipular la Base de datos hay que hacer un bkp de la misma

Y luego no olvide que Update all es tu mejor amigo y nos ayuda a mantener una instalación sana de ODOO.  
