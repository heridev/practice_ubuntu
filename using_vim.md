#MANEJO DE VIM

rm -rf .vim
Borrar de forma forzada un archivo llamado .vim

* Para escribir es i
* para guardar y salir :wq
* para salir sin guardar :q!
* para copiar toda una linea esc + :yy
* para copiar solo cierto texto--> esc + :y + p donde quieras que aparezca
* para eliminar toda una linea dd
* borrar la letra bajo el cursor esc + x
* esc + :e . → esto hace que aparezca el listado de los archivos en donde
esta la ruta actual de directorio
* esc + :pwd -> en el directorio que actualmente te encuentras
* esc + :cd “directorio” pues empieza a utilizar ese directorio como el
base para uso de path relative
* seleccionas el texto(v) y luego “y” y con p mayuscula o minuscula pegas
antes o despues
* esc + :/term para buscar
* Para cortar y pegar primero seleccionas y luego “c”
* buscar y reemplazar-->:%s/search/replace/gc  donde g busca todas las
ocurrencias y el c hace que confirmees en cada parte que quieres reemplazar
