---
layout: single
title:  "Qt Charts and conan package manager"
date:   2016-05-20 16:19:48 +0100
lang: es
categories: cpp  
---
{% include toc %}

Each new Qt release usually comes with new and interested fueatures. In the last 5.6.0, the bright new <code>QtCharts</code>'s licence was a tentation to me. However, after download this bundle, I discovered that <code>QtCharts</code> was not included. You need to download it from <code>git</code> and compile it yourself.
This is a perfect opportunity to generate my first <code>conan</code> package. If you don't know yet <code>conan</code>, please visit www.conan.org. It is #prometedor# new system to facilitate the **package management for C++ and a perfect CMake integration**. It also supports other building tool's integration, but Is there anyone better than <code>CMake</code>? Well, this is my humble opinion, hehe.
From an architectonic point of view, the package architecture usually is postergated until the software solution is huge. It is a common and bad _praxis_ on lot of companies. A good _package architecture_, at the beginning of the project, will allow us to reach a set of advantages: 

 1. **Work parallization capacity** in different solution's areas and executed by several work groups. If we don't have versioned and well-splitted packages, the integration of developments which has been executed by different teams (specially when teams are remoted) could be a *overload* that affects directly to our **time to market**. The *project managers* and *software architect* have to work together to link the **BRS** with the **package management** because this package architecture could facilitate the archive of the deliverables and be able to use *fast tracking*.
 2. It **increase the solution quality** because it avoids to fall out into *the versions' hell*. Analyzing periodically the package's evolution, we are able to determine where we need to increase the effort, make *integrations* or *refactoring* components. The main aim is to **reduce the over-cost**, specially the cases due to **tecnology debit/mortage** (we will see that on a future post).


The <code>Java</code> modern language guys have a good solution for that using <code>Maven</code> or <code>Gradel</code>. However on a <code>C</code> or <code>C++</code> project, my recommendation is to use the tandem <code>CMake</code> and <code>conan</code>. Both tools are specially useful when your solution is multiplaform. I mean not only different SO (like Windows, Mac or Linux), also when you work using several compilers (clang, gcc, MSVC, etc) or different version of them.

# Starting with conan#

Let's do the first step, How can I generate a <code>conan</code> package? Briefly, we have to create a new file, the package *recipe*: <code>conanfile.py</code>. Yes, it is python, so Can you imagine the flexibility to create custom steps?. There is another option, based on a simpler text file, but <code>python</code> is enough simple. Well let's come back to our recipe file. This file contains a <code>python</code> class where we can setup things like how the system will get the source code, whose are its dependencies, how to build it or what should be in the binary package.

One of the biggest advantages againts <code>Maven</code> is that conan manages very well some aspects needed by native applications, like: compiler and its version, system architecture (x86, x64, etc), operative system, etc. Moreover, it will try to build the binary package whether it does not still exist on your local repository or for your specific configuration. In example, let's assume that our system is based on <code>gcc 4.9</code> and we want to test our library on <code>Microsoft VS 2015</code>. We only need to setup our default configuration or adjust some command line parameters and each dependency will be compiled, linked, and stored in our local repository using the new compiler.

The <code>conan</code> recipe for this post is located in ... TODO

# Environment setup#


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


