
# Pipes

## Introducción

Un pipe es una herramienta poderosa para claramente expresar una secuencia de operaciones múltiples. Hasta aquí, has venido usándolas sin saber cómo funcionan, o cuales son las alternativas. Ahora, en este capítulo, es tiempo de explorar las Pipes en más detalle. Aprenderás las alternativas a las Pipes, cuando no deberías usar las pipes y algunas herramientas relacionadas útiles.

### Prerequisitos

Un pipe, `%>%`, viene del paquete __magrittr__ de Stefan Milton Bache. Los paquetes en el tidyverse cargan %>% automáticamente, así que no cargaras magrittr explícitamente. Aquí, sin embargo, nos enfocamos en la tubería, y no estamos cargando otros paquetes, por lo que nosotros lo cargaremos explícitamente


```r
library(magrittr)
library(datos)
```

## Alternativas a los pipes

El objetivo de un pipe es ayudarte a escribir código en una manera que sea más fácil de leer y entender. Para ver porque un pipe es tan útil, vamos a explorar diferentes formas de escribir el mismo código. Usemos el código para contar una historia acerca de un pequeño conejito llamado Foo Foo:

> Pequeño conejito Foo Foo
> Fue saltando por el bosque
> Recogiendo ratones del campo
> Y golpeándolos en la cabeza

Este es un poema popular para los niños que se acompaña mediante gesticulación con las manos.

Empezaremos por definir un objeto que representa un pequeño conejito Foo Foo:


```r
foo_foo <- pequeño_conejito()
```

Y usaremos una función para cada verbo: `saltar()`, `recoger()`, and `golpear()`. Usando este objeto y estos verbos, hay (al menos) cuatro maneras en las que podemos volver a contar la historia en código:

1.	Guardar cada paso intermedio como un nuevo objeto.
2.	Sobreescribir el objeto original muchas veces.
3.	Componer las funciones.
4.	Usar un pipe.

Trabajaremos a través de cada enfoque, mostrándote el código y hablando de las ventajas y desventajas.


### Pasos intermedios

El enfoque más sencillo es guardar cada paso como un nuevo objeto:


```r
foo_foo_1 <- saltar(foo_foo, a_traves_del = bosque)
foo_foo_2 <- recoger(foo_foo_1, up = ratones_del_campo)
foo_foo_3 <- golpear(foo_foo_2, en_la = cabeza)
```

La máxima desventaja de esta manera es la obligación de nombrar cada paso intermedio. Si hay nombres naturales, es una buena idea, y deberías hacerlo. Pero muchas veces, como en este ejemplo, no hay nombres naturales, y agregando sufijos numéricos para hacer los nombres únicos. Esto conduce a dos problemas:

1. El código está abarrotado con nombres poco importantes.

1. Hay que incrementar cuidadosamente el sufijo en cada línea.

Cada vez que escribo código como este, invariablemente uso el número incorrecto en una línea y luego pierdo 10 minutos rascándome la cabeza tratándome de dar cuenta que hay de malo con mi código.
Probablemente te preocupes que esta forma cree muchas copias de tus datos y ocupe demasiada memoria. Sorprendentemente, este no es el caso. Primero, ten en cuenta que preocuparse proactivamente acerca de la memoria no es una manera útil de invertir tu tiempo: preocupate acerca de ello cuando se convierta en un problema (ej. cuando te quedes sin memoria), no antes. En segundo lugar, R no es estúpido, compartirá las columnas a lo largo de los marcos de datos, cuando sea posible. Veamos a una tubería de manipulación de datos cuando agregamos un nueva columna al `ggplot2::diamantes`:


```r
diamantes2 <- diamantes %>%
  dplyr::mutate(precio_por_quilate = precio / quilate)
#> Registered S3 methods overwritten by 'ggplot2':
#>   method         from 
#>   [.quosures     rlang
#>   c.quosures     rlang
#>   print.quosures rlang

pryr::object_size(diamantes)
#> Registered S3 method overwritten by 'pryr':
#>   method      from
#>   print.bytes Rcpp
#> 3.46 MB
pryr::object_size(diamantes2)
#> 3.89 MB
pryr::object_size(diamantes, diamantes2)
#> 3.89 MB
```

`pryr::object_size()` ofrece la cantidad de memoria ocupada por todos los argumentos. Este resultado puede parecer contraintuitivo al principio:

* `diamantes` ocupa 3.46 MB,
* `diamantes2` ocupa 3.89 MB,
* `diamantes` y `diamantes2` juntos ocupan 3.89 MB!

¿Cómo funciona esto? Bien, `diamantes2` tiene 10 columnas en común con `diamantes`: no hay necesidad de duplicar los datos, así que ambos dataframes tienen variables en común. Estas variables solo serán copiadas si tu modificas una de ellas. En el siguiente ejemplo, nosotros modificamos un solo valor en `diamantes$quilates`. Esto significa que la variable `carat` no será más compartida entre dos dataframes, y una copia debe ser realizada. El tamaño de cada dataframe no se cambia, pero el tamaño colectivo se incrementa:


```r
diamantes$quilates[1] <- NA
#> Warning: Unknown or uninitialised column: 'quilates'.
pryr::object_size(diamantes)
#> 3.67 MB
pryr::object_size(diamantes2)
#> 3.89 MB
pryr::object_size(diamantes, diamantes2)
#> 4.1 MB
```

(Notar que usamos `pryr::object_size()` aquí, no en la construcción `object.size()`. `object.size()` solo toma un solo objeto así que puede computar como los datos son compartidos a través de múltiples objetos.)

### Sobreescribir el original

En vez de crear objetos en cada paso intermedio, nosotros podemos sobreescribir el objeto original:


```r
foo_foo <- hop(foo_foo, a_traves_del = bosque)
foo_foo <- scoop(foo_foo, arriba = ratones_del_campo)
foo_foo <- bop(foo_foo, en_la = cabeza)
```

Esto es menos tipeo (y menos pensamiento), así que es menos probable que cometas errores. Sin embargo, hay dos problemas:

1. Debugging es doloroso: si cometes un error vas a necesitar correr de nuevo el pipeline desde el principio.

1. La repetición de un objeto siendo transformado (hemos escrito foo_foo 6 veces!) oscurece lo que está siendo cambiado en cada línea.

### Composición de funciones

Otro enfoque es abandonar la asignación y encadenar las llamadas a las funciones juntas:


```r
saltar(
  recoger(
    golpear(bla_bla, por_el = bosque),
    arriba = raton_de_campo
  ),
  en = la_cabeza
)
```

Aquí está la desventaja es que se debe leer de dentro hacia afuera, de derecha a izquierda, y que los argumentos terminan separados
[dagwood sandwhich](https://en.wikipedia.org/wiki/Dagwood_sandwich)). En resumen, este código es difícil para un ser humano.

### Uso de pipe

Finalmente, podemos usar pipe:


```r
foo_foo %>%
  saltar(a_través_del = bosque) %>%
  recoger(arriba = ratones_campo) %>%
  golpear(en = la_cabeza)
```

Esta es mi forma preferida, ya que se enfoca en los verbos, no en los sustantivos. Puedes leer esto como una serie de composición de funciones como un set de acciones imperativas. Foo salta, luego recoge y luego golpea. La desventaja, por supuesto, es que necesitas estar familiarizado con el uso de los pipes. Si nunca has visto `%>%` antes, no tendras idea acerca de lo que realiza el código. Afortunadamente, la mayoría de la gente entiende la idea facilmente, así que cuando compartes tu código con otros que no están familiarizados con los pipes, tu pueden enseñarles facilmente.

El pipe trabaja realizando una "transformación léxica": detrás de escena, magrittr reensambla el código en el pipe a una forma que funciona por sobreescritura en un objeto intermedio. Cuando se ejecuta un pipe como el de arriba, magrittr hace algo como esto:


```r
mi_pipe <- function(.) {
  . <- saltar(., a_traves_del = bosque)
  . <- recoger(., arriba = ratones_campo)
  golpear(., en = la_cabeza)
}
mi_pipe(foo_foo)
```

Esto significa que un pipe no trabajara para dos clases de funciones:
1. Funciones que usan el entorno actual. Por ejemplo, `asignar()`creará una nueva variable con el nombre dado en el entorno actual:


```r
assign("x", 10)
x
#> [1] 10

"x" %>% assign(100)
x
#> [1] 10
```

El uso de la asignación con el pipe no funciona porque lo asigna a un entorno temporario usado por `%>%`. Si quieres usar la asignación con pipe, debes explicitar el entorno:


```r
env <- environment()
"x" %>% assign(100, envir = env)
x
#> [1] 100
```

Otras funciones con este problema incluyen `get()` y `load()`.

1. Funciones que usan una evaluación perezosa. En R, los argumentos de las funciones son solamente computados cuando la función los usa, no antes de llamar a la función. El pipe computa cada elemento de a turno, así que no puedes confiar en este comportamiento.

Un lugar para este problema es `tryCatch()`, que te permite capturar y manejar errores:


```r
tryCatch(stop("!"), error = function(e) "An error")
#> [1] "An error"

stop("!") %>%
  tryCatch(error = function(e) "An error")
#> Error in eval(lhs, parent, parent): !
```

Hay una relativa gran cantidad de funciones con este comportamiento, incluyendo a `try()`, `suppressMessages()`, y `suppressWarnings()` en R base.

## Cuando no usar el pipe

El pipe es una herramienta poderosa, pero no es la única herramienta a tu disposición, ¡y no soluciona ningún problema! Los pipes son mayoritariamente usados para reescribir una secuencia lineal bastante corta de operaciones. Pienso que deberías buscar otra herramienta cuando:

* Tus pipes no son más largas que (digamos) 10 pasos. En ese caso, creamos objetos intermedios con nombres significativos. Esto hará el debugging más fácil, porque puede más facilmente chequear los resultados intermedios, y hace más simple entender tu código, porque los nombres de las variables pueden ayudar a comunicar la intención.
* Tienes múltiples inputs y outputs. Si no hay un objeto primario para ser transformado, pero sí dos o más objetos siendo combinados juntos, no uses el pipe.
* Si estas empezando a pensar acerca de un gráfico directo con una estructura compleja de dependencia. Los pipes son fundamentalmente lineales y expresan relaciones complejas que típicamente llevarán a un código confuso.

## Otras herramientas de magrittr

Todos los paquetes en tidyverse automaticamente harán `%>%` disponible para tí, así que normalmente no se carga magrittr explicitamente. De todas formas, hay otras herramientas útiles dentro de magrittr que podrías querer utilizar:

* Cuando se trabaja en un pipe más complejo, es a veces más útil llamar a una función por sus efectos secundarios. Tal vez quieras imprimir el objeto actual, o graficarlo, o guardarlo en el disco. Muchas veces, estas funciones no devuelven nada, efectivamente terminando el pipe.

Para trabajar en torno a este problema, puedes usar el pipe "T". `%T>%`trabaja igual que `%>%` excepto que este devuelve el lado izquierdo en vez del lado derecho. Se lo llama "T" porque literalmente tiene la forma de una T.


```r
rnorm(100) %>%
  matrix(ncol = 2) %>%
  plot() %>%
  str()
#>  NULL

rnorm(100) %>%
  matrix(ncol = 2) %T>%
  plot() %>%
  str()
#>  num [1:50, 1:2] -0.387 -0.785 -1.057 -0.796 -1.756 ...
```

<img src="pipes_files/figure-html/unnamed-chunk-13-1.png" width="70%" style="display: block; margin: auto;" /><img src="pipes_files/figure-html/unnamed-chunk-13-2.png" width="70%" style="display: block; margin: auto;" />

* Si estas trabajando con funciones que no están basados en una API (ej., tu pasas vectores individuales, no un dataframe o expresiones a ser evaluadas en el contexto de un dataframe), pueden encontrar `%$%` útil. El operador explota las variables en un dataframe así que te puedes referir a ellos explícitamente. Esto es útil cuando se trabaja con muchas funciones en R base:


```r
mtautos %$%
  cor(cilindrada, millas)
#> [1] -0.848
```

* Para asignaciones magrittr provee el operador `%<>%` te permite reemplazar el código como:


```r
mtautos <- mtautos %>%
  transform(cilindros = cilindros * 2)
```

con


```r
mtautos %<>% transform(cilindros = cilindros * 2)
```

No soy partidario de este operador porque pienso que una asignación es una operación especial que siempre debe ser clara cuando sucede.
En mi opinión, un poco de duplicación (ej repetir el nombre de un objeto dos veces) está bien para lograr hacerlo más explícito.
