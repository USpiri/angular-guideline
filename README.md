
# **Angular architecture, recommendations and good practices**

Serie de lineamientos para la estructura, organización y buenas prácticas de proyectos en Angular, ayudando a escribir código limpio y mantener un standar de código y performance.  
Este artículo no pretende ser una guía explicando cada concepto de Angular, tampoco ser reglas estrictas, como indica el título son recomendaciones.

## Indice:

- [Angular](#angular)
- [Versión de Angular](#version-de-angular)
- [Angular CLI](#usar-angular-cli)
- [Comandos utiles](#comandos-utiles)
- [Angular Coding Style](#angular-coding-style)
- [Angular Configuration](#angular-configuration)
- [Angular Modules](#angular-modules)
- [Angular Components](#angular-components)
- [Angular Standalone Components](#angular-standalone-components)
- [File naming](#file-naming)
- [Folder Structure](#folder-structure)
- [Barrels](#barrels)
- [Lazy Loading](#lazy-loading)
- [Data Infrastructure](#data-infrastructure)
- [Spinner](#spinner)
- [Pipes](#pipes)
- [Observables](#observables)
- [Angular Material](#angular-material)


## **Angular**  

Angular es un Framework de JavaScript de código abierto escrito en **TypeScript**. Su objetivo principal es desarrollar aplicaciones de una sola página. Google se encarga del mantenimiento y constantes actualizaciones de mejoras para este framework.  

#### Version de Angular

Esta guideline fue creada pensando en la versión de **Angular 14**

## **Usar Angular CLI**

[Angular CLI](https://cli.angular.io/) es la herramienta por defecto a la hora de desarrollar una app en Angular. Este CLI facilita la creación de la aplicación siguiendo con buenas prácticas por lo que un buen consejo es **Usar Angular CLI** para generar los nuevos componentes, directivas, módulos, servicios y pipes.

#### Comandos utiles

```c
// Instalar Angular CLI
npm install -g @angular/cli

// Verificar la versión instalada
ng version

// Iniciar un nuevo proyecto Angular
ng new <app-name>

// Generar un nuevo componente
ng generate component|directive|module|service|pipe <component-name>

// Ejecutar la aplicación localmente
ng serve

// Instalar dependencia
npm install @<dependency-name>

// Agregar dependencia
ng add @<dependency-name>
```

## **Angular Coding Style**

Serie de lineamientos descriptos en [Angular Coding Style](https://angular.io/guide/styleguide) que se repetirán a lo largo del documento. Estas son algunas de las reglas que se necesitan seguir para que un proyecto cumpla con el standard de Angular style guide:

1. Limitar **archivos a 400 líneas de código**, considerar atomizar.
2. Definir **funciones pequeñas** y específicas, limitándolas a **75 líneas**.
3. Mantener la **consistencia en la nomenclatura**.
4. Si existen **variables que deben permanecer intactas**, declararlas como **"const"**.
5. El nombre de las **propiedades y los métodos** deben **SIEMPRE** el minúsculas y **camelCase**
6. **Siempre dejar un salto de línea entre los imports** y los módulos como así también entre las importaciones de terceros y de los módulos personalizados.
7. **Utilizar prefijos personalizados** para prevenir que los nombres de los elementos entren en conflicto con otros componentes o incluso con componentes nativos de HTML.
8. **Nunca** se deberá **nombrar a una interface con la primer letra mayúscula "I"** como se suele hacer en otros lenguajes de programación,
9. Seguir en mayor medida el **principio de Single Responsibility**.
10. Es importante **no crear más de un componente|servicio|directiva dentro de una misma carpeta**, esto viola el principio de Single Responsibility.

## **Angular Configuration**

Algunas modificaciones a los archivos de configuración creados por defecto que nos facilitarán el acceso o la legibilidad del código.

### **Path alias (tsconfig.json)**

Representan atajos para acceder a nuestros archivos permitiéndonos implementar imports más cortos que faciliten la comprensión.

```json
"paths":{
   "@src/*":[
      "src/*"
   ],
   "@assets/*":[
      "src/assets/*"
   ],
   "@shared/*":[
      "src/app/shared/*"
   ]
}
```

### **Angular Configuration (angular.json)**

Es solo una recomendación disponible para usar a la hora de compilar nuestra aplicación.   
Reemplaza el archivos de environments por environments.local en caso de que se trabaje en desarrollo y environments.prod para producción.

```json
"configurations": {
    "development": {
        "budgets": [
            {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
            }
        ],
        "fileReplacements": [
            {
                "replace": "src/environments/environment.ts",
                "with": "src/environments/environment.local.ts"
            }
        ],
        "optimization": false,
        "buildOptimizer": false,
        "vendorChunk": true,
        "extractLicenses": false,
        "sourceMap": true,
        "namedChunks": true
    },
    "production": {
        "budgets": [
            {
                "type": "initial",
                "maximumWarning": "2mb",
                "maximumError": "5mb"
            }
        ],
        "fileReplacements": [
            {
                "replace": "src/environments/environment.ts",
                "with": "src/environments/environment.prod.ts"
            }
        ],
        "optimization": true,
        "buildOptimizer": true,
        "vendorChunk": false,
        "outputHashing": "all",
        "extractLicenses": true,
        "sourceMap": false,
        "namedChunks": false
    }
}
```

## **Angular Modules**

Antes de introducir la estructura de carpetas (Folder structure) vamos a definir el concepto y uso de módulos. Angular se basa en el principio de *Separar responsabilidades*, la esencia de este principio es que el código debe desarrollarse y agruparse de tal forma que cada una tenga una"***Responsabilidad única***". Los módulos son un grupo de componentes, servicios y pipes que trabajan en conjunto para proporcionar un "*Conjunto de características/funciones específicas*".  
Esta práctica hace posible la implementación del Lazy Loading junto con la carga de Children Components, tema que se abordará más adelante.

## **Angular Components**  

### **Atomizar componentes**

Este punto debería considerarse como extensión del principio de single responsibility aplicado no solo a las carpetas y al código, sino que a los componentes también. En donde se crea cada componente con el fin de ejecutar una función específica. Mientras más largo se vuelva un componente, más difícil será mantenerlo e iterar sobre él.   
Si un componente se comienza a tornar demasiado grande, lo ideal es descomponerlo en componentes más pequeños, más manejables y dedicados a funciones muy específicas.  
Recordar que una buena práctica es **limitar los componentes a 400 líneas de código**.

### **Reutilizar componentes**

Si exite alguna parte de la interfaz de usuario (UI) que se necesite usar en muchos sitios de nuestra aplicación, crearemos un componente externo que luego haremos uso del mismo donde se necesite. Esta acción, a la larga, terminará ahorrando tiempo cuando por ejemplo se requiera hacer un cambio a la IU. Para este caso no tendremos que buscar en todos los archivos el la parte de la UI que queramos modificar, solo tendremos que modificar el componente.

### **Component selectors**

Para los selectores de componente, en caso de que decidamos cambiar el que se crea por defecto con el CLI, debe usarse dashed-case o kebab-case.  
**Utilizar prefijos personalizados** para prevenir que los nombres de los elementos entren en conflicto con otros componentes o incluso con componentes nativos de HTML e  identificar a la "característica/funcionalidad" a la que pertenece el componente.  

```c
// Usar
    <prefix>-custom-button

// Evitar
    prefixCustomButton
```

## **Angular Standalone Components**

El término "standalone" se refiere a componentes, directivas o pipes que pueden ser usadas independientemente de un NgModule. En esta página se encuentra el funcionamiento y creación de ellos. [Angular Standalone Components](https://www.telerik.com/blogs/angular-14-introducing-standalone-components)

## **File naming**

Mantener la **consistencia en la nomenclatura** se vuelve muy importante para la mantención y legibilidad de código, también ayuda a encontrar más rápido el contenido deseado.

### **Separar el nombre de los archivos con puntos y guiones**

Usaremos los guiones ("**-**") para separar palabras dentro del *nombre descriptivo* para el archivo y puntos ("**.**") para separar el anterior del tipo de archivo. El patrón recomendado es:  

```c
<descriptive-name>.<type>.ts|.css|.html

// Ejemplo
feature-name.component.ts

// Evitar
featureName.compnt.ts
```

Los tipos de archivos convencionales dentro de Angular son `.service`, `.component`, `.pipe`, `.module`, `.directive`. Adicionalmente se pueden crear "tipos" propios pero *no se recomienda crear demasiados*. Estos **no deben ser nombres abreviados** (Como `.srvc`) para evitar confusiones y **deben ser descriptivos e inequívocos**.  

## **Folder Structure**

Definir la folder structure es un factor importante a considerar a la hora de crear nuestro proyecto. Nuestra estructura deberá adaptarse fácilmente a los nuevos cambios que surjan durante el desarrollo.  
La siguiente estructura fue creada con un módulo "home" de ejemplo, junto con algunos componentes y determinadas carpetas bajo el supuesto de que se utiliza [Angular Material](https://material.angular.io/). Debajo se explicará cada carpeta y archivo graficado.

```
.
├── app/
│   ├── components/
│   │   ├── shared-component/
│   │   │   └── shared-component.component.css|.html|.spec.ts|.ts
│   │   └── ...
│   ├── models/
│   │   └── home.interface.ts
│   ├── modules/
│   │   ├── home/
│   │   │   ├── components/
│   │   │   │   ├── home-button/
│   │   │   │   │   └── home-button.component.css|.html|.spec.ts|.ts
│   │   │   │   ├── dialogs/
│   │   │   │   │   ├── home-dialog/...
│   │   │   │   │   └── home-table-dialog/...
│   │   │   │   └── ...
│   │   │   ├── services/
│   │   │   │   ├── home.service.ts
│   │   │   │   ├── home.service.spec.ts
│   │   │   │   └── ...
│   │   │   ├── shared/
│   │   │   │   ├── materials/
│   │   │   │   │   └── materials.module.ts
│   │   │   │   ├── pipes/
│   │   │   │   │   ├── home.pipe.ts
│   │   │   │   │   └── ...
│   │   │   │   └── directives/...
│   │   │   ├── home-routing.module.ts
│   │   │   ├── home.component.css|.html|.spec.ts|.ts
│   │   │   └── home.module.ts
│   │   └── another-module/...
│   ├── services/...
│   ├── shared/
│   │   ├── animations/
│   │   │   ├── animations.module.ts
│   │   │   └── ...
│   │   ├── directives/
│   │   │   └── ...
│   │   └── pipes/
│   │       └── ...
│   ├── app-routing.module.ts
│   ├── app.component.css|.html|.spec.ts|.ts
│   └── app.module.ts
├── assets/
│   ├── data/...
│   ├── fonts/...
│   └── images/...
├── environments/
│   ├── environment.local.ts
│   └── environment.prod.ts
├── styles/
│   ├── custom-theme.scss
│   ├── component-theme.scss
│   └── ...
├── index.html
├── styles.css
├── main.ts
└── polyfills.ts
  
```

### **Descripción**

La idea principal es separar las funciones/lógica de manera organizada y por categorías. Cada módulo (De `./app/modules`) deberá contenerse a sí mismo y no depender de otros (Independencia) para reforzar el concepto de Lazy Loading donde cada uno cargará *SOLO* si es necesario.  
Para aquellos componentes, servicios, etc. que sean necesarios en más de un módulo (Ejemplo: módulos de Angular Material) se creará un shared.module (En `./app/shared`) para importarlos y que desde aquí sean exportados a toda la app.  
Para los servicios compartidos es recomendable implementarlos directamente en `./app/app.module`. Ya que hacerlo en un shared.module podría implicar la creación de diferentes instancias del servicio imposibilitando la comunicación a lo largo de la aplicación.  

- `./app/component`: Carpeta para componentes compartidos por varios módulos o componentes standalone.
- `./app/models`: Carpeta de interfaces y clases. Archivos que podrán llamarse con el tipo ".interface".
- `./app/modules`: Carpeta principal de la app, aquí se encontrará cada módulo como se han definido previamente y conformarán un path distinto para nuestra app. Estos deben ser independientes el uno del otro.
- `./app/services`: Carpeta para los servicios compartidos por varios módulos.
- `./app/shared`: Carpeta para los recursos compartidos por varios módulos, como pipes, directives y animations.  
- `./styles`: Carpeta para almacenar el theme de Angular Material junto con customizaciones de clases para el mismo.
- `./environments/environment.local.ts`: Reemplazo de environment.ts por environment.local.ts (Explicado más adelante)

### **Module sub-structure**

Cada módulo en `./app/modules` contendrá una sub-estructura similar a la que contiene el módulo principal (`./app`)  

- `./app/modules/home/component`: Carpeta para componentes pertenecientes al módulo home.
- `./app/modules/home/services`: Carpeta para los servicios utilizados por el módulo home.
- `./app/modules/home/shared`: Carpeta para los recursos compartidos por los diferentes componentes del módulo home, como pipes, directives, animations y material modules.  
- `./app/modules/home/home.module.ts`: Módulo home.
- `./app/modules/home/home-routing.module.ts`: Módulo routing de home. Importante para la implementación de Lazy Loading
- `./app/modules/home/home.component.css|.html|.spec.ts|.ts`: Componente principal home.  

## **Barrels**

Para ayudar a la organización del proyecto podemos utilizar conceptos como *"Barrels"*. La principal idea es agregar un archivo llamado "index.ts" para cada una de las carpetas que contengan un archivo .ts. Dentro de este se exportará cada una de las variables exportables de los .ts permitiendo crear una especie de jerarquía que nos va a ayudar a acortar largas cadenas de imports.  
Son solo una recomendación ya que ellos no aparecen en la Angular Style Guide.

#### **Ejemplo:**  

Supongamos 3 componentes diferentes dentro de `./app/modules/home/component`:  

```ts
export class SomeComponent {} 
export class SomeClass {} 
export class SomeService {} 
```

Sin Barrels necesitaríamos 3 imports diferentes para cada uno de estos componentes:

```ts
import { SomeComponent } from '../component/some.component.ts';
import { SomeClass }     from '../component/some.model.ts'; 
import { SomeService }   from '../component/some.service.ts'; 
```

Agregando este barrel dentro de `./app/modules/home/component`  

```ts
export * from './some.model.ts';   // re-export all of its exports 
export * from './some.service.ts'; // re-export all of its exports 
export { SomeComponent } from './some.component.ts'; // re-export the named thing 
```

Ahora solo tendríamos que importar lo que necesitemos de este barrel  

```ts
import { SomeComponent, SomeService, SomeClass } from '../some'; // el archivo index está implicito
```

**Nota:** Esta [extensión](https://marketplace.visualstudio.com/items?itemName=mikehanson.auto-barrel) resulta muy útil para la implementacion de Barrels

## **Lazy Loading**

Una buena práctica es cargar de forma perezosa (lazy load) los módulos de nuestra aplicación siempre que sea posible. Obligando a que *SOLO* cargue lo necesario cuando sea necesario permitiendo que el tiempo de arranque de nuestra app disminuya considerablemente y nos brinde mayor seguridad al proyecto.  

#### **Sin Lazy Loading**

```ts
import ...;

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', component: HomeModule },
  { path: '**', redirectTo: 'home' }
];

@NgModule({...})
export class AppRoutingModule { }
```

#### **Con Lazy Loading**

```ts
import ...;

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', loadChildren: () => import('./modules').then( m => m.HomeModule ) },
  { path: '**', redirectTo: 'home' }
];

@NgModule({...})
export class AppRoutingModule { }
```

## **Data Infrastructure**

### **Variables intactas**

**Las variables intactas (Que no van a ser modificadas) deben declarada como "const"** tal y como se indica en la guía de Angular.

### **Evitar usar el tipo "any"**

Declarar variables o constantes con sus respectivos tipos evitará cometer errores involuntarios. Por lo que una buena práctica será intentar **siempre declarar el tipo de dato** en la medida que sea posible.

```ts
// Evitar errores de este tipo

const a = 1;
const b = 'dos';
const c = a + b;

console.log('Valor de Z: ' + x); //Resultado no deseado = Valor de Z: 1dos

// Aplicar el uso de tipos estrictos nos ayudará a prever

const a:number = 1;
const b:number = 'dos';
const c:number = a + b;

console.log('Valor de Z: ' + x); 

// Esto nos devolverá un error de compilación avisando que 'dos' no es asignable a una variable de tipo number

```

### **Utilizar Interfaces**

Mediante el uso de interfaces podemos forzar que las clases implementen las funciones y propiedades que se extienden de la interface. Typescript no mostrará un error si un objeto no contiene todas las clases de nuestra interface.

```ts
export interface SomeInterface {
  text: string;
  duration?: number;
  color?: string;
}
```

También es posible la utilización de `namespaces` para evitar confundir nuestra interface con otras clases distribuidas dentro de la aplicación  

```ts
export namespace SomeNamespace {

  interface LogOptions {
    verbose?: boolean;
  }

  interface SomeInterface {
    text: string;
    duration?: number;
    color?: string;
  }

}
```

### **Safe strings**

La declaración de "Safe string" puede ser muy beneficiosa en determinados casos. Estas variables (de tipo string) tienen la particularidad que siempre tienen solo un conjunto de valores posibles y nosotros podemos declarar una lista de ellos. Entonces la variable solo aceptará estos posibles valores y de esta forma evitar bugs inesperados mientras escribimos el código.

```ts
// Declaración normal de un string en donde podemos asignarle cualquier valor a un string

private vehicleType: string;
this.vehicleType = 'four wheeler';
this.vehicleType = 'two wheeler';
this.vehicleType = 'car';

// Declaración con Safe String

private vehicleType: 'four wheeler' | 'two wheeler';
this.vehicleType = 'four wheeler';
this.vehicleType = 'two wheeler';

this.vehicleType = 'car'; // Esta linea nos va a retornar el siguiente error

Type '"car"' is not assignable to type '"four wheeler" | "two wheeler"'

```

### **Safe navigation operator (?)**

A la hora de mostrar datos dentro de un template, si el objeto fuese null e intentamos acceder a alguna de sus propiedades, lo que obtendremos es una excepción, un error. Pero si utilizamos el Safe navigation operator (?) El template va a ignorar la variable nula y procederá a acceder a ella una vez deje de ser nula.

```html
<span>{{ text?.value }}</span>
```

## **Spinner**

El uso del spinner radica en informar al usuario que la carga de los datos (lo que utilizamos en el proyecto) están en proceso. Básicamente es un componente que muestra el estado de carga dentro de la aplicación. Este puede recibir un boolean para mostrarse u ocultarse.

**load.service.ts**
```tsx
import { Injectable } from '@angular/core';
import { AbstractControl } from '@angular/forms';
import { MatAutocomplete, MatAutocompleteSelectedEvent, MatChipInputEvent } from '@angular/material';
import { Subject } from 'rxjs';

export interface LoaderState {
  show: boolean;
}

@Injectable({
  providedIn: 'root'
})
export class LoadService {

  spinnerStatusState = new Subject<any>();
  
  constructor() {}
  
  displaySpinner(value: boolean) {
    this.spinnerStatusState.next(<LoaderState>{ show: value });
  }

}
```

**app.component.ts**
```tsx
import { Component, ChangeDetectionStrategy, OnInit } from '@angular/core';
import { LoadService } from './services/load.service';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent implements OnInit {

  show = false;

  constructor(private loadService: LoadService) {}

  ngOnInit() {
    this.loadService.spinnerStatusState.subscribe(state => {
      setTimeout(() => {
        this.show = state.show;
      }, 0);
    });
  }

}
```

**app.component.html**
```html
<ng-container>
  <span *ngIf="show"><app-spinner></app-spinner></span>
  <app-navbar> <router-outlet></router-outlet> </app-navbar>
</ng-container>
```

**spinner.component.ts**
```tsx
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-spinner',
  template: `
    <div class="loading"></div>
  `,
  styleUrls: ['./spinner.component.scss']
})
export class SpinnerComponent implements OnInit {

  constructor() {}

  ngOnInit() {}
  
}
```

**Nota:** El estilo es a elección, incluso puede cambiarse por un gif o cualquier otra cosa que se quiera.

**Cómo usarlo:**  

 Inyectar el servicio en el constructor del componente y después de llamar a un endpoint ejecutar: `this.loadService.displaySpinner(true);`

## **Pipes**

Una pipe es una herramienta que nos permite modificar o transformar la información presentada en pantalla. La data de la información no se modifica, sólo se transforma y se presenta de una determinada manera pero es sólo a efectos de presentación. Si bien su fin es solo estético, su implementación se basa en la reusabilidad y la simplificación de la arquitectura de un proyecto separando, posiblemente, procesos matemáticos o semánticos.

```html
<!-- Esta pipe transforma el texto a uppercase -->
<p>{{ someString | uppercase }}</p>
```

### Async Pipe

Los observables pueden ser usados directamente en el template con las pipe async, cuando se destruye el componente que está usando el template el observable se desuscribe automáticamente.  
Se vuelven útiles para mostrar en el componente los valores obtenidos y cancelar automáticamente la suscripción y así limitar las fugas de memoria innecesarias.

**some.component.html**
```html
<p>{{ someString | async }}</p>
```

**some.component.ts**
```ts
this.text = observable.pipe(
  map(value => value.item)
);
```

## **Observables**

Se pueden combinar los observables. Algunas veces nos encontraremos con que estamos llamando a más de un endpoint a la vez, entonces ¿Porqué no combinarlos?  
Esto es posible gracias a `combineLatest` de rxjs. De esta forma, incluso si alguno de los valores no llega, podremos acceder al resultado (o error) de la suscripción.


```tsx
import { combineLatest } from 'rxjs';

...ngOnInit() {

  combineLatest(
    this.yourService.getObservable1(),
    this.yourService.getObservable2()
  ).subscribe(
    ([result1, result2]) = {
      ...
    }
  )

}
```

De esta forma resulta mucho más fácil desuscribirnos en el ngOnDestroy.  

### **Unsuscribe**

Una buena recomendación es que (Por más que Angular por defecto se desuscriba de las peticiones HTTP automáticamente), si se sabe que el observable va a traer información una única vez desde el back, usar `.pipe(first())` despues de suscribirnos al componente. De esta forma una vez obtenidos los datos el sistema se desuscribe solo.

En caso de necesitar una constante actualización de los datos utilizaremos el ngOnDestroy para desuscribirse del observable.

```tsx
private subs: Subscription | undefined;

ngOnDestroy(){
  if (this.subs){
    this.subs.unsuscribe():
  }
}

private handleSubsOrder( orderId:string ){
  this.subs = this.cartCheckoutFacade.startOrderFulfilmentPolling(irderId)
}
```

## **Angular Material**

https://kindly-windflower-254.notion.site/Angular-Material-as-a-base-for-reusable-components-6af5855328284905b4bd5a3a66354b91

https://ngrx.io/guide/store

https://materialtheme.arcsine.dev/
