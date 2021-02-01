# Limpiando datos de docker del disco local.
Hoy al duplicar una base de 18 gigas me encontre con poco espacio en mi disco rigido.
Revizando encontre mas de 69 Gigas en distintos archivos de docker. 
Si bien suelo borrar los volumenes e imagenes el directorio **/var/lib/docker/overlay2** era gigante. Algunas cosas no las usaba hace 2 años.


[Es este Blog](https://medium.com/better-programming/docker-tips-clean-up-your-local-machine-35f370a01a78) encontre una solucion un poco brutal pero eficiente.
```console
filoquin@local:~$ sudo docker system prune
```

