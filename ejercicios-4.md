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
6. Todas las palabras que empiezan y acaban por la misma letra (volver a este punto al acabar toda la lección). 

En este caso pienso que un grupo de captura con grep podría hacer el trabajo. Usó los flags de grep: -c para que cuente e -i para que no considera la diferencia de mayúsculas y minúsculas. La expresión regular empieza por ``` ^ ``` indicando principio, despúes paréntesis del grupo de captura y corchetes del rango, donde incluyo todas las letras del teclado para que case con un caracter de esa clase. Despúes vendría qualquier número de letras o dígitos y recuperaríamos lo capturado con \1 al final $. Pero me salen cero resultados, lo que no es cierto. :-(

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -ci -E '^([qwertyuiopasdfghjklzxcvbnm])\w*\1$' aquella_voluntad.txt 
0
```
## Ejercicio 2
¿Cuántos gene_ids existen con varios ceros seguidos en los dos gtfs (Humano y Drosophila)?. ¿Cuáles son? ¿Cuántas veces aparece cada uno en el .gtf dado?
Explora el fichero de anotaciones para ver si existen otros gene_ids con muchos números seguidos iguales.

### Respuesta ejercicio 2

Para seleccionar los genes_ids que cumplen con esas condiciones usamos grep con el flag -o y la expresión regular:  tras gene_id " cualquier número de caracteres dígeitos o letras\w* 0 y otro o más 0 seguido de cualquier número de dígitos y comillas dobles \d+". La salida la ordenamos con sort y con uniq -c contamos cuantas veces aparece y lo guardamos en un archivo con tee y contamos el número de líneas del archivo. Para el caso del gtf humano se usa zgrep por estar comprimido.

```
nguerrero@nguerrero-VirtualBox:~/gtfs$ grep -oE 'gene_id "\w*00+\d*"' Drosophila_melanogaster.BDGP6.28.102.gtf | sort | uniq -c | tee gene_ids_droso | wc -l
183
```
```
nguerrero@nguerrero-VirtualBox:~/gtfs$ zgrep -oE 'gene_id "\w*00+\d*"' Homo_sapiens.GRCh38.102.gtf.gz | sort | uniq -c | tee gene_ids_human | wc -l
630
```
Exploramos otras repeticiones de número haciendo uso de expresiones como 1 {3,} que dentro de la expresiones regular equivale a 3 o más dígitos 1. Escribimos un piplein similar al anterior y vemos como hay muchas gene-ids con repeticiones de números, al menos tres copias 

```
nguerrero@nguerrero-VirtualBox:~/gtfs$ grep -E 'gene_id "\w*\0{3,}|1{3,}|2{3,}|3{3,}|4{3,}|5{3,}|6{3,}\d*"' Drosophila_melanogaster.BDGP6.28.102.gtf | sort | uniq -c | wc -l
118756
```

```
nguerrero@nguerrero-VirtualBox:~/1-toma-de-contacto-cris-nestor$ grep -ci -E '^([qwertyuiopasdfghjklzxcvbnm])\w*\1$' aquella_voluntad.txt 
0
```
## Ejercicio 3

Crea un pipeline que convierta un fichero fasta con secuencias partidas en múltiples líneas en otro sin saltos de línea. 
Al final, para cada secuencia, imprimirá su nombre y el número de caracteres que tenga. 




### Respuesta ejercicio 3

Creo un archivo de ejemplo en formato fasta con dos secuencias:
```
nguerrero@nguerrero-VirtualBox:~/4-expresiones-regulares-sed-awk-cris-nestor$ cat fasta_ex3 
>GeneID "001"
AAAAAA
TTTTTT
CCCCCC
GGGGGG

>GeneID "002"
ATCGACAGAGTAGAGATAGAC
ACGTACCAACACACACGTGAC
ACGTACACAGTGTGTGGACTT
```
Utilizo tr con el flag para borrar -d sobre los saltos de líneas \n

```
nguerrero@nguerrero-VirtualBox:~/4-expresiones-regulares-sed-awk-cris-nestor$ cat "fasta_ex3" | tr -d '\n'  
>GeneID "001"AAAAAATTTTTTCCCCCCGGGGGG>GeneID "002"ATCGACAGAGTAGAGATAGACACGTACCAACACACACGTGACACGTACACAGTGTGTGGACTT
```
Ahora debo imprimir el número de caracteres de la secuencia y el nombre, pero no se me ocurrió como puedo seleccionar cada secuencia por separado.

## Ejercicio 4
En la sección 3.1., convertimos la cadena `chr1:3214482-3216968` a un formato tabular con `sed`. Sin embargo, existen otras maneras en las que podríamos haber obtenido el mismo resultado final. ¿Se te ocurren algunas? Recuerda que puedes usar el flag `g`, o puedes encadenar distintas llamadas a `sed` con tuberías si ves que meterlo todo en una única expresión regular se te antoja complicado. 

### Respuesta ejercicio 4

un opción puede ser con tr

```
nguerrero@nguerrero-VirtualBox:~/4-expresiones-regulares-sed-awk-cris-nestor$ echo "chr1:3214482-3216968" | tr ':' '\t' | tr '-' '\t'
chr1  3214482  3216968
```
