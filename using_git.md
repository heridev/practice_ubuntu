# MANEJANDO GIT EN UBUNTU

* first of all you need install git if you have installed git you can see
the version using de command “git --version”
* After install you need go to the directory where you have your new
project or your files to control system, after type the command “git
init”.
* ls -la
para poder ignorar algo creamos dentro de dicha carpeta el archivo con
“touch .gitignore” and that file we must write the file whose no appear
or no touch el git.

* Despues para que se registren los archivos que contiene dicha carpeta
del proyecto y los tome en cuenta
“git add .”
y si haces un archivo despues de crearlo entonces 
“git add “nombre del archivo””
“git log” muestra lo que se ha hecho

-# See the current status of your repository 
-# (which files are changed / new / deleted)
git status

## Entonces en el proceso es 
1: leer todo lo que hay y si creas uno ejemplo “touch using_git.md”,
despues das “git add using_git.md”
2: das el commit “git commit”
3: checas que cambios ha habido en la historia y uso “git log”

El git status es para ver antes de que se haga el commit para ver que
esta pendiente 

## Simple commands
Here are the few commands that you will need from the very beginning:
* git init: inside your new project folder, to create a local repository.
* git add .:
* git ls-files --cache: list the files in the current project.
* git commit: commit your code using the default editor to write your
message.
* git status: see if there is some code to be committed or not.
* git log:
* git clone git://... projectname : checkout a remote git project into
locale working copy.

If you deleted a file but you have not yet added it to the index or
committed thathe change, you can check out the file again. 


* #Delete a file
* repositorym test01
* #Revert the deletion
* git checkout test01

Para no tener que dar git commit y despues entrar al editor de esta
forma es directo el cambio y en la misma linea
git commit -m "Added al comment"

[alias]
  co = checkout
  ci = commit
  st = status
  br = branch
  hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph
--date=short
  type = cat-file -t
  dump = cat-file -p

* En caso de que te hayas equivocado sobre un cambio por ejemplo y hayas
hecho todo el proceso de editarlo, guardarlo y darle comit y se guarde
el en log entonces para deshacer la ultima operacion lo unico que
hacemos es:
* git revert HEAD
* Despues se va a abrir el editor pero le damos guardar, luego salimos y
debemos tener el archivo como esta originalmente antes del ultimo cambio
realizado y guardado con commit
 y para quitar un commit mas viejo 
git revert HEAD clave_commit.

Si hiciste un cambio pero tambien se te paso poner otro y ya hiciste el
commit para evitar que haya muchos historias pues entonces volvemos a
editar el archivo y pegamos lo que nos falta en el cambio anterior para
que aparezca como uno y en lugar de hacer el simple git commit -m
“mensaje” pones:
git commit --amend -m "cambios completos de esto y esto"

## Mover archivos y que tambien se registre dicho cambio en el git
mkdir lib
git mv hello.rb lib
git status

## Eliminar archivos que ademas lo registre el git
git rm hello.rb

# Para actualizar un repositorio clonado 
git merge origin/master

### PARA SINCRONIZAR ARCHIVOS CON UN HOSTING REMOTO

primero obtienes las claves ssh y lo configuras por alli esta el
tutorial por ejemplo en github como ese lo hice siguiente los pasos
nomas falto el de crear la carpeta .ssh antes de comenzar para si lo
vuelves a hacer ya sabes como es, despues configuras tu maquina y
generas todo y ya no te va a pedir cada vez estos datos de contra y
correo que los ocupas para el siguiente:

* git remote add origin
* git@github.com:heribertoperezci/practice_ubuntu.git
* git pull origin master //esto es para jalar todos los cambios por si
alguien hubiera cambiado algo
* git push origin master //esto es para ya subir y sincronizar con lo tuyo
y lo nuevo cambio.

###### Los demas comandos se mantienen igual que editar ver logs, add para
agregar commit, etcetera todo lo demas

* Git checkout -b “nombre_branch” //algo asi para crear un nuevo branch o
usuario 
* git checkout “nombre_branch” // para cambiar de usuario el que va a
hacer cambios


##Para poder ver cuales son los cambios entre un commit y el anterior
##antes de hacer un push

git show

## Para conocer la ayuda sobre un comando agregar -h al final
git show -h
