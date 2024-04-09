---
title: UD6 - Scripting
#subtitle: Creación de scripts en bash
author:
  - Brayan Mecinas Pardo
abstract: Introducción a la creación de scripts con bash
lang: ca
header-includes:
  - \usepackage{lastpage}
  - \usepackage{awesomebox}
titlepage: true
titlepage-rule-height: 0
#titlepage-rule-color: 65309
titlepage-background: ../img/portada_canva_2.png
toc-own-page: true
toc-title: Continguts
header-left: UD6 - Scripting
#header-center: \thesection
header-right: Curs 2023-2024
footer-left: IESMRE - bmecinaspardo
#footer-center: Scripting C
footer-right:  \thepage de \pageref{LastPage}
page-background: ../img/marca_agua-removebg-preview.png
page-background-opacity: [0.05]

pandoc-latex-environment:
  noteblock: [note]
  tipblock: [tip]
  warningblock: [warning]
  cautionblock: [caution]
  importantblock: [important]
...

# Concepto de script
Los scripts son guiones que contienen una serie de órdenes y estructuras organizadas secuencialmente.

Principalmente, presentan las siguientes características:

- Se generan con gran facilidad y son bastantes funcionales.
- Se ejecutan de forma lineal-secuencial orden tras orden.

Los lenguajes de scripts son interpretados, no compilados; lo que quiere decir que sus órdenes secuenciales son interpretadas por un sistema o programa padre. Actualmente, son muy conocidos lenguajes-scripts como JavaScript, PHP, ASP, etc., para el desarrollo de webs.

El concepto de los scripts en Linux, es bastante análogo al de los ficheros por lotes (.bat) de MSDOS, siendo los scripts bastante más potentes por la cantidad de estructuras, bucles, y demás recursos que permiten utilizar.

El Shell es la interfaz con la que el usuario-programador se comunica con el sistema operativo directamente. Principalmente, es el elemento encargado de las siguientes funciones:

* Interpretar comandos: Acepta comandos y los ejecuta; si se trata de comandos de los usuarios, como los scripts, los envía al Kernel para que sea este quien los ejecute y procese.
* Actuar como un intérprete de programación de un lenguaje de alto nivel.

Existen distintos tipos de Shell, cada uno con una sintaxis diferente, pero con la misma filosofía: sh, csh, bash y tcsh.

Podéis encontrar más información sobre shells en [este enlace](https://www.ibm.com/docs/es/aix/7.2?topic=administration-operating-system-shells)

# Trabajo con scripts
## Tipos y ejecución de scripts

La primera tarea será crear el script utilizando para ello cualquier editor que acompaña a Linux: nano, gedit, vi, etc.
Al inicio de cada script, se identifica el tipo de fichero. En el caso de scripts tenemos las siguientes opciones:

| Identificación    | Descripción       |  
|-------------------|-------------------|
| #!bin/bash        | script de bash    |
| #!bin/sh          | script de shell   |
| #!usr/bin/python  | script de python  |

Para ejecutar el script, se presentan dos opciones:

-  Asignarle el permiso de ejecución al fichero y luego ejecutarlo

```console
$ chmod 777 miscript.sh
$ ./miscript.sh
```
-  Ejecutarlo con bash

```console
$ bash miscript.sh
```
## Variables
Las variables de shell pueden servir tanto para controlar el entorno (variables de entorno), como para utilizarlas en scripts, Y pueden ser de dos tipos:

- Locales al shell actual (visibles con set)
- Globales a todos los shells (variables de entorno, visibles con env), Las variables empiezan por una letra o por ```_```, y generalmente se escriben en mayúsculas. Después del primer carácter, podemos poner letras, números o el carácter ```_```.

Las operaciones que podemos hacer con las variables son:

- Asignarles un valor: `variable=valor`
- Recuperar su valor: `$variable o ${variable}`
    - Las llaves se utilizan para recuperar un valor en expresiones más largas: `echo${var}hola`
- Obtener la longitud en caracteres de una variable:`echo ${#variable}`
- Eliminar una variable: `unset variable`
- Definir una variable como solo lectura: `readonly variable`

### Variables de entorno
Cada shell se ejecuta en un entorno de ejecución determinado, que especifica determinados aspectos de funcionamiento mediante lo que se conoce como variables de entorno o variables globales. Veamos algunas de estas variables:

| Variable          | Descripción                   | 
|-------------------|-------------------------------|
| HOME              | Directorio del usuario        |
| MANPATH           | Ruta páginas del manual       |
| SHELL             | Shell por defecto             |
| PS1/PS2           | Prompts primario y secundario |
| USERNAME          | Nombre del usuario            |
| LANG              | Localización y codificación   |
| PWD               | Directorio actual             |
| LC_*              | Aspectos de localización      |
| PATH              | Ruta de los ejecutables       |
| LANGUAGE          | Localización                  |

Para ver todas las variables de entorno, hacemos uso de la orden `env`. Además, si queremos definir una nueva variable de entorno, podemos hacer uso de la orden `export`. Con esto, la variable exportada será visible tanto en el shell actual como en los hijos

## Expansiones del Shell
### Expansiones de parámetros
Cuando accedemos a una variable con el símbolo `$`, estamos haciendo uso de la expansión de parámetros, que no es más que la sustitución del nombre de la variable por su valor.

Cuando utilizamos las llaves `{ }` para la expansión, lo que hacemos es el nombre del parámetro (variable) o símbolo que queremos expandir. Veamos un ejemplo:

```bash
A=David
echo "Hola ${A}, bienvenido/a"
```
??? note "Nota"
    Es posible que en muchas ocasiones se reconozca la variable usando solo `$variable`en lugar de `${variable}`, pero es recomendable usar la expansión siempre con las llaves `{}`:::

### Expansiones de órdenes
Con la expansión de órdenes podemos reemplazar una orden por su salida. Para eso podemos hacer uso de las siguientes opciones 
```bash
$(orden)
```
o
```bash
`orden`
```

Veamos un ejemplo sencillo con el comando date:
```console
$ date
jueves, 14 de febrero de 2019, 11:34:07 CET
$ echo date
date
$ echo `date`
jueves, 14 de febrero de 2019, 11:35:10 CET
$ echo $(date)
jueves, 14 de febrero de 2019, 11:36:20 CET
$ echo "Hoy es `date +%A`"
Hoy es jueves
```
### Expansiones de llaves
Con la expansión de llaves podemos generar cadenas de texto arbitrarias, mediante la combinación de los elementos que coloquemos dentro de las llaves.

```console
$ echo a{d,c,b}e
ade ace abe
$ echo \/usr\/share{\/icons,\/applications}
/usr/share/icons /usr/share/applications
```

Fijaos que escapamos la `/` con el carácter `\`.

### Expansión aritmética
Con la expansión aritmética substituimos una expresión aritmética por su resultado. Para utilizarla podemos utilizar las siguientes sintaxis:
- `$((expresión))`
- `$[expresión]`
- `let var=expresión`

Veamos algunos ejemplos:
```console
$ echo $((7*3/2))
10
$ echo $[3*2+5]
11
$ let n=5+10
$ echo $n
15
```

## Paso de parámetros a un Script

Para pasar parámetros a un script, los añadimos después del nombre del script.

```console
$ ./miscript.sh arg1 arg2 arg3 ... argN
```
Dentro del script haremos referencia a los argumentos a través de las siguientes variables:

|                     |                             | 
|---------------------|-----------------------------|
| `$0`                | El nombre del script        |
| `$1 a $9`           | Argumentos del 1 al 9       |
| `${10}, ${11}`      | Argumentos a partir del 10  |
| `$#`                | Número de argumentos        |
| `$* o $@`           | Todos los argumentos        |

Vamos a verlo con un ejemplo:

```bash
#!/bin/bash
SUMA=$((${1:-0} + ${2:-0} + ${3:-0}))
echo $SUMA
```

Ejecución:

```console
$ bash ./s2.sh 1 2 3
6
```

## Tratamiento de la entrada y la salida
La lectura se puede realizar mediante dos mecanismos:

- A través de la entrada estándar (stdin) con el comando `read`

```bash
#!/bin/bash
echo -n "Escribe el DNI y letra separados por un espacio"
read DNI LETRA
```

!!! note "Nota"
  La opción `-n` evita el salto de línea final


- Con la redirección de entrada de un fichero

```console
read X < fichero
```
Con esto, guardamos en la variable X el contenido de la primera línea del fichero.

## Resultado de la ejecución de una orden o Script
Las órdenes ejecutadas en un shell tienen un código de salida que se guarda en la variable `$?`

- Si el código de salida (`$?`) es 0, indica que la orden se ha ejecutado correctamente.
- Si el código de salida (`$?`) es mayor que 0, indica que la orden no se ha ejecutado correctamente.

Con la orden `exit`, podemos finalizar un script e indicar el código de salida (como argumento
numérico) que deseemos, en función de la ejecución de éste.
Si en un script no utilizamos `exit`, el resultado del script será el de la última orden ejecutada.

Veamos un ejemplo:
```console
$ echo $((3+5))
8
$ echo $?
0
$ echo $((3/0))
bash: 3/0: division by 0 (error token is "0")
$ echo $?
1 
```

# Estructuras de control de flujo
## Estructura condicional


|       if simple     |
|---------------------|
```bash
if  condicion
  then
    cumple_condicion
fi
```

---------------------------------------------------
    Aliniació     Capçalera       Aliniació       Aliniació
    a l'esquerra  centrada        a la dreta      per defecte
------------- ------------ --------------- ---------
    Fila1       Fila 1        Fila 1        Fila 1
    Columna1    Columna2      Columna 3     Columna 3

    Fila2       Fila 2        Fila 2        Fila 2
    Columna1    Columna2      Columna 3     Columna 3
----------------------------------------------------