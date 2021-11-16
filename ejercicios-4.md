# Sesión IV - Expresiones regulares: grep, sed y awk

Herramientas computacionales para bioinformática: UNIX, expresiones regulares y shell script

Edita esta plantilla en formato markdown [Guía aquí](https://guides.github.com/features/mastering-markdown/) como se pide en el guión. 
Cuando hayas acabado, haz un commit de tus cambios y súbelos al repositorio antes de la fecha de entrega señalada. 

======================================

**Añade por favor capturas de pantalla y el código de tus pipelines.**


## Ejercicio 1
Usando el fichero `aquella_voluntad.txt`, identifica usando grep:

1. El número de líneas que terminan por `o`. 
2. El número de líneas que terminan por `o` o por `a`. 
3. El número de líneas pares que terminan por `o` o por `a`
4. Todas las palabras que empiezan y acaban por `s` (ordenadas alfabéticamente)
5. Todas las palabras que no empiezan por t y acaban por `s`. (ordenadas por número de línea)
6. Todas las palabras que empiezan y acaban por la misma letra (volver a este punto al acabar toda la lección). 

### Respuesta ejercicio 1

1. El número de líneas que terminan por `o`.

Usamos grep con el flag de contar -c y como argumento usamos o$ que indica la letra o al final de la linea

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -cE 'o$' aquella_voluntad.txt 
38
```
2. El número de líneas que terminan por `o` o por `a`.

Similar al anterior pero adicionamos |a$ al argumento de grep, para que ademas de las filas que terminan en o también saque aquellas que terminan en a.

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -cE 'o$|a$' aquella_voluntad.txt 
85
```
3. El número de líneas pares que terminan por `o` o por `a`

Generamos un pipeline de dos pasos, el primero es similar al anterior, pero sumamos el flag -n de grep lo que va a adicionar el número de línea a la salida, la segunda parte es otra llamada a grep para que escoja y cuente con el flag -c aquellas líneas que comienzan por cualquier número de digitos \d*, y tienen un número par antes de los dos puntos [02468]: para acabar la línea con cualquier tipo y número de caracteres .*

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -nE “o$|a$” aquella_voluntad.txt | grep "\d*[02468]:.*" -c
45
```
4. Todas las palabras que empiezan y acaban por `s` (ordenadas alfabéticamente)

Usamos grep de nuevo para que nos de las palabras con el flag -o. La expresión regular se leería del siguiente modo: uno o más espacios \s+ seguidos de de s, después cualquier número de caracteres letra o dígitos \w* , la letra s y después cualquier número de espacios. Esa salida se ordena con sort y se escoge sólo una copia de cada palabra con uniq.  

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -oE "\s+s\w*s\s+" aquella_voluntad.txt | sort | uniq
 sauces 
 selvas 
 sendos 
 silvestres 
 sombras 
 sus 
```
5. Todas las palabras que no empiezan por t y acaban por `s`. (ordenadas por número de línea)

Usamos grep con el flag -o para obtener las palabras que sigan la expresion regular: que no comienze por T o t [^tT] seguida de cualquier número de letras \w*, la letra s y después uno o más espacios \s+. Con tee en el pipeline se genera un archivo para consultar la lista y contamos las líneas para tener un idea del número de palabras.

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -o -E "[^tT]\w*s\s+" aquella_voluntad.txt | tee lista_palabras | wc -l
200
```
## Ejercicio 2
¿Cuántos gene_ids existen con varios ceros seguidos en los dos gtfs (Humano y Drosophila)?. ¿Cuáles son? ¿Cuántas veces aparece cada uno en el .gtf dado?
Explora el fichero de anotaciones para ver si existen otros gene_ids con muchos números seguidos iguales.

### Respuesta ejercicio 2


## Ejercicio 3

Crea un pipeline que convierta un fichero fasta con secuencias partidas en múltiples líneas en otro sin saltos de línea. 
Al final, para cada secuencia, imprimirá su nombre y el número de caracteres que tenga. 

### Respuesta ejercicio 3


## Ejercicio 4
En la sección 3.1., convertimos la cadena `chr1:3214482-3216968` a un formato tabular con `sed`. Sin embargo, existen otras maneras en las que podríamos haber obtenido el mismo resultado final. ¿Se te ocurren algunas? Recuerda que puedes usar el flag `g`, o puedes encadenar distintas llamadas a `sed` con tuberías si ves que meterlo todo en una única expresión regular se te antoja complicado. 

### Respuesta ejercicio 4

