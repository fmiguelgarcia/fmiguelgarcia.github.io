---
layout: single
title:  "Qt Charts and conan package manager"
date:   2016-05-20 16:19:48 +0100
lang: es
categories: cpp  
---
{% include toc %}

Cada nueva release de Qt suele traer nuevas e interesantes features. En el caso de la última 5.6.0, la nueva y flamante licencia de QtCharts era una tentación. Sin embargo, tras descargar el nuevo bundle, descubrí que no estaba incluido en este. Toca bajárlo de git y compilarlo a parte.
Perfecta la ocasión para generar mi primer paquete de Conan.io (www.conan.io). Para los que no lo conocéis aún, **Conan** es un sistema muy prometedor para la **gestión de paquetes de C++ que se integra perectamente con CMake**. Soporta integración con otras herramientas de construcción, pero ¿hay alguna al nivel de <code>CMake</code>? hehe.
Desde el punto de vista arquitectónico, la arquitectura de paquetes suele ser bastante postergada en el tiempo hasta que ya la solución es enorme. Mala praxis de ciertas empresas. Una buena _gestión de la arquitectura de paquetes_, desde el principio del proyecto _nos proporciona_ una serie de ventajas:

 1. **Capacidad para paralelizar el trabajo** en diferentes áreas y por diferentes grupos. Si no dispones de paquetes versionados y correctamente separados, la integración de los desarrollos de los equipos (y más cuando son remotos) puede ser una _sobrecarga_ de esfuerzo que afecta definitivamente al **time to market**. Atención a los _Project Managers_: este diseño arquitectonico estará en consonancia con el **BRS**, así que habrá que realizar unas reuniones iniciales con los arquitectos para ajustarlo.
 2. Aumenta enormemente la **calidad** de la solución, ya que evita caer en el conocido _infierno de versiones_. Analizando periódicamente la evolución de las versiones de los paquetes, podemos determinar _dónde poner más esfuerzo_, realizar _integraciones o refactorizar componentes_ con el **objetivo que reducir los sobre costes**, en especial aquellos relacionados con la **deuda tecnológica** (en la próxima entrada hablaremos de eso).

Los chicos de <code>Java</code> lo tienen bastante arreglado con <code>Maven</code> o <code>Gradel</code>, pero si tu projecto es en C/C++ mi recomendación es el tandem <code>CMake</code> y <code>conio</code>. Más aún cuando la solución sea multiplataforma.

# Comenzando con conan #

Pero vamos al tajo, cómo generar un paquete conan. Básicamente tenemos que crear un archivo conocido como _"receta"_ del paquete: <code>conanfile.py</code> (sí python, imagina la flexibilidad que proporciona). Este archivo contiene una clase Python donde le indicamos al sistema cómo obtener el código fuente (para projectos alojados en repositorios de versionado, a.k.a git ... cualquier otro es como una rueda de palos en comparación), cuales son sus dependencias, cómo construirlo y qué debe contener el paquete.

La gran ventaja de conan sobre maven es que tiene en cuenta factores muy importantes para las soluciones nativas, como por ejemplo: el compilador, la arquitectura, el sistema operativo. Además si no existe el paquete binario para tu configuración, el sistema intentará construirlo. Por ejemplo, supongamos que todo nuestro sistema se base en <code>gcc 4.9</code> y queremos probar qué tal va nuestra solución sobre <code>clang 5.0</code>. Nos bastará con compilar el paquete correspondiente a la aplicación y automáticamente todas las dependencias de estas serán recompiladas y almacenadas en tu repositorio local usando la nueva configuración.

El archivo de receta, <code>conanfile.py</code> lo podéis encontra en el siguiente repositorio git ....TODO

# Preparando el entorno #
Si pusiera Qt 5.6.0 como dependencia del paquete, tendría que generar todos y cada uno de los paquetes conan para Qt 5.6.0 . Eso por ahora, lo dejaremos a parte y estableceremos como precondición que tu sistema lo tenga instalado. Realmente, basta con actualizar el path para que encuentre el <code>qmake</code> correcto. En mi caso, que uso <code>fish</code> en lugar de <code>bash</code>:

``` 
set -xg PATH ~/bin/Qt/5.6/gcc_64/bin/ $PATH
```
y para comprobar que la versión de <code>qmake</code> es la correcta:

```
$> qmake --version
QMake version 3.0
Using Qt version 5.6.0 in /home/miguel/bin/Qt/5.6/gcc_64/lib
```

# Exportar, construir y disfrutar #

Lo primero que necesitamos es exportar el paquete. Esto es copiar la receta a nuestro repositorio local. De esta forma, lo tendremos accesible como dependencia en otros futuros paquetes.

```
$> conan export miguel/stable

QtCharts/5.7.0@miguel/stable: A new conanfile.py version was exported
QtCharts/5.7.0@miguel/stable: conanfile.py exported to local storage
QtCharts/5.7.0@miguel/stable: Folder: /home/miguel/.conan/data/QtCharts/5.7.0/miguel/stable/export
```

Las recetas de conan se exportan con cierta información posicional, en este caso, <code>miguel/stable</code> identifica que la receta es del usuario <code>miguel</code> y sobre el canal <code>stable</code>

Con esto sería suficiente, ya que las dependencias se pueden construir si no se dispone del paquete compilado para tu configuración específica. Pero como ahora estamos en el *rol* de forjadores de paquetes necesitaremos probar que la construcción y la generación del paquete es correcta. Para ello, la propuesta de **conio.io** es generar un paquete especial de test. Éste tiene como dependencia nuestro paquete. Además tenemos que añadir un código que haga uso de la librería para generar un ejecutable de prueba. Puedes ver este paquete de prueba en el subdirectorio <code>test_package</code>. 
Cuando se lanza la prueba, se generará y guardará el nuevo paquete en nuesto repositorio local:

```
$> set -xg CONAN_USERNAME "miguel"
$> set -xg CONAN_CHANNEL "stable"
$> conan test_package
```
Hay dos puntos importantes:

 1. Esta receta, se trae el código fuente de _QtCharts_ desde _github_. 
 2. La dependencia con nuestro paquete, su información posicional (usuario/canal) se establece usando las variables de sistema <code>CONAN_USERNAME</code> y <code>CONAN_CHANNEL</code>. De esta forma, es más flexible y un mismo paquete de test puede ser usado por distintos usuarios y/o canales.

Tras la descarga del código fuente y la compilación de la librería QtCharts, se compilará y ejecutará la aplicación de test. Finalmente aparecerá en pantalla un bonito ejemplo de aplicación usando QtCharts.

Para usar la librería en tus projectos, será suficiente con añadir el paquete a las dependencias de tu projecto. En este caso (puedes ver el ejemplo completo en <code>test_package/conanfile.ph</code>:

```python
    requires = "QtCharts/5.7.0@miguel/stable" 
```

y añadir la información para tu herramienta de build, en mi caso <code>CMake</code> (a caso hay otra mejor?):

```cmake
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()
```
El ejemplo completo en <code>test_package/CMakeLists.txt</code>

# Conclusion #
Es importante disponer de un sistema de gestión de paquetes apropiado cuando te enfrentas a un desarrollo donde hay varios productos o equipos trabajando en parallelo. La compartición de funcionalidad y la gestión correcta de las dependencias son factores fundamentales. He visto empresas donde no se compartían librerías porque cada producto llevaba su velocidad y al final, se hacía lo mismo dos o tres veces. 
El versionado de librerías compartidas también tiene su lado oscuro. Si la separación de paquetes no es correcta, acabarás sufriendo ante cada mínimo cambio ya que requerirá que se actualicen innumerables versiones de distintos paquetes: **infierno de dependenicas**

El versionado es una herramienta que mejora la productividad y la calidad, tanto del proceso como del software. Y para sacar lo máximo es importante disponer de buenas herramientas para llevarlo a cabo, como es el caso de <code>conan</code> y de <code>CMake</code>, y por supuesto, de excelentes librerías externas como <code>Qt</code> y su magnífico IDE (aunque personalmente aún prefiero **KDevelop** y mi viejo **Vim**).


