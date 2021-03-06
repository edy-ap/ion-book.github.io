---
layout: post
title: "Conectado una API REST con Ionic"
date: 2017-05-12
tags: [api, demos]
categories: ionic2
repo: "https://github.com/ion-book/demo103"
author: nicobytes
cover: "/images/posts/ionic2/2016-07-19-rest-api-with-ionic/cover.jpg"
remember: true
versions:
  - title: 'ionic'
    number: '3.3.0'
  - title: 'ionic-native'
    number: '3.10.3'
  - title: 'ionic-app-scripts'
    number: '1.3.7'
  - title: 'cordova-cli'
    number: '7.0.1'
  - title: 'ionic-cli'
    number: '3.3.0'
---

> Una parte fundamental de cualquier aplicación es conectarse con servicio externos, en este caso vamos a conectarnos con una **API REST**. Vamos a crear una aplicación que se contecte con [http://randomuser.me/](http://randomuser.me/){:target="_blank"} un API con información de usuarios aleatorios.

<amp-img width="1848" height="1039" layout="responsive" src="/images/posts/ionic2/2016-07-19-rest-api-with-ionic/cover.jpg"></amp-img>

# Actualización (01/05/2017)
<hr/>

Hemos actualizado este demo con el último release de **Ionic 3**, si aún estas en alguna de las versiones anteriores puedes seguir estos pasos [de Ionic 2 a Ionic 3](https://www.ion-book.com/blog/tips/ionic-2-to-ionic3/){:target="_blank"}.

Ademas en este demo usamos la función de [**lazy loading** y **@IonicPage**](https://www.ion-book.com/blog/tips/ionic-page-and-lazy-loading/){:target="_blank"}. Puedes ver el repositorio [**Demo103**](https://github.com/ion-book/demo103){:target="_blank"}

<hr/>

# Paso 1: Iniciando el proyecto

Lo primero que haremos será iniciar un nuevo proyecto con ionic, vamos a nuestra terminal y ejecutamos:

```
ionic start demo103 blank
```

Ionic crea una carpeta con el nombre del proyecto, nuestro siguiente paso será ubicarnos dentro a la carpeta del proyecto desde nuestra terminal con:

```
cd demo103
```

El proyecto inicia con el template **blank** y por esto tendremos una estructura básica del proyecto, la carpeta en la que vamos a trabajar será `src`:

<div class="row">
  <div class="col col-100 col-md-50 col-lg-50">
    <amp-img width="376" height="183" layout="responsive" src="/images/posts/ionic2/2016-07-11-camera-and-ionic/tree1.png"></amp-img>
  </div>
</div>

Agregamos la plataforma para la que vamos a desarrollar:

```
ionic cordova platform add android
ionic cordova platform add ios
```

## Paso 2: Importar **HttpModule**

Ahora debemos agregar `HttpModule` en nuestro archivo `app.module.ts`, este paso es muy importante si dentro de la aplicación se usa la dependencia `Http`:

```ts
...
import { HttpModule } from '@angular/http';

@NgModule({
  declarations: [
    MyApp
  ],
  imports: [
    BrowserModule,
    HttpModule,
    IonicModule.forRoot(MyApp),
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp
  ],
  providers: [...]
})
export class AppModule {}
```

# Paso 3: Crear provider

Vamos a usar [ionic generator]({{site.urlblog}}/tips/ionic-generator/){:target="_blank"} para crear nuestro nuevo proveedor de datos

```
ionic g provider user-service
```

ionic creará un archivo para nuestro servicio que estará en `src/providers/user-service.ts`:

Recuerda que debes agregar este provider dentro del array `providers` en `app/app.module.ts`, así:

```ts
...
import { UserService } from '../providers/user-service';

@NgModule({
  declarations: [
    MyApp
  ],
  imports: [
    BrowserModule,
    HttpModule,
    IonicModule.forRoot(MyApp)
  ],
  bootstrap: [IonicApp],
  entryComponents: [
    MyApp
  ],
  providers: [
    ...
    UserService,
    ...
  ]
})
export class AppModule {}
```

# Paso 4: Conectarse a Random User API

Random User es un **REST API** que nos retorna usuarios aleatoriamente, pueden consultar la docs de esta API [aquí](https://randomuser.me/documentation){:target="_blank"}.

Para conectarse solo debemos hacer una solicitud con el parámetro *results* que será la cantidad de usuarios que necesitemos así:

```
https://randomuser.me/api/?results=25
```

La respuesta de random user API será de esta manera:

```json
{
  "results": [
    {
      "gender": "male",
      "name": {
        "title": "mr",
        "first": "samuel",
        "last": "ross"
      },
      "location": {
        "street": "5592 pockrus page rd",
        "city": "santa ana",
        "state": "oregon",
        "postcode": 69974
      },
      "email": "samuel.ross@example.com",
      "login": {
        "username": "silverwolf434",
        "password": "blackjac",
        "salt": "d14iEZQT",
        "md5": "261a57061d35918f5c3ef7f90f4f2a80",
        "sha1": "c330ad8c974ab9982622d5d5506cfed4886a79ab",
        "sha256": "8ca4a1ca48975a69bd829de61298cd6a950dede740d4c0e755d07ee4cfc06fa5"
      },
      "registered": 1155461743,
      "dob": 1078494582,
      "phone": "(179)-102-8139",
      "cell": "(107)-396-0688",
      "id": {
        "name": "SSN",
        "value": "476-30-9095"
      },
      "picture": {
        "large": "https://randomuser.me/api/portraits/men/80.jpg",
        "medium": "https://randomuser.me/api/portraits/med/men/80.jpg",
        "thumbnail": "https://randomuser.me/api/portraits/thumb/men/80.jpg"
      },
      "nat": "US"
    }
  ],
  "info": {
    "seed": "705567e86a824a27",
    "results": 1,
    "page": 1,
    "version": "1.0"
  }
}
```

Nos retorna toda la información necesaria respecto a usuario, ahora dentro del `UserService`, vamos a crear el método `getUsers` que se encargará de obtener los datos de la API.

```ts
getUsers() {
  return this.http.get('https://randomuser.me/api/?results=25')
  .map(res => res.json())
  .toPromise();
}
```

La clase completa quedaría así:

```ts
import { Injectable } from '@angular/core';
import { Http } from '@angular/http';
import 'rxjs/add/operator/map';
import 'rxjs/add/operator/toPromise';

@Injectable()
export class UserService {

  constructor(
    private http: Http
  ) {}

  getUsers() {
    return this.http.get('https://randomuser.me/api/?results=25')
    .map(res => res.json())
    .toPromise();
  }
}
```

{% include blog/subscribe.html %}

# Paso 5: Inyectar el servicio.

Desde el archivo `home.ts` vamos a inyectar a `UserService`, de esta manera:

```ts
import { Component } from '@angular/core';
import { IonicPage, NavController } from 'ionic-angular';
import { UserService } from '../../providers/user-service';

@IonicPage()
@Component({
  selector: 'page-home',
  templateUrl: 'home.html',
})
export class HomePage {

  users: any[] = [];

  constructor(
    public navCtrl: NavController,
    public userService: UserService
  ) {}

  ionViewDidLoad(){
    this.userService.getUsers()
    .then(data => {
      this.users = data.results;
    })
    .catch(error =>{
      console.error(error);
    })
  }
}
```

Importamos el servicio `UserService` desde su ubicación, luego definiremos `users` como un array vacío, luego en el constructor inyectamos como una dependencia a `UsersService` y finalmente dentro del método `ionViewDidLoad` llamamos al método `getUsers` que hará la solicitud y finalmente la respuesta la asigna a `this.users`.

# Paso 6: El template

Ahora en el template de `home.html` lo único que nos queda es mostrar los usuarios, básicamente iteramos array de users y luego solo mostramos las atributos de cada usuario.:

```html
{% raw %}
<ion-header>
  <ion-navbar color="primary">
    <ion-title>
      Demo 103
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content>
  <ion-list>
    <ion-item *ngFor="let user of users">
      <ion-avatar item-start>
        <img [src]="user.picture.medium">
      </ion-avatar>
      <h2>{{ user.name.first | uppercase }}</h2>
      <p>{{ user.email }}</p>
    </ion-item>
  </ion-list>
</ion-content>
{% endraw %}
```

## Resultado:

<div class="row">
  <div class="col col-100 col-md-33 offset-md-33 col-lg-33 offset-lg-33">
    <amp-img width="1080" height="1920" layout="responsive" src="/images/posts/ionic2/2016-07-19-rest-api-with-ionic/screen1.jpg"></amp-img>
  </div>
</div>
<br/>