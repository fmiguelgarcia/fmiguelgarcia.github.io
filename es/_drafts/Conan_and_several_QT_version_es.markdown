---
layout: single
title:  "Conan, manegando diferentes versiones de Qt"
date:   2017-06-16 21:00:00 +0100
lang: es
categories: cpp  
---
A veces me encuentro con proyectos C++ que usan **Qt** como framework principal,
y **Conan** para gestionar sus otras dependencias. Normalmente **Qt** está
instalado en el sistema o el entorno de desarrollo y normalmente las dependecias
también usa **Qt**.

En este entorno, actualizar la versión  del framework **Qt** en nuestra solución, implica que tengamos que
recompilar manualmente todas las dependencias y generar nuevos paquetes.

La idea para solucionar este tedioso trabajo, especialmente cuando trabajas con
diferentes proyectos y que usan diferentes versiones de **Qt**, es que la
versión de **Qt** forme parte de la identificación del paquete al igual que lo
son el SO, la arquitectura o el modo (release, debug, etc.).

Para ello, **Conan** nos proporciona las *opciones*. Paquetes generados con opciones
distintas son considerados como paquetes distintos. De esta forma tenemos el
mismo paquete compilado para distintas versiones de **Qt** (por ejemplo, 4.8,
5.6, 5.9, etc.).

Y para cargar esa opcion automáticamente, usaremos la función *configure()* : 

```python
class QEEntityConan(ConanFile):
    name = "QEEntity"
    version = "1.0.0"
    requires = "QEAnnotation/1.0.0@fmiguelgarcia/stable"
    settings = "os", "compiler", "build_type", "arch"
    license = "https://www.gnu.org/licenses/lgpl-3.0-standalone.html"
    description = "QE Annotated Entity library"
    options = { "qt_version": "ANY"}

    def configure(self):
        self.options.qt_version = os.popen("qmake -query QT_VERSION").read().strip()
        self.output.info("Configure Qt Version: %s" % self.options.qt_version)
```

En el código de ejemplo, definimos la opción *qt_version* que puede contener
cualquier valor. Luego, el método *configure()* se encarga de extraer la versión
de **Qt**, usando el comando **qmake --query** y guarda el valor en dicha opción
de la receta conan.
**marcando así este paquete compilado válido para la versión Qt específica de
compilación**

Y listo, cada vez que qeramos cambiar o probar una versión del framework,
**Conan** usará o recompilará los paquetes que no tengamos ya compilados para
esa versión específica.
