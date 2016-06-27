---
layout: single
title:  "Technical debt and VAN"
date:   2016-06-19 16:19:48 +0100
lang: es
categories: pmp 
---
Las últimas estadísticas nos dicen que la vida média de un producto software es de 10 años. Acaso el software se desgasta como el motor de un viejo coche y llegado el momento, no se puede reparar más y es mejor comprar uno nuevo. 
Bueno, hay muchos factores, pero en esta entrada me gustaría fijarme en la **Deuda técnica**, y como gestionarla desde el punto de vista del PMP.

{% include toc %}

Pongamos un ejemplo para empezar. Supongamos que eres el PMP de un proyecto que durará más de 10 años. Evidentemente, necesitarás una inversión inicial y algo de financiación a lo largo del tiempo. Evidentemente, los inversores se preguntarán si el proyecto es una buena inversion, cuánto será el retorno y cuándo será ese retorno. Si los resultados económicos empezarán a verse el primer, segundo, o quinto año. En una palabra, el VAT.

El el mundo del software, estimamos es coste de cada nueva feature y sabemos cuánto nos va a costar. Pero, realmente implementamos lo que estimamos? No, normalmente surgen problemas, se eleva la complejidad o simplemente los requisitos no se corresponden con las necesidades del cliente.

Volviendo a nuestro ejemplo financiero, cada cambio que no está planificado en el proyecto es lo mismo que solicitar financiación. Es decir, no disponemos de suficiente líquido en un momento determinado y tiramos de los servicios financieros externos. Pero esto no es gratis. A ningún administrador se le ocurriría solicitar un financiación sin tener en cuenta el coste extra que supondrán los intereses. Lo tildarían de incompetente como mímino. Pues bien, esto sucede a diario en la mundo del desarrollo de software.
 
# Pero qué es la deuda técnica #

**Buscar info de sobre deuda técnica para usar como referencia**.

Básicamente, la deuda técnica es todo aquello que nos permite flexibilizar el desarrollo frente a cambios en el proyecto, pero que deberemos arreglar en el futuro. Esto ocurre, por ejemplo, cuando se descubren nuevos requisitos, que normalmente, están fuera del diseño arquitectónico del producto, o cuando nos vemos obligados a usar un *workaround* para solventar algo rápido.

En el caso de nuevos requisitos, es como incrementar el crédito, por lo que los intereses finales serán mayores. Normalmente, esos nuevos requisitos se intentan meter dentro de la linea base de tiempo. Esto afectará a la línea base del coste o a la calidad. Puesto que normalmente, se evitan los sobre-costes, se intenta forzar a rebajar la calidad (por ejemplo, reduciendo la cobertura de pruebas, o aumentando la complejidad ciclomática del código, etc). Bien, esa rebaja de la calidad es una **deuda técnica**. Si no *pagamos* esa deuda, afectará al producto. Por ejemplo, si decidimos reducir el número de test, incrementamos el riesgo de que se produzcan errores, especialmente costosos, si se descubren después de entregar el producto al cliente.

Si *parcheamos* o realizamos un *workaround* para solucionar algo de manera rápida, y evitar así retrasar la entrega de la solución, estamos también incrementando nuestra deuda técnica. Es decir, reducimos el **time to market**, pero dejamos pendiente la corrección futura. Si ese área está bien aislada y no se verá involucrada en futuros desarrollos, puede que nunca nos vuelva a afectar. Sin embargo, si se vuelve a ver involucrado en nuevos cambios, habrá que pagar esa  **deuda técnica**. Esto implica que el desarrollo se verá incrementado en tiempo/coste, ya que habrá que resolver los problemas previos antes de añadir nuevas características.

Muchas veces, puede ser necesario agrupar las **deudas técnicas** y resolverlas juntas: esto se conoce en el mundo del software como **refactoring**.  

# Refactoring: pagando lo que se debe #

El refactoring lo podemos clasificar como:
	- Refactoring pro-activo u opcional. Este es el mejor porque es opcional y nos permite planificar si abordamos el refactoring (pagar la deuda) o seguimos endeudándonos para conseguir un bien mayor (generalmente cumplir en plazo, o por falta de recursos). La otra gran ventaja es que nos da una idea del estado del equipo. Si en el proyecto tenemos bastantes ordenes de cambio que provienen del equipo y que son refactorings proactivos, significa que el engagement del equipo es muy elevado. El caso contrario, mi recomendación es invertir en incrementar ese engagement. No hay nada peor que un equipo desmotivado en el proyecto.
	- Refactoring obligatorio. No nos queda más remedio que pagar esta deuda. En el ejemplo financiero, es como si hubieramos llegado a un procedimiento penal y nos han condenao a pagar. En este caso, perdemos la flexibilidad. No hay otra salida que ejecutar el refactoring si queremos disponer de la nueva funcionalidad.

# Control de la deuda técnica #

De igual manera que todo administrador o PMP mantiene un control de la financiación económica, nosotros también tendremos que mantener un control sobre nuestra deuda técnica.Para ello, la propuesta es localizar cada nueva deuda técnica como un nuevo riesgo, asociado a un área o módulo. Al igual que cualquier otro riesgo, tendremos que añadir tanto al probabilidad como el impacto. Además, el coste debe ser proporcionado como una estimación proporcionada por el equipo técnico.

Es exactamente igual que calcular el VAT, pero en lugar usar el tiempo para ver ese retorno, usaremos el **indice de conexión** entre los diferentes módulos. No es lo mismo, un deuda que afecta a 5 componente principales, que una que sólo afecta a un componente que sólo usa un cliente específico.

# Plantilla de riesgo por deuda técnica #

# Cálculo del valor de deuda técnica #

# Que no hacer #

# hyppes y su muerte anunciada #

En este negocio, cada cierto tiempo, surgen nuevos hyppes a los que todo el mundo parece venir bien. Esas nuevas tendencias suelen presentarse como una *bala de plata*: resuleven todos los problemas, son muy fáciles de usar y todo el mundo está feliz con los resultados. Algunos de estos últimos hyppes son el **Agile** (ver **el agile está muerto**) o el omnipresente **BigData** (ver porcentaje de éxito de los projectos de bigdata).

Gran parte del éxito de éstos se debe a que los proyectos que los usan no sufren apenas deuda técnica. Sin embargo, a lo largo del tiempo y la evolución de los proyectos, el peso de la deuda se va incrementando. Existen incluso casos donde se vuelve imposible la continuidad del desarrollo.
 
# Conclusion #

He tenido la suerte de trabajar en bastantes projectos con un largo bajage, y por supuesto con una gran deuda técnica heredada. El gran problema es que esa deuda no se hace visible a la capa de gestión hasta que no llega a niveles muy muy alarmantes. Es imposible pretender tener equipos de alto rendimiento cuando estos equipos gastan más del 40% de su tiempo de desarrollo en lidiar con esa deuda. Esto acaba con la desmotivación de los equipos, el incremento del **time to market**, o incluso la perdida de competencia y de negocio.

Es comprensible pensar que en un proyecto que lleva años de desarrollo y evolución, el coste de evaluar esa deuda técnica, puede ser notable. Pero veámoslo desde el punto de vista financiero, ¿alguien se atrevería a comprar una casa y empezar a hacer reformas sin comprobar previamente si tiene cargas previas? Muchas veces, portar la idea del mundo abstracto del software a un ejemplo más habitual, puede ayudar a hacer comprender a los stackeholders de la necesidad de este proceso.

Por supuesto, si el proceso se puede realizar de manera progresiva, el coste de este es más distribuido entre diferentes features y además suele ser más preciso porque los detalles técnicos están en ese momento sobre la mesa.
