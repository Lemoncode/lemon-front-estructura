# Lemoncode - Estructura Front

<img src="../content/logo.png" width="120px">

versión: 0.1

Fecha de publicación: 27 de Octubre de 2022

<div style="page-break-before:always"></div>

# Sumario

Tópicos de esta guía:

- Nombrando y creando carpetas.
- Estructura de carpetas.
- Importación de rutas relativas y aliases.
- Nombrando y creando ficheros.
- Distribución de contenido en ficheros.
- Estructura de un _pod_.
- Nombrando eventos y propiedades _callbacks_.
- Principio de promoción.
- Política de pruebas unitarias.
- Herramientas

# Nombrando y creando carpetas

Prácticas que tenemos definidas

- Usar minúsculas.
- Separar palabras con guiones medios.
- Usar nombres cortos pero descriptivos.
- Salvo que lo tengamos claro no crear carpetas demasiado temprano.
- Uso de singulares y plurales.
- Uso de _barrel_.

## Uso de minúsculas

Esto viene porque _Linux_ es _case sensitive_ y _Windows_ no, aunque
ya los IDE y herramientas de desarrollo lo suelen resaltar es
posible que se nos pase este detalle y por ejemplo tener
que el _build_ local en máquinas Windows funciona, pero en CI en una
máquina Linux, y este fallo es muy complicado de depurar ya que
si nadie tiene una máquina Linux / Mac OS en local.

Podéis ver en muchos proyectos _open source_ que no siguen esta aproximación,
nosotros lo entendemos así porque en el resto de Europa y en USA la mayoría
de _Front Enders_ desarrollan con Mac OS.

## Separación de palabras

Nosotros solemos usar guiones medios (_kebak case_) porque nos resulta más legible.

Podría valer otro separador, pero es importante que sea consistente, si por ejemplo
se usa guion bajo, que se use siempre guion bajo, no mezclar.

En el caso de los ficheros veremos que usamos dos separadores, el "-" y el ".", esto
lo veremos más adelante, así como el razonamiento.

<div style="page-break-before:always"></div>

## Nombres cortos pero descriptivos

En el caso de carpetas intentamos que los nombres sean cortos, ya que normalmente
acompañarán a ficheros que cuelguen de la raíz, pero le damos importancia a que estos
describan que es lo que hacen.

En caso de que hagan falta más de una palabra, usamos guiones medios para separarlas
(mismo razonamiento que para nombre de ficheros).

## Salvo que lo tengamos claro no crear carpetas demasiado temprano

Salvo que este muy claro que es necesario crear una carpeta o estructura de carpetas
(por que tengamos un armazón inicial definido, o tengamos claro que van a ser necesarias),
preferimos crear la carpeta sólo cuando se necesario.

Por ejemplo:

- Tenemos una carpeta `*components*`, de primeras vamos soltando componentes aquí, conforme esta carpeta crece en contenido va haciendo más grande va a hacer falta re factorizarla y agrupar componentes comunes en subcarpetas.

- Tenemos debajo de _components_, un componente de _layout_, otro de que _wrapea_ un _input_, un _combobox_, una lista... esos componentes serán candidatos de agruparlos en la carpeta `components/form`, sin embargo la de layout mientras no crezca no lo planteamos.

Siguiendo esta aproximación, nos evitamos tener una estructura de carpetas de las que hay veces que sólo cuelga un fichero, este mal se llama "el principio de clarividencia del programador", a priori en muchos escenarios no sabemos por dónde va a crecer el proyecto.

Es malo tanto tener muchas carpetas y profundidad sin contenido, como tener poca profundidad de carpetas y muchos ficheros.

## Uso de singulares y plurales

A nivel de carpetas, salvo que estemos hablando de adjetivos o términos no contables (_common_, _core_), utilizaremos el plural para las carpetas, ya que suelen agrupar elementos (_components_, _scenes_, _layouts_).

Siguiendo esta aproximación es más natural identificar carpetas, en los ficheros evitaremos el uso de plurales.

## Uso de ficheros barrel

Es buena práctica debajo de cada subcarpeta generar un fichero _index.ts_ que sea el punto de entrada para los ficheros de esa carpeta, algo así
como:

_./index.ts_

```ts
export * from "./kanban.container";
export * from "./providers/kanban.provider";
```

<div style="page-break-before:always"></div>

De esta manera:

- Las importaciones son más cortas.
- Si se renombra un fichero, no hay que cambiar las importaciones, sólo tocar el _barrel_.
- Si se renombra una carpeta, sólo hay que cambiar el _barrel_.
- Si se reagrupa en subcarpetas, sólo hay que cambiar el _barrel_.
- Tenemos una forma de indicar al programador que va a consumir elementos de nuestra carpeta que funcionalidad queremos publicar como "_pública_", si bien el desarrollador puede ir a por la ruta completa, va a detectar que el desarrollador original no quería exponer el fichero por algún motivo.

# Estructura de carpetas.

Cuando creamos una aplicación de tamaño medio, seguimos la aproximación de _pods_.

```txt
my-application/
├─ common/
├─ common-app/
├─ core/
├─ layout/
├─ pods/
├─ scenes/
```

Para proyectos más grandes, candidatos dividir en cargas bajo demanda o submódulos podemos añadir un nivel de carpetas más _submodules_ aquí se puede elegir dos aproximaciones:

```txt
my-application/
├─ common/
├─ core/
├─ module/
│  ├─ my-module/
│  │  ├─ common-app/
│  │  ├─ layout/
│  │  ├─ pods/
│  │  ├─ scenes/
```

Aquí depende como se enfoque cada submódulo podrían compartir _concerns_ comunes.

<div style="page-break-before:always"></div>

Otra opción es la siguiente:

```txt

my-application/
├─ module/
│  ├─ my-module/
│  │  ├─ common/
│  │  ├─ common-app/
│  │  ├─ core/
│  │  ├─ layout/
│  │  ├─ pods/
│  │  ├─ scenes/
```

En este caso cada módulo es una isla independiente, si necesitamos compartir _concerns_ comunes podríamos plantear crear una librería o una carpeta común o _core_ a módulos.

## common

Bajo esta carpeta se incluyen todos los componentes y funcionalidades que se pueden usar en cualquier parte de la aplicación, y que podrían ser reutilizables en otras aplicaciones (no tienen relación con el dominio de la aplicación), por ejemplo:

- Un componente de UI para mostrar un calendario.
- Una función para parsear un fichero CSV cualquier.
- Unas expresiones regulares para validar un email o un formato dado.
- ...

La funcionalidad que se publique aquí, si se demuestra útil (ver más abajo principio de promoción), se puede extraer a una librería estándar y que otros proyectos de la empresa lo usen o incluso promocionarlo a _open source_.

## common-app

Bajo esta carpeta se incluyen todos los componentes y funcionalidades que se pueden usar en cualquier parte de la aplicación, pero que NO son reutilizables en otras aplicaciones (SI tienen relación con el dominio de la aplicación), por ejemplo:

- Un panel de filtro de paciente de un hospital que usan campos específicos de la base de datos.
- Un listado de pacientes con una jerarquía de datos específica del dominio.
- ...

Es decir son componentes / funcionalidades reusable que no se pueden reaprovechar en otras aplicaciones ya que están atadas al dominio del proyecto.

Esta carpeta es opcional y puede generar controversia ya que se puede incurrir en el riesgo de que la carpeta acabe siendo un cajón de sastre, si no hay criterio de que debe incluir.

<div style="page-break-before:always"></div>

## core

En esta carpeta incluimos la funcionalidad que es transversal al proyecto, es decir ficheros que podemos usar a diferentes niveles de la aplicación y que guardan información, por ejemplo:

- Las rutas de navegación.
- El perfil del usuario / roles de seguridad.
- El tema de la aplicación.
- Contextos y proveedores de la aplicación.
- Cachés de datos comunes.
- ...

A veces nos puedes costar distinguir que debe entrar en la carpeta _common_ y la _core_ como regla para distinguirlo:

- La carpeta _common_ está más orientadas a componentes y funcionalidades independientes que podemos incrustar en la aplicación, estas funcionalidades actúan como cajas negras independientes.
- La carpeta _core_ almacena funcionalidad que se usa y está cohesionada a nivel de aplicación, por ejemplo información de la aplicación que necesitamos consumir a diferentes niveles de la aplicación.

## layout

En _layout_ definimos las páginas maestras, es decir el armazón de una página (_wireframe_), por ejemplo una ventana de aplicación puede tener la siguiente estructura:

```txt
---------------------------
|       Header            |
---------------------------
|                         |
|        body             |
|                         |
|                         |
---------------------------
|       Footer            |
|                         |
|                         |
---------------------------
```

Otro _layout_ puede ser el de la ventana de _login_ en el que simplemente centramos el contenido en horizontal y vertical.

Cada página de la aplicación (escena) elegirá que _layout_ puede usar y en el área de _body_ aparecerá el contenido de la escena.

Esto se puede elaborar más:

- Un _layout_ podría tener más de una zona para poder incluir contenido de página.
- Un _layout_ podría tener _sublayouts_.

Aconsejamos no entrar en este nivel de complejidad salvo que sea estrictamente necesario.

También la elección de _layout_ se puede realizar a nivel de página (escena), o se puede intentar hacer una agrupación de páginas por _layout_ a nivel de _router_, pero esto depende mucho del _router_ que estemos usando y la versión del mismo (por ejemplo React Router en unas versiones lo permite en otras no).

## pods

En un _pod_ encapsulamos una funcionalidad rica e independiente, lo asemejamos a una isla de código, es estanca y sólo se comunica con el exterior mediante funcionalidad transversal que podemos encontrar en _core_ o usando componentes comunes (_common_, _common-app_).

Un ejemplo de capetas de pod:

```tsx
my-app/
├─ pods/
│  ├─ patient/
│  │  ├─ components/
│  │  ├─ patient.api.ts
│  │  ├─ patient.mapper.ts
│  │  ├─ patient.business.ts
│  │  ├─ patient.container.tsx
│  │  ├─ patient.component.tsx
│  │  ├─ patient.vm.ts
│  │  ├─ index.ts
```

El concepto de _pod_ lo cubriremos con más extensión cuando cubramos la sección _estructura de un pod_.

### ¿Por qué no encapsulamos esto en páginas / escena?

El objetivo de una escena (página) es:

- Elegir que _layout_ va a usar.
- Manejar posibles parámetros a nivel de _query string_ que puedan llegar.
- Elegir que _pod_ o _pods_ va visualizar.

Queda fuera del alcance mezclar otros detalles de implementación.

### ¿Por qué una isla de código?

El objetivo del pod es que sea lo más independiente posible, de esta manera:

- Cuando llega un desarrollador se puede centrar en un _pod_ concreto y conocer la funcionalidad común y _cross_ que le afecte, no tiene que conocer el proyecto completo.

- Es más fácil de mantener ya que vamos tocando por islas estancas y un cambio en un _pod_ no tiene por qué afectar a otro.

- Es más fácil orientarlo al lenguaje del dominio para ese _pod_, creando _ViewModels_ específicos.

- Todo lo que impacta al _pod_ se encuentra cerca (mismo nivel de carpeta o inferiores) y el desarrollador no tiene que ir navegando por el proyecto para encontrarlo.

## scenes

Una forma de definir las páginas de la aplicación es usando la nomenclatura de escena, es decir, cada página de la aplicación es una escena, ¿Por qué este término? Bueno, es como si a fin de cuentas sólo usáramos este elemento para hacer una composición de _pods_ y _layouts_, intenta romper con el término de _página_ que solemos asociar al concepto de _página web_ en la que metíamos un mazacote de funcionalidad.

Si no estás cómodo con el termino, siempre puedes usar "_pages_" o "_views_", lo importante es ser consistente y que todo el equipo esté de acuerdo con el nombre que se usa.

## Un ejemplo con más niveles

Conforme el proyecto crece y se va haciendo más complejo, se va haciendo necesario añadir más niveles de carpetas, salvo que tengamos claro desde un principio la dirección en la que va a crecer el mismo, es mejor ir añadiendo niveles de carpetas conforme se vaya necesitando.

Un ejemplo de cómo puede quedar _common_:

```txt
common/
├─ components/
│  ├─ forms/
├─ hooks/
├─ validations/
```

Sobre este ejemplo:

- Conforme el proyecto crece nos solemos encontrar que creamos varios componentes comunes reusables, si esto pasa es buena idea crear una carpeta de _components_ para tenerlo localizado.
- Es muy normal que en un proyecto se acaba con una capa que haga de _wrapper_ de los componentes comunes que se usen y que incluyan por ejemplo la fontanería para trabajar con un motor de gestión de formularios o un tematizado dado, de ahí que se cree la subcarpeta _forms_ dentro de
  _components_, esto sería candidato a promocionar a librería.
- Lo mismo pasa con los _hooks_ y las validaciones, temas genéricos merece la pena tenerlos localizados (por _ejemplo_ una validación de NIF, o de código HL7..., o un _hook_ que realiza algo genérico como por ejemplo un hook que nos permite detectar si hemos hecho clic fuera de un elemento.

Esta ruta puede que no encaja en tu proyecto, también te puedes plantear organizarlo por característica funcional, todo depende de cómo crezca el proyecto.

Un ejemplo para la carpeta _core_:

```txt
core/
├─ api-configuration/
├─ auth/
├─ constants/
├─ router/
├─ theme/
```

En este caso organizamos temas transversales que se pueden usar en toda la aplicación:

- Configuración para nuestra _api rest_.
- Autenticación (preguntar por usuario, roles...).
- Constantes a nivel global.
- Configuración de rutas de navegación de páginas.
- El tema de la aplicación (colores, fuentes, tamaños...).

Igual que en para la carpeta _common_ este nivel de subcarpeta se puede ajustar a tus necesidades o no, algo importante a evitar es terminar con un montón de niveles de carpetas que tengan uno o dos ficheros a lo sumo.

Un ejemplo de la carpeta _pod_ (en este caso con carpetas y ficheros):

Aquí lo importante es tener claro cuál es el punto de entrada e identificar los ficheros y carpeta por su tipo, de cara a saber dónde tocar.

```txt
pod/
├─ patient/
│  ├─ api/
│  ├─ components/
│  ├─ patient.business.ts
│  ├─ patient.component.tsx
│  ├─ patient.container.tsx
│  ├─ patient.hooks.ts
│  ├─ patient.mapper.ts
│  ├─ patient.vm.ts
│  ├─ index.ts
```

En este caso podemos plantear en _api_ crear una carpeta si vamos a tener varios ficheros que monten la api para este _pod_.

En _components_ tenemos un caso parecido, si con el _root container_ y _component_ no tenemos suficiente, es buena idea crear
una carpeta _components_ para agrupar los subcomponentes que se usen en el pod (y si hubieran muchos agruparlos a su vez por funcionalidad).

A nivel de ficheros, dependiendo de la complejidad del pod, vamos creando ficheros o rompiendo en carpetas (sobre los ficheros lo cubriremos más adelante), no debemos crear ficheros porque si, sino porque realmente necesitamos agrupar funcionalidad.

El objetivo aquí es que cuando abramos el pod podamos ir rápidamente al punto de entrada e identificar donde tenemos que acudir para tocar algo.

Normalmente un pod no debería de crecer de forma desmesurada, en ese caso tendríamos que pensar si romper en más de un _pod_.

<div style="page-break-before:always"></div>

## Importación de rutas relativas y aliases

Un tema importante a la hora de importar módulos es tener en cuenta las rutas relativas y los aliases.

Por ejemplo en un _pod_ complejo nos podríamos encontrar con algo como:

```tsx
import { Calendar } from "../../../../common/components/calendar.component";
```

Esto presenta varios problemas:

- Legibilidad: es difícil de leer y entender.
- Mantenibilidad: si cambiamos la estructura de carpetas, tenemos que ir a todos los ficheros que importan este componente y cambiar la ruta (con suerte _VSCode_ nos puede ayudar a hacerlo, pero no siempre es así).
- Productividad: si las herramientas de _VS Code_ no me sacan el importa de manera automática, tengo que ir probando subiendo carpeta por carpeta.

Para evitar esto, aplicamos varias soluciones:

- Por un lado con el uso de _PODs_ los _path_ relativos (código específico del _POD_) se quedan controlado dentro de la isla de _PODS_.
- Por otro lado para acceso a carpetas globales (_common_, _core_,...) usamos aliases que nos permitan importar esos módulos sin usar rutas relativas largas y completas.

Es decir se nos puede quedar con un alias de este tipo:

```tsx
import { Calendar } from "@/common/components/calendar.component";
```

ó

```tsx
import { Calendar } from "common/components/calendar.component";
```

Si trabajas con _webpack_ y _typescript_ una forma cómoda de configurar esto es:

- Le indicas en _Typescript_ que el prefijo _@_ es un alias a la carpeta _src_, de esta manera creas un alias por cada carpeta que cuelgue justo de src (_common_, _core_, _scenes_, _pods_, _layout_...).

<div style="page-break-before:always"></div>

_tsconfig.json_

```diff
    "esModuleInterop": true,
+    "baseUrl": "src",
+    "paths": {
+      "@/*": ["*"]
+    }
  },
```

Y para no repetir configuracíon y posibles errores en _webpack_, nos podemos bajar el plugin _tsconfig-paths-webpack-plugin_ que nos permite usar los mismos aliases que tenemos en typescript.

```bash
npm install tsconfig-paths-webpack-plugin --save-dev
```

Y consumirlo en _webpack_:

```diff
const HtmlWebpackPlugin = require("html-webpack-plugin");
+ const TsconfigPathsPlugin = require('tsconfig-paths-webpack-plugin');
const path = require("path");
const basePath = __dirname;

module.exports = {
  context: path.join(basePath, "src"),
  resolve: {
    extensions: [".js", ".ts", ".tsx"],
+   plugins: [new TsconfigPathsPlugin()]
  },
```

De esta manera los _imports_ a carpetas raíz se nos quedan de esta manera:

```tsx
import { LoginPage } from "@/scenes/login";
```

# Nombrando y creando ficheros

Otro tema importante es que convenciones seguimos para nombrar ficheros.

En cuanto a _casing_ nos quedamos con siempre usar _lowercase_ (minúsculas) para nombrar los ficheros, la razón es la misma que con las carpetas, en Windows los nombres de los ficheros no son _case sensitive_, en _linux_ sí, si tu equipo de desarrollo es Windows y tu entorno de CI/CD o producción es _linux_ te puedes encontrar con problemas difíciles de detectar ya que en tu local la aplicación va a funcionar.

<div style="page-break-before:always"></div>

¿Y si un fichero tiene varias palabras? Aquí cubrimos dos casos:

- Lo que define al fichero a nivel de dominio / funcionalidad, lo separamos con guiones "-" (_kebap-case_).
- Lo que define técnicamente al fichero, lo separamos con puntos "." (_dot-case_).

¿Y está complejidad por qué?

- Por un lado podemos identificar y leer fácilmente lo que define la parte funcional del fichero.
- Por otro pasa lo mismo con la técnica, siempre nos la encontramos en el final del fichero, y además un fichero que tenga
  varias partes relacionadas va a salir junto (ordenado por nombre).

Por ejemplo:

```tsx
my - calendar.component.tsx;
my - calendar.business.tsx;
my - calendar.hooks.tsx;
my - calendar.styles.css;
```

Sobre los sufijos a usar en la parte técnica del fichero, aquí depende de los que el equipo vea oportuno, los que solemos usar:

- **.container.tsx**: para componentes contenedores, es decir que tienen lógica y presentación.

- **.component.tsx**: para componentes tontos, es decir que solo tienen presentación (o al menos no el peso fuerte de lógica)

- **.business.ts**: para fichero de con funciones puras que resuelvan problemas (también se pueden plantear con estado, pero todo lo que no tenga que ver con React, código plano JS)
- **.hook.ts**: para almacenar _hooks_ (funciones que usan _hooks_ de React).

- **.api.ts**: aquí podemos almacenar código para gestionar llamadas a _API's rest_.

- **.mapper.ts**: para convertir de modelo de API a _ViewModel_.

- **.model.ts**: para definir modelos de datos (de API o global), depende del equipo se podría pensar en romper en más niveles de modelo (por ejemplo _api-model_, _domain-model_... un consejo aquí es no meter más complejidad si no es necesario).

- **.validation.ts**: cuando extraemos la lógica de validación de un formulario a un fichero aparte, esto también es muy útil ya que es fácil de probar y lo tenemos localizado y no esparcido por el árbol de componentes de UI.

<div style="page-break-before:always"></div>

- **.vm.ts**: Aquí definimos el modelo de la vista, habrá ocasiones en que ese módulo sea un mapeo de uno a uno con lo que nos traemos con la API (sobre todo si los que desarrollan la API son los mismos que desarrollan la aplicación), pero en otros casos puede ser que tengamos que mapear valores y realizar transformaciones para que se ajusten a la vista (esa complejidad la sacamos del UI y la pasamos a una función pura JS, fácil de
  probar).

Un tema importante a definir es si debemos usar sólo singulares para definir los ficheros o admitimos plurales (en las carpetas lo hacíamos así), en los ficheros, el consejo es que normalmente no (aunque esto depende de lo que decida el equipo), una fuente de fallo común es tener los siguientes ficheros:

```txt
client.component.tsx
clients.component.tsx
```

Es muy fácil qué a la hora de importarlo o usar el componente / clase / función, se nos baile una _s_ y nos quedemos tontos pensando porque no funciona ese código (los fallos tontos son los que más duelen), en vez de esto se pueden usar las siguientes alternativas:

```txt
client.component.tsx
client-collection.component.tsx
```

ó

```txt
client.component.tsx
client-list.component.tsx
```

También podemos comentar que hacer por contenido / tamaño del fichero:

- Si un componente / función, está muy cohesionada a por ejemplo un componente de un fichero, y el mismo tienen un tamaño pequeño, podemos plantear dejar la funcionalidad en el mismo fichero, y más tarde extraerla si vemos que el fichero crece mucho o se puede reutilizar (aquí lo mismo comentarlo con el equipo, hay desarrolladores que prefieren tener un sólo fichero para cada cosa).
- Si uno de los ficheros empieza a crecer demasiado, o si tiene sentido agrupar cierta funcionalidad o romperla, nos podemos plantear crear una subcarpeta y realizar la división del fichero por contenido, esto puede pasar si por ejemplo una _api_ crece mucho, o si un componente lo rompemos en subcomponentes.

# Estructura de un pod.

La solución de _pods_ que usamos está inspirada en _Ember_, parte de que cuando desarrollamos en un proyecto medio/grande es complicado:

- Encontrar el fichero que queremos editar.
- Saber si un cambio en un fichero puede afectar a otra página / funcionalidad que lo use.
- Hacernos con el conocimiento de la funcionalidad que queremos editar.
- No impactar en el trabajo de otros compañeros (generar conflictos).
- Detectar si algo es funcionalidad común o específica.

Para ello planteamos utilizar el concepto de _pod_:

**Un pod es un módulo de código que tiene todo lo necesario para funcionar.**

Es decir salvo funcionalidad _cross/común_ (que la movemos a carpetas raíz, como vimos en la sección de carpetas), cada _pod_ tiene encapsulado todo lo necesario para funcionar, y no depende de nada más que de lo que está dentro de su carpeta (salvo carpetas comunes / transversales).

Así pues (salvo funcionalidad común), un pod tiene:

- Definidas a que API's va a acceder y que modelo de datos de api va a consumir.
- Definidas que _ViewModels_ va a usar.
- Definido su contenedor, components y subcomponentes.
- Definido su módelo y validaciones.

Esto permita que un desarrollador que no conozca el proyecto, pueda en un tiempo razonable estudiar cómo funciona un _pod_ en concreto y saber dónde tocar para introducir una modificación.

De esta manera:

- El tiempo de entrada en un proyecto es menor.
- Las colisiones otros compañeros son menores (cada uno toca su _pod_).
- Con los _viewModels_ enfocamos los datos a la vista.
- En caso de hacer una migración es más fácil ir migrando por _pods_.
- A la hora de añadir pruebas unitarias o plantear seguir TDD para ciertas partes del código, es más fácil ya que rompemos el código en piezas simples que hacen una cosa y sólo una cosa.

Sobre la separación de ficheros, es como comentamos en la sección de "_ficheros_", la idea es separar el _pod_ en piezas que hagan una cosa y una sola cosa:

- **.container.tsx**: para componentes contenedores, es decir que tienen lógica y presentación.

- **.component.tsx**: para componentes tontos, es decir que solo tienen presentación (o al menos no el peso fuerte de lógica)

- **.business.ts**: para fichero de con funciones puras que resuelvan problemas (también se pueden plantear con estado, pero todo lo que no tenga que ver con React, código plano JS)
- **.hook.ts**: para almacenar hooks (funciones que usan _hooks_ de React).

- **.api.ts**: aquí podemos almacenar código para gestionar llamadas a API's rest.

- **.mapper.ts**: para convertir de modelo de API a _ViewModel_.

- **.model.ts**: para definir modelos de datos (de API o global), depende del equipo se podría pensar en romper en más niveles de modelo (por ejemplo _api-model_, _domain-model_... un consejo aquí es no meter más complejidad si no es necesario).

- **.validation.ts**: cuando extraemos la lógica de validación de un formulario a un fichero aparte, esto también es muy útil ya que es fácil de probar y lo tenemos localizado y no esparcido por el árbol de componentes de UI.
- **.vm.ts**: Aquí definimos el modelo de la vista, habrá ocasiones en que ese módulo sea un mapeo de uno a uno con lo que nos traemos con la API (sobre todo si los que desarrollan la API son los mismos que desarrollan la aplicación), pero en otros casos puede ser que tengamos que mapear valores y realizar transformaciones para que se ajusten a la vista (esa complejidad la sacamos del UI y la pasamos a una función pura JS, fácil de
  probar).

Un tema muy importante a la hora de definir los _ViewModels_, es que no puedo importar un _ViewModel_ de otro _pod_, por muy parecido que sea tengo que volver a crearlo, ¿Por qué? Por no quiero tener acoplamientos entre _pods_, otro tema es un _ViewModel_ que se use en todas la aplicación (por ejemplo un _Lookup Id/Valor_), en ese caso lo promociono a _core/model_ o _core/vm_ lo que mejor encaje con el equipo.

Para aprender más sobre este tipo de solución, tenemos repositorios y formaciones específicas para esto.

Desventajas de los pods:

- Si el proyecto es pequeño puede ser un _overkill_.
- Se fragmentan en muchos ficheros y hay que entender que hace cada uno.
- Hay que saber medir que funcionalidad es común / transversal y cual no.
- Otro tema es muchas veces terminamos con un _pod_ por _escena_, o un modelo de api muy parecido al de _vm_, con el trabajo adicional de fontanería que implica (_mappers_, _vm_, ...)

¿Qué pasa si empiezo a tener muchos _pods_? Aquí me puedo plantear crear una carpeta superior de "module" y agrupar por funcionalidad (ver sección carpeta)

# Nombrando funciones, eventos callbacks...

Nombrar elementos de código, es una tarea complicada y la base para que un desarrollador (o el propio programador una semana después) pueda entender ese código o seguirlo, para ello es importante que el equipo esté de acuerdo en seguir una serie de reglas.

## Nombrando componentes, hooks, funciones

Sobre los nombres de componentes:

- Como estamos con React siempre tienen que empezar por mayúsculas (los que empiezan por minúsculas están reservados para elementos básicos de HTML).
- Hay que estudiar si añadir sufijos, si es un contenedor añadir al nombre del componente "_Container_", si es componente "_Componente_", esta decisión no es clara, tienes sus pros y cons:
  - Por un lado cuando usamos un componente sabemos que lo es "_patientComponent_" porque lo tiene en el nombre (por ejemplo no se confunda con la entidad _Patient_).
  - Por otro lado es muy pesado arrastrar "_Component_" para cada componente.

Sobre los nombres en entidades (_model_ y _vm_), aquí el equipo debe de plantear si añadir sufijo entity o vm para distinguir: es buena idea añadir _vm_ a una entidad de _Viewmodel_, ya que así evitamos confusiones cuando importamos de forma automática entidades (no puede traer un nombre con entidad de _api model_ y se nos puede complicar darnos cuenta de ese fallo tonto).

Sobre los nombre de _hooks_, aquí seguir las recomendaciones del equipo de Facebook, es decir que empiecen por "_use_" y que sean verbos.

Sobre los nombres de funciones, aquí hay que tener en cuenta que las funciones puras son muy fáciles de testear, por lo que es muy recomendable que sean verbos y que describan.

## Eventos y callbacks

Un tema importante cuando gestionamos eventos es saber de forma fácil que función maneja un evento en nuestro componente, y cual se burbujea con una _prop_.

Aquí es bueno que el equipo este de acuerdo en seguir una aproximación consistente.

En nuestro caso proponemos nombrar los manejadores de eventos locales con el prefijo _handle_ y los que se burbujean con _props_, con el prefijo _on_, un ejemplo:

```tsx
export const App = () =>  {
  const [value, setValue] = React.useState("");
  const handleValueChange = (newValue : string) => {
    setValue(newValue);
  }

  return (
    <div>
      <NameEdit onValueChange={handleValueChange}/>
    </div>
  );
  )
}

interface Props {
  value : string;
  onValueChange : (value : string) => void;
}

export const NameEdit : React.FC<Props> = (props) => {

  const handleInputChange = (e) => {
    onValueChange(e.target.value);
  }

  return (
    <input value={value} onChange={handleInputChange}/>
  );
}
```

<div style="page-break-before:always"></div>

# Principio de promoción

Hay ocasiones en las que está claro que un componente / función / hook... va a ser reutilizable, en ese caso directamente podemos colocarlo en la carpeta que toque.

En otros, dicha funcionalidad sigue el siguiente camino:

- Empieza a ser implementada, puede ser dentro del mismo componente, o en el mismo fichero.
- Si vemos que gana peso se puede extraer a un fichero aparte.
- Si vemos que es reutilizable se puede promocionar a la carpeta que toque (_common_, _core_, _common-app_).
- Si este componente demuestra valía y se puede usar en otros proyectos, lo promocionamos a librería.

De esta manera nos ahorramos crear falsos reusables, y los componentes/funciones/hooks se quedan en el nivel que toque.

# Política de pruebas unitarias

El tema del _testing_ es muy controvertido, aquí el equipo debe llegar a un acuerdo en el que estén cómodos y se pueda llevar a cabo.

En nuestro caso, la decisión que tomamos es:

- Trabajar con componentes UI a nivel de aplicación e incluir pruebas unitarias es complicad, ya que se suelen realizar muchos cambios, y se pierde parte de agilidad (_refactors_, etc...), si detectamos un componente que sea crítico a nivel de aplicación si le añadimos pruebas unitarias, si no delegamos en e2e.
- Lo bueno es que esos componentes los solemos dejar lo más vacío posible (_vaciar el cangrejo_), es decir si aplicamos _Unit Testing_ en los siguientes elementos a nivel de aplicación (de ahí la importancia de vaciar los componentes):
  - _Hooks_.
  - Funciones puras y negocio (aquí podemos aplicar TDD)
  - _Mappers_ (aquí aplicamos TDD)
  - API.
- También aconsejamos realizar pruebas unitarias de _common_ y _core_, ya que es base de código que se va a usar de forma pesada en la aplicación.
- El resto lo cubrimos con e2e _testing_ el resto (_cypress_).

# Herramientas

Aquí cada equipo decide que herramientas usar y ser consistente con la elección.

El mínimo con el que trabajamos es _Prettier_ y el plugin para evitar que se haga un _push_ sin haber aplicado _prettier_ a los ficheros.

Por otro lado utilizamos la herramienta para gestionar PR del proveedor de _Git_ que estamos usando, normalmente _Github_.

Después se pueden añadir herramientas tanto en local y/o _CI/CD_:

- _Linting_: Esto es decisión personal del equipo, a veces puede ser fuente de discusión sobre que reglas aplicar o no.
- Herramientas para detección de malos olores en el código (por ejemplo SonarQube)
- Herramientas para detección de vulnerabilidades y librerías que necesitan actualización (por ejemplo _SonarQube_).
