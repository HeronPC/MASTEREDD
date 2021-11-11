$ git merge change_class Renaming hello.rb => ruby.rb Auto-merging ruby.rb
Merge made by recursive.
ruby.rb |	6 ++----
1 files changed, 2 insertions(+), 4 deletions(-)
$ cat ruby.rb class HiWorld def self.hello
puts "Hello World from Ruby"
end end
HiWorld.hello

Observa que no hay conﬂictos de merge y el archivo que se había renombrado, ahora tiene el nombre de clase ‘HiWorld’ que se hizo en la otra rama.
Conﬂictos de merge / fusión: En situaciones en las que el mismo bloque de código se edita en diferentes ramas no hay manera de que una computadora lo resuelva, así que depende de nosotros. Vamos a ver otro ejemplo donde se cambia la misma línea en dos ramas.

$ git branch
* master
$ git checkout -b fix_readme
Switched to a new branch 'fix_readme'
$ vim README
$ git commit -am 'fixed readme title' [fix_readme 3ac015d] fixed readme title
1 files changed, 1 insertions(+), 1 deletions(-)

Ahora hemos commiteado un cambio a una línea en nuestro archivo README en una rama. Vamos a cambiar la misma línea de una manera diferente en nuestra rama ‘master’.

$ git checkout master
Switched to branch 'master'
$ vim README
$ git commit -am 'fixed readme title differently' [master 3cbb6aa] fixed readme title differently
1 files changed, 1 insertions(+), 1 deletions(-)

Vamos a combinar la primera rama en nuestra rama principal, provocando un conﬂicto de merge.




$ git merge fix_readme
Auto-merging README
CONFLICT (content): Merge conflict in README
Automatic merge failed; fix conflicts and then commit the result.
$ cat README
<<<<<<< HEAD
Many Hello World Examples
=======
Hello World Lang Examples
>>>>>>> fix_readme
This project has examples of hello world in

Podemos ver que Git inserta marcas de conﬂicto estándares, en los archivos cuando hay un conﬂicto de merge. Ahora nos toca a nosotros resolverlos. Vamos a hacerlo de forma manual, pero mirá opciones para git mergetool si queres que Git use una herramienta de merging gráﬁca (como kdiﬀ3, emerja, p4merge, etc).

$ vim README	# here I'm fixing the conflict
$ git diff
diff --cc README
index 9103e27,69cad1a..0000000
--- a/README
+++ b/README
@@@ -1,4 -1,4 +1,4 @@@
- Many Hello World Examples
-Hello World Lang Examples
++Many Hello World Lang Examples
This project has examples of hello world in

Un buen consejo para la resolución de conﬂictos de merge en Git es que si ejecutas git diﬀ, te mostrará los dos lados del conﬂicto y cómo se ha resuelto (como se muestra aquí). Ahora es el momento de marcar el archivo como resuelto. En Git lo hacemos con git add - para decirle a Git que el archivo se ha resuelto tenés que hacerle un commit.

$ git status -s
UU README
$ git add README
$ git status -s
M	README
$ git commit
[master 8d585ea] Merge branch 'fix_readme'

Y ahora ya resolvimos con éxito nuestro conﬂicto de merge y commiteado el resultado. En pocas palabras, se usa git merge para combinar otra rama en tu




rama actual. Puede ser que se de cuenta de forma automática cómo combinar de la mejor manera los diferentes cambios de ambas ramas y si no puede sólo, tendrás que resolver los conﬂictos manualmente.

git log - mostrar la historia de commits en un branch
Hasta ahora hemos estado haciendo commits de cambios de tu proyecto y cam- biando entre diferentes ramas aisladas, pero ¿Qué pasa si nos hemos olvidado de cómo llegamos a donde estamos? ¿Y si queremos saber en qué diﬁere una rama de otra? Git te proveé de una herramienta que te muestra todos los mensajes de commit que te llevan al estado actual de la rama, este comando se llama git log. Para entender el comando git log, hay que entender que información se almacena cuando se ejecuta el comando git commit para guardar los cambios realizados. Además de mostrar los archivos modiﬁcados y los mensajes de com- mit, también se muestra información sobre la persona que hizo el commit, Git también almacena el commit en que se ha basado el estado actual de la rama. Es decir, si se clona un proyecto, ¿Cuál es el commit que modiﬁcaste para llegar al commit que subiste / pusheaste? Esto es útil para saber cómo el proyecto llegó a donde está y permite a Git averiguar quién lo cambió. Si Git tiene commit que se guardó y en el que se basó, entonces se puede calcular de forma automática lo que ha cambiado. El commit en que se basó un nuevo commit se llama el “padre / parent”. Para ver una lista cronológica de los padres de cualquier rama, puede ejecutar git log cuando estás en esa rama. Por ejemplo, si ejecutamos git log en el proyecto Hello World en que hemos estado trabajando en esta sección, vamos a ver todos los commits que hemos hecho. Para ver una versión más compacta de la misma historia, podemos utilizar la opción - oneline.

$ git log --oneline
8d585ea Merge branch 'fix_readme' 3cbb6aa fixed readme title differently 3ac015d fixed readme title
558151a Merge branch 'change_class'
b7ae93b added from ruby 3467b0a changed the class name 17f4acf first commit

Lo que nos muestra es la historia del desarrollo de este proyecto. Si los mensajes de los commits son adecuadamente descriptivos, ésto nos puede informar sobre todos los cambios que se han aplicado o han inﬂuido en el estado actual de la rama y por lo tanto lo que hay en ella. También podemos utilizarlo para ver cuando la historia se bifurca y se fusionó / mergeó con la opción –graph. Aquí es el mismo comando pero con el gráﬁco de topología encendido:
$ git log --oneline --graph
⦁	8d585ea Merge branch 'fix_readme'




|\
| * 3ac015d fixed readme title
⦁	| 3cbb6aa fixed readme title differently
|/
⦁	558151a Merge branch 'change_class'
|\
| * 3467b0a changed the class name
⦁	| b7ae93b added from ruby
|/
⦁	17f4acf first commit

Ahora podemos ver más claramente cuando las ramas se separaron y luego cuando se fusionaron de nuevo. Esto es muy bueno para ver lo que ha sucedido o qué cambios se aplicaron; también es muy útil para la gestión de las ramas. Vamos a crear una nueva rama, hacer algún trabajo en ella y luego volver a nuestra rama principal para hacer algo de trabajo; ﬁnalmente vamos a ver cómo el comando log puede ayudarnos a entender lo que está sucediendo en cada uno. En primer lugar vamos a crear una nueva rama para añadir el lenguaje de programació Erlang al ejemplo Hello World, queremos hacer esto en una rama para no “ensuciar” nuestra rama estable con código que puede no funcionar por un tiempo, para que podamos cambiar “limpiamente” hacia y desde ella.

$ git checkout -b erlang
Switched to a new branch 'erlang'
$ vim erlang_hw.erl
$ git add erlang_hw.erl
$ git commit -m 'added erlang' [erlang ab5ab4c] added erlang
1 files changed, 5 insertions(+), 0 deletions(-)
create mode 100644 erlang_hw.erl

También agregamos un programa de ejemplo Haskell mientras que todavía en la rama llamada “erlang”.

$ vim haskell.hs
$ git add haskell.hs
$ git commit -m 'added haskell' [erlang 1834130] added haskell
1 files changed, 4 insertions(+), 0 deletions(-)
create mode 100644 haskell.hs

Finalmente, decidimos que queremos cambiar el nombre de clase de nuestro programa de Rubi a como era antes. Por lo tanto, podemos volver a la rama principal, cambiar eso y commitear directamente en la rama principal en lugar de crear otra rama.




$ git checkout master
Switched to branch 'master'
$ ls
README	ruby.rb
$ vim ruby.rb
$ git commit -am 'reverted to old class name' [master 594f90b] reverted to old class name
1 files changed, 2 insertions(+), 2 deletions(-)

Así que ahora supongamos que no trabajamos en el proyecto durante un tiempo porque tenemos otras cosas que hacer. Cuando regresemos queremos saber en qué estado está la rama ‘erlang’ y que hemos dejado fuera de la rama principal. Con sólo mirar el nombre de la rama, no podemos saber qué cambios hemos hecho en Haskell, pero usando git log podemos saberlo fácilmente. Si le das un nombre de rama a Git, te mostrará sólo los commits que son “accesibles” en la historia de esa rama, que son los commits que inﬂuyeron en el estado actual.

$ git log --oneline erlang 1834130 added haskell ab5ab4c added erlang
8d585ea Merge branch 'fix_readme' 3cbb6aa fixed readme title differently 3ac015d fixed readme title
558151a Merge branch 'change_class'
b7ae93b added from ruby 3467b0a changed the class name 17f4acf first commit

De esta manera, es muy fácil ver que código tenemos incluído en la rama Haskell (resaltado en la salida). Lo que es aún más genial es que podemos decirle fácilmente a Git que sólo estamos interesados en los commits que son “accesibles” desde una rama y no desde otra, es decir, qué commits son exclusivos de una rama en comparación con otra.
En este caso, si estamos interesados en mergear la rama “erlang”, queremos ver qué commits van a afectar nuestra rama cuando hagamos el correspondiente merge. La forma en que le decimos a Git es agregando un ˆ delante de la rama que no queremos ver. Por ejemplo, si queremos ver los commits que se hay en la rama ‘erlang’ y que no están en la rama ‘master’, podemos hacer erlang ˆ master, o viceversa. Tenga en cuenta que en línea de comandos, Windows trata a ˆ como un carácter especial, en cuyo caso tendrá que rodear ˆ master entre comillas.

$ git log --oneline erlang ^master
1834130 added haskell




ab5ab4c added erlang
$ git log --oneline master ^erlang
594f90b reverted to old class name

Esto nos da una buena y simple herramienta de gestión de ramas. Nos permite ver fácilmente que commits son únicos y a que ramas pertenecen, así sabemos lo que nos falta y lo que íbamos a mergear si tuviéramos que hacer el merge correspondiente. En pocas palabras se usa git log para hacer una lista de la historia de commits o una lista de personas que han hecho cambios que han llevado al estado actual de la rama. Esto permite ver cómo el proyecto llegó al estado en que se encuentra actualmente.

git tag - marcar un punto en la historia como importante
Si se llega a un punto que es importante y que se quiere recordar por siempre ese commit en especíﬁco, podés etiquetarlo con git tag. El comando tag, bási- camente, consiste en poner un marcador permanente en un commit en concreto para que pueda ser usado para comparar con otro commit en el futuro. Esto se hace a menudo cuando se libera una release (versión) o se hace una entrega.
Digamos que queremos lanzar nuestro proyecto Hello World como la versión “1.0”. Podemos marcar el último commit (HEAD) como “v1.0” ejecutando git tag -a v1.0. La opción -a signiﬁca “hacer una etiqueta anotada”, lo que te permite agregar un mensaje a la etiqueta, que es lo que casi siempre se quiere hacer. La ejecución sin -a también funciona, pero no graba cuando fue etique- tado, quien lo ha marcado, o no te permite agregar un mensaje a la etiqueta. Se recomienda crear siempre las etiquetas anotadas.

$ git tag -a v1.0

Cuando se ejecuta el comando git tag -a, Git abrirá el editor y se puede escribir un mensaje para la etiqueta, al igual que como se escribe un mensaje de commit. Ahora, cuando corremos git log –decorate, podemos ver nuestra etiqueta allí.

$ git log --oneline --decorate --graph
⦁	594f90b (HEAD, tag: v1.0, master) reverted to old class name
⦁	8d585ea Merge branch 'fix_readme'
|\
| * 3ac015d (fix_readme) fixed readme title
⦁	| 3cbb6aa fixed readme title differently
|/
⦁	558151a Merge branch 'change_class'
|\
| * 3467b0a changed the class name




⦁	| b7ae93b added from ruby
|/
⦁	17f4acf first commit

Si hacemos más commits, la etiqueta estará justo en ese commit, así que ten- emos un commit especíﬁco marcado para siempre y siempre se pueden comparar futuros commits al mismo.
Nosotros no tenemos que etiquetar el commit en el que estamos, sin embargo, si nos olvidamos de etiquetar un commit que hemos pusheado / subido, podemos etiquetarlo ejecutando el mismo comando, pero con el SHA commit al ﬁnal. Por ejemplo, digamos que habíamos lanzado el commit 558151a (varios commits atrás), pero se nos olvidó etiquetarlos en el momento. Los podemos marcar ahora:

$ git tag -a v0.9 558151a
$ git log --oneline --decorate --graph
⦁	594f90b (HEAD, tag: v1.0, master) reverted to old class name
⦁	8d585ea Merge branch 'fix_readme'
|\
| * 3ac015d (fix_readme) fixed readme title
⦁	| 3cbb6aa fixed readme title differently
|/
⦁	558151a (tag: v0.9) Merge branch 'change_class'
|\
| * 3467b0a changed the class name
⦁	| b7ae93b added from ruby
|/
⦁	17f4acf first commit

Las etiquetas apuntando a los objetos trackeados desde las cabezas de las ramas se descargarán automáticamente al hacer un fetch de un repositorio remoto. Sin embargo, las etiquetas que no son accesibles desde las cabezas de las ramas se omitirán. Si desea asegurarse de que todas las etiquetas se incluyen siempre, debe incluir la opción –tags.

$ git fetch origin --tags
remote: Counting objects: 1832, done.
 remote: Compressing objects: 100 (726/726), done. remote: Total 1519 (delta 1000), reused 1202 (delta 764)
  Receiving objects: 100 (1519/1519), 1.30 MiB | 1.21 MiB/s, done. Resolving deltas: 100 (1000/1000), completed with 182 local objects. From git://github.com:example-user/example-repo
* [new tag]	v1.0	-> v1.0
* [new tag]	v1.1	-> v1.1




Si lo que desea es una sola etiqueta, usar git fetch tag . Por defecto, las etiquetas no están incluidas cuando haces un push a un repositorio remoto. Con el ﬁn de actualizar explícitamente esto, se debe incluir la opción –tags usando git push. En pocas palabras se utiliza git tag para marcar un commit o marcar tu reporitorio como importante. Esto también te permite referirte a ese commit con una referencia más fácil de recordar que un SHA.

Intercambio y actualización de Proyectos
Git no tiene un servidor central como Subversion. Todos los comandos hasta el momento se han realizado a nivel local, sólo la actualización de una base  de datos local. Para poder colaborar con otros desarrolladores en Git, tenes que poner todos los datos en un servidor en el que los otros desarrolladores tengan acceso. La forma en que Git hace esto es sincronizando los datos con otro repositorio remoto. En estos casos no hay ninguna diferencia real entre un servidor y un cliente - un repositorio Git es un repositorio Git y se puede sincronizar con otro fácilmente.
En general, vas a hacer una serie de commits a nivel local, y luego a buscar a los datos del repositorio compartido en línea que clonaste, combinar cualquier nuevo trabajo que se agregue al tuyo, y por último hacer un push de todo esto al repositorio remoto.

git pull bajar nuevas ramas y datos del repositorio remoto
El comando git pull busca en un repositorio remoto y trata de combinar en la rama actual lo que trajo del remoto. Git tiene dos comandos para actualizar el repositorio local con un repositorio remoto. El primer comando es git fetch y se usa para sincronizar el repositorio local con otro remoto, bajando los datos no tenes a nivel local. Las ramas que se encuentran en el servidor remoto se llaman “ramas remotas” y son idénticas a las ramas locales, con excepción de que Git no te permitirá que cambies tu entorno hacia ellas - sin embargo, podes combinarlas con las tuyas locales (hacer un merge de esas ramas a las tuyas), hacerles un diﬀ con otras ramas, etc. Pero todos esos comandos los corres locales después de sincronizar el repositorio en el que estás trabajando.
El segundo comando que busca los nuevos datos de un servidor remoto es “git pull”. Este comando básicamente ejecuta un git fetch inmediatamente seguido de un git merge de la rama que esta en ese repositorio remoto y que está siendo seguida por la rama en la que te encontras parado en tu repositorio local. Al- gunos autores insisten en correr los comandos fetch y merge por separado, sin embargo la mayoría de la gente que utiliza git hace directamente un git pull cada vez que quiere actualizar su repositorio local con el repositorio remoto.




Asumiendo que tenes un repositorio remoto conﬁgurado y queres bajarte actual- izaciones, tendrías que correr el comando git fetch [alias] para avisarle a Git que baje toda las actualizaciones, y luego deberás correr git merge [alias]/[branch] para hacer un merge (sincronicar los cambios) en la rama en la que te encon- tras parado, cualquier actualización que veas en el servidor. Entonces, si por ejemplo, estuviste trabajando en un proyecto de Hello World en conjunto con muchas otras personas y queres traerte todos los cambios que se subieron desde que te actualizaste por última vez, tendrías que hacer algo similar al siguiente ejemplo, en que tomamos a github como el alias (un alias es una forma amigable de llamar a una URL de un repositorio git):

$ git fetch github
remote: Counting objects: 4006, done.
 remote: Compressing objects: 100 (1322/1322), done. remote: Total 2783 (delta 1526), reused 2587 (delta 1387)
  Receiving objects: 100 (2783/2783), 1.23 MiB | 10 KiB/s, done. Resolving deltas: 100 (1526/1526), completed with 387 local objects. From github.com:schacon/hw
8e29b09..c7c5a10	master	-> github/master 0709fdc..d4ccf73	c-langs	-> github/c-langs 6684f82..ae06d2b	java	-> github/java
⦁	[new branch]	ada	-> github/ada
⦁	[new branch]	lisp	-> github/lisp

Acá podemos ver que desde la última vez que actualizamos nuestro repositorio se agregaron o actualizaron cinco ramas. Las ramas ‘ada’ y ‘lisp’ son nuevas, y las ramas ‘master’, ‘c-langs’ y ‘java’ fueron actualizadas. En nuestro ejemplo, otros desarrolladores están subiendo cambios a ramas remotas para ser contro- ladas antes de ser mergeadas a la rama master. También podes ver los mapeos que hace git, la rama ‘master’ en el repositorio remoto, se ve como una rama llamada ‘github/master’ en el local. De esta forma podes hacer un merge de la rama ‘master’ remota en la rama ‘master’ local, corriendo el comando git merge github/master. O podes ver que commits nuevos hay en esa rama corriendo git log github/master ˆmaster. Si tu repositorio remoto se llama ‘origin’, entonces será origin/master en tal caso. Si tenes más de un repositorio remoto, podes hacer un fecth desde uno especíﬁco corriendo git fetch [alias] o podes decirle a git que sincronice con todos los remotos corriendo git fetch –all.

git push subir las nuevas ramas y cambios a un repositorio remoto
Para compartir tus commits con otros, necesitas hacer un push de tus cambios a un repositorio remoto. Para hacer esto, tenes que correr el comando git push [alias] [branch] que intentará hacer tu [branch] el nuevo [branch] en el [alias]




remoto. Veamos un ejemplo tratando de hacer un push inicial (por primera vez) de nuestra rama ‘master’ al alias remoto ‘github’.
$ git push github master
Counting objects: 25, done.
  Delta compression using up to 2 threads. Compressing objects: 100 (25/25), done. Writing objects: 100 (25/25), 2.43 KiB, done. Total 25 (delta 4), reused 0 (delta 0)
To git@github.com:schacon/hw.git
⦁	[new branch]	master -> master
Ahora si alguien clona el repositorio, entonces tendrá exactamente lo mismo que acabamos de subir junto con toda la historia. Ahora, ¿Que pasa si tenes un branch como el llamado ‘erlang’ del ejemplo anterior y queres compartir sólo ese? Podes hacer un push únicamente de ese branch:
$ git push github erlang
Counting objects: 7, done.
Delta compression using up to 2 threads.
  Compressing objects: 100 (6/6), done. Writing objects: 100 (6/6), 652 bytes, done. Total 6 (delta 1), reused 0 (delta 0)
To git@github.com:schacon/hw.git
⦁	[new branch]	erlang -> erlang
Ahora, cuando la gente haga un clon o un fetch sobre ese repositorio, entonces obtendrán un branch llamado ‘erlang’ que podrán ver y del cual podrán sin- cronizar. Podes hacer un push de cualquier branch a cualquier repositorio re- moto al que tengas permisos de escritura. Si tu branch ya se encuentra en el repositorio remoto, git va a tratar de actualizarlo, si no se encuentra, entonces lo va a crear. Por último, un problema frecuente se da cuando queres hacer  un push de algún cambio sobre una rama que alguien actualizó antes que vos y vos no tenes esa actualización. Si tratas de hacer un push en ese caso Git por default no te va a dejar pisar esas actualizaciones. Para resolver este problema, tenes que actualizar tu copia haciendo un pull de los cambios (o un fetch-merge) y luego hacer el push. Esto es lo que pasará si intentas de hacer un push en una rama que tenes desactualizada con respecto al servidor remoto:
$ git push github master
To git@github.com:schacon/hw.git
! [rejected]	master -> master (non-fast-forward)
error: failed to push some refs to 'git@github.com:schacon/hw.git'
To prevent you from losing history, non-fast-forward updates were rejected
Merge the remote changes before pushing again.	See the 'Note about fast-forwards' section of 'git push --help' for details.




Entonces podes arreglar esto corriendo git fetch github; git merge github/master y luego haciendo nuevamente el push.

Herramienta visual:  Rabbit VCS
Si bien recomendamos particularmente la línea de comandos para trabajar con git, también existen herramientas visuales que nos permiten trabajar de una forma más “amigable”. Una herramienta muy conocida y usada es RabbitVCS, a continuación mostramos paso a paso como sería uno de los ﬂujos explicados anteriormente con una herramienta visual como esta:

⦁	Clonamos un repositorio: Especiﬁcamos la URL, el usuario y la contraseña para que Git veriﬁque los permisos.
 

⦁	Realizamos un cambio en un archivo: Podemos ver que la herramienta nos marca los archivos modiﬁcados con un icono color rojo.
⦁	Hacemos un commit del cambio realizado: elegimos la opción “conﬁrmar” en caso de que tengamos lenguaje español.
⦁	Conﬁrmamos el commit: Le agregamos un mensaje que describa lo que estuvimos haciendo y conﬁrmamos.
⦁	Hacemos un push: Se subirá al servidor remoto todo lo commiteado en el repositorio.

Para más información y para bajarse la herramienta visitar el sitio oﬁcial de la herramienta: http://rabbitvcs.org/

Referencias
⦁	Se utilizaron algunas imágenes cuya fuente provienen del sitio ⦁	git-scm.com.
⦁	La información troncal del documento se redactó basándose en el sitio oﬁcial de gitref.
⦁	El contenido está bajo la licencia Creative Commons Atribución- CompartirIgual 4.0 Internacional.
