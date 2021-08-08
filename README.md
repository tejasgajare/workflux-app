# Create Standalone UI Component Library in Angular

Create a new Angular workspace without an application.
```bash
ng new flux --create-application=false
```

```bash
cd flux
```

Configure the library to use SASS rather than the default CSS styling.
```bash
ng config schematics.@schematics/angular:component.style scss
```

Generate a library in the workspace. We must explicitly mention the prefix. 
```bash
ng generate library @flux/ui --prefix=flux
```

Create a UI Component. We will use an example of a simple Button.
```bash
ng generate component button
```

Customise the components as per our needs.
#### **`button.component.ts`**
```diff
-   import { Component, OnInit } from '@angular/core';
+   import { Component, Input, OnInit } from '@angular/core';
    @Component({
    selector: 'flux-button',
    templateUrl: './button.component.html',
    styleUrls: ['./button.component.scss']
    })
    export class ButtonComponent implements OnInit {

    constructor() { }
+    
+   @Input() text: string | undefined;
+   @Input() disabled: boolean | undefined;
+
    ngOnInit(): void {
    }

}
```

#### **`button.component.html`**
```HTML
<button [disabled]="disabled">{{text}}</button>
```

Create a Button module to consolidate the component and services.
```bash
ng generate module button
```

#### **`button.module.ts`**
```diff
    import { NgModule } from '@angular/core';
    import { CommonModule } from '@angular/common';
+   import { ButtonComponent } from './button.component';

    @NgModule({
    declarations: [
+       ButtonComponent
    ],
    imports: [
        CommonModule
+   ],
+   exports: [
+      ButtonComponent
+   ]
    })
    export class ButtonModule { }
```

Create index file to export all the modules, components and services.
#### **`button/index.ts`**
```ts
export * from './button.component'
export * from './button.module'
```

This will allow us to export the button component with single export in public-api.
#### **`public-api.ts`**
```ts
export * from './lib/button'
```
Remove the ButtonComponent from the declarations (which is added by default with ng generate component commnand) in the root module. Add the ButtonModule to the imports.
#### **`ui.module.ts`**
```diff
    import { NgModule } from '@angular/core';
    import { UiComponent } from './ui.component';
+   import { ButtonModule } from './button';

    @NgModule({
    declarations: [
        UiComponent,
-       ButtonComponent
    ],
    imports: [
+       ButtonModule
    ],
    exports: [
        UiComponent
    ]
    })
    export class UiComponent { }
```

Finally, pack the library using the following scripts
#### **`package.json`**
```diff
    "scripts": {
        "ng": "ng",
        "start": "ng serve",
        "build": "ng build",
        "watch": "ng build --watch --configuration development",
        "test": "ng test",
+       "lint": "ng lint",
+       "e2e": "ng e2e",
+       "build-lib": "ng build @flux/ui",
+       "npm-pack": "cd dist/flux/ui && npm pack",
+       "package": "npm run build-lib && npm run npm-pack"
    }
```
```bash
npm run package
```

Create a new Angular application in separate directory
```bash
ng new workflux-app
```

Install the tarball to use the standalone library in your application. Copy the tarball to the application directory and use its absolute filepath.

```bash
npm install <filepath>
```
#### **`app.module.ts`**
```diff
    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
+   import {ButtonModule, UiModule} from "@flux/ui";
    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';

    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
+       UiModule,
+       ButtonModule
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
```
#### **`app.component.ts`**
```diff
<flux-button></flux-button>
```
