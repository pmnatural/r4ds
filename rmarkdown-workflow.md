
# Flujo de trabajo en R Markdown

Antes, discutimos un flujo de trabajo básico para capturar tu código de R cuando trabjas interactivamente en la _consola_, la cual captura lo que trabaja en el _editor de script_. R Markdown une la _consola_ y el _editor de script_, desdibujando los limites entre exploración interactiva y captura de código a largo plazo. Puedes iterar rápidamente dentro un bloque, editando y re-ejecutando con Cmd/Ctrl + Shift + Enter. Cuando estés conforme, sigue adelante y inicia un nuevo bloque.

R Markdown es también importante ya que integra firmemente prosa y código. Esto hace que sea un gran __notebook de analísis__, porque permite desarrollar código y registrar tus pensamientos. Un *notebook* de analísis comparte muchos de los mismos objetivos que tiene un *notebook* de laboratorio clasico en las ciencias físicas. Puede:

* Registrar qué se hizo y por qué se hizo. Independientemente de cuan buena sea tu memoria, si no registras lo que haces, llegará un momento cuando hayas olvidado detalles importantes.

* Apoyar el pensamiento riguroso. Es mas probable que logres un análisis fuerte si registras tus pensamientos mientras avanzas, y continuas reflexionando sobre ellos. Esto también te ahorra tiempo cuando eventualmente escribes tu analisis para compartir con otros.

* Ayudar a que otros comprendan tu trabajo. Es raro hacer un analisis de datos por sí sola, dado que muy seguido trabajarás como parte de un equipo. Un *notebook* de laboratorio ayuda a que compartas no solo lo que has hecho, sino también por qué lo hiciste con tus colegas o compañeros de laboratorio.

Muchos de estos buenos consejos sobre el uso más efectivo de *notebooks* de laboratorio pueden también ser trasladados para utilizar un *notebooks* de analísis. He extraído de mis propias experiencias y los consejos de Colin Purrington sobre *notebooks* de laboratorio (<http://colinpurrington.com/tips/lab-notebooks>) para sugerir los siguientes consejos:

* Asegúrate de que cada *notebook* tenga un titulo descriptivo, un nombre de archivo evocativo, y un primer parrafo que describa brevemente los objetivos del analisis.

* Utiliza el campo para fecha del encabezado YAML para registrar la fecha en la que comienzas a trabajar en el *notebook*:

 ```yaml
 title: My title
 date: 2016-08-23
 ```

Utiliza el formato ISO8601 AAAA-MM-DD para que no haya ambiguidad. ¡Utilízalo incluso si no escribes normalmente fechas de ese modo!

* Si pasas mucho timepo en una idea de analísis y resulta ser un callejón sin salida, ¡no lo elimines! Escribe una nota breve sobre por qué falló y déjala en el *notebook* .Esto te ayudará a evitar ir por el mismo callejon sin salida cuando regreses a ese analisis en el futuro.

* Generalmente, es mejor que hagas la entrada de datos fuera de R. Pero si necesitas registrar un pequeño bloque de datos, establécelo de modo claro usando `tibble::tribble()`.

* Si descubres un error en un archivo de datos, nunca lo modifiques directamente,
 en su lugar escribe código para corregir el valor. Explica por qué lo corregiste.

* Antes de concluir por el día, asgurate de que puedes *knit* el *notebook* (si utilizas cacheo, asgurate de limpiar los caches). Esto te permitirá corregir cualquier problema mientras el código esta todavia fresco en tu mente.

* Si quieres que tu código sea reproducible a largo plazo (esto quiere decir que puedas regresar a ejecutarlo el mes próximo o el año próximo), necesitarás registrar las versiones de los paquetes que tu código usa. Un enfoque riguroso es usar __packrat__, <http://rstudio.github.io/packrat/>, el cual almacena paquetes en tu directorio de proyecto, or __checkpoint__, <https://github.com/RevolutionAnalytics/checkpoint>, el cual reinstala paquetes disponibles en una fecha determinada . Un truco rápido y sucio es incluir un bloque que ejecute `sessionInfo()` --- , esto no te permitirá recrear fácilmente tus paquetes tal y como estan hoy, pero por lo menos sabrás cuales eran.

* Crearás muchos, muchos, muchos *notebooks* de analisis a lo largo de tu carrera. ¿Cómo puedes organizarlos de modo tal que puedas encontrarlos otra vez en el futuro? Recomiendo almacenarlos en proyectos individuales, y tener un buen esquema de nombrado.
