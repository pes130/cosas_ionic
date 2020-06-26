
# 1. Managers de interés
## 1.1. ModalController
Te permite la gestión: creación, mostrar/ocultar, destrucción de modales.
Para crearlo:
1. Lo creas como una página cualquiera:
```bash
ionic g page miModal
```
2. Ionic cli te añade la ruta automáticamente a `app-routing.module.ts`, quítalo.
3. Añadelo en `app.module.ts`:
```typescript
    ...
    imports: [
        ...,
        MiModalPageModule,
        ...
    ],
    ...
```
4. Inyéctalo en el controlador de la página donde lo necesites:
```typescript
    constructor(private modalCtrl:ModalController) {
        ...
    }
```
5. Lo creamos y mostramos de la siguiente manera:
```typescript
    abrirModal() {
        // creamos y abrimos
        this.modalCtrl.create({
            component: MiModalPage,
            // le pasamos argumentos en componentProps
            componentProps: {
                parametro1: 'valor',
                parametro2: 'valor'
            }
        }).then((modal)=>{
            modal.onDidDismiss().then(()=>{
                // LO que queramos ejecutar al cerrar.
            });
            // Mostramos el modal ya creado
            modal.present();
        })
    }
```
## 1.2. AlertController
Para mostrar alertas
1. En el controlador donde deseemos usarlo, lo inyectamos así:
```typescript
constructor(private alertCtrl:AlertController) {

}
```

2. Función para abrir un alert:
```typescript
async abrirAlert(){
    const alert = await this.alertCtrl.create({
        header: 'Cabecera del alert',
        message: 'Mensaje del alert',
        buttons: [
            {
                text: 'No',
                role: 'Cancel',
                cssClass: 'secondary',
                handler: (blah) => {
                    console.log('Le has dado a cancelar');
                }
            },
            {
                text: 'Sí',
                handler: () => {
                    console.log("Le has dado a sí");
                }
            }
        ]
    });
    // Lo mostramos
    await alert.present();
}
```


# 2. Plugins de interés
Veamos algunos plugins de capacitor.
## 2.1. Cámara
En nuestro controlador, hemos de hacer lo siguiente:
1. Importarlo:
```typescript
import { Plugins, CameraResultType } from '@capacitor/core';
const { Camera } = Plugins;
```

2. Usarlo en una función:
```typescript
async sacarFoto() {
    try {
        const miFoto = await Camera.getPhoto({
            quality: 50,
            height: 400,
            width: 600,
            allowingEditing: false,
            resultType: CameraResultType: Base64
        });
    } catch(error) {
        console.error(error);
    }
}
```

Para poder usarla en la versión de escritorio, necesitamos la librería PWA Elements de Ionic. Tenemos que:
1. Importarla:
```bash
npm install @ionic/pwa-elements
```

2. En src/main.ts escribir:
```typescript
import { defineCustomElements } from '@ionic/pwa-elements/loader';
defineCustomElements(window);

...

platformBrowserDynamic().bootstrapModule(AppModule)
    .catch(err => console.log(err));
...
```

## 2.2. Base de datos local
Vamos a hacerlo con **ionic storage**. 
1. tenemos que instalarlo:
```bash
npm install --save @ionic/storage
```
2. Importarlo en `imports` en **app.module.ts**:
```typescript
...
imports: [
    ...,
    IonicStorageModule.forRoot(),
    ...
]
```
3. Instalar plugin de sqlite:
```bash
npm install cordova-sqlite-storage --save
```

4. Inyectarlo en un servicio:
```typescript
constructor(private storage:Storage) {
    ...
}
```

5. Guardar algo (de la forma clave: valor):
```typescript
this.storage.set('variable1', valor1);
```

6. Recuperar algo:
```typescript
variable1 = this.storage.get('variable1');
```


# 3. Componentes de ionic


# 4. Crear un módulo de componentes
Buena forma de organizar elementos. Por ejemplo, para organizar mis propios componentes. 
En esencia necesito:
1. Crear un módulo donde declararemos nuestros componentes. Luego, donde quieras usarlo, importas ese módulo.
2. Declarar componentes dentro del módulo.

Veamos:
1. Crear un módulo:
```bash
ionic g module componentes
```

2. Creamos un componente:
```bash
ionic g component componentes/prueba
```

3. Declararlo en nuestro módulo. Lo declaramos y lo marcamos como exportable:
```typescript
...
@NgModule({
  declarations: [
    PruebaComponent
  ],
  imports: [
    CommonModule
  ],
  exports: [
    PruebaComponent
  ]
})
...
```

4. Para utilizar uno de nuestros componentes en cualquier página, tenemos que importar el **ComponentesModule** en el módulo de la página en cuestión, declarándolo en los imports. Para usarlo en la HomePage, lo importamos en home.module.ts, así:
```typescript
...
@NgModule({
  imports: [
    CommonModule,
    FormsModule,
    IonicModule,
    HomePageRoutingModule,
    ComponentesModule
  ],
  declarations: [HomePage]
})
...
```
5. Ya podemos usarlo en el html de la página que queramos (en nuestro caso, home.page.html), haciendo uso de su etiqueta:
```html
...
<ion-content>
    <app-prueba></app-prueba>
</ion-content>
```

## 4.1. ¿Pasar argumentos de entrada a un componente?
Es posible pasarle parámetros de entrada. Imagina que nuestro componente muestre un saludo a un nombre de persona que le pasemos como parámetro:
1. Se usa de esta manera:
```html
<ion-content>
    <app-prueba nombre="Segismundo"></app-prueba>
</ion-content>
```

2. En el controlador de nuestro componente **PruebaComponent**, recogemos este valor de la siguiente manera:

```typescript 
...
export class PruebaComponent implements OnInit {
    @Input() nombre: string;

    constructor () {}

    ...
}
```



# 5. Integración con Firebase
1. Instalar dependencias para firebase en un proyecto:
```bash
npm install firebase @angular/fire --save
```

2. Loguearte con tu cuenta de firebase en la terminal:
```bash
firebase login
```

3. Iniciar la integración con Firebase
```bash
firebase init
```

## 5.1. Firebase hosting
1. Instalar firebase CLI:
```bash
npm install -g firebase-tools
```
2. Desplegar mi aplicación:
```bash
firebase deploy
```

3. Listado de proyectos
```bash
firebase projects:list
```

4. Crear alias para un proyecto (por ejemplo, para crear la versión de producción)
```bash
firebase use  --add
```
Te aparecerá un asistente en modo texto.

### RESUMEN - workflow hosting
* ng build
* firebase deploy

Si uso entornos de desarrollo y producción:
* ng build --prod
* firebase use (alias de proyecto para producción)
* firebase deploy


## 5.2. Autenticación con Firebase
1. Importamos el módulo en app.module.ts:
```typescript
...
  imports: [
    ...
    AngularFireModule.initializeApp(environment.firebaseConfig),
    AngularFireAuthModule,
    ...
  ],
...
```