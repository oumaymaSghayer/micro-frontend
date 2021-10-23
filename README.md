# AngularMfeExample

This project was achieved based on this article: https://dev.to/sbhuvane/micro-frontend-in-angular-using-module-federation-31om

## Pre-Requisites:

    1.Angular CLI: 11.2.8
    2.Node: 15.4.0
    3.Yarn: 1.22.10

We will be using yarn as package manager instead of NPM. Why? We will be using Webpack 5 Module Federation with Angular 11. Angular CLI 11 uses webpack version 4. We will be overriding the webpack version in package.json and yarn is required to override the web pack version for angular cli.

Set Yarn as package manager usinr `ng config cli.packageManager yarn `

## Create Workspace

Run `ng new angular-mfe-example --createApplication="false"`

The above command will create a workspace with no projects.

## Create Host Application

1.Run `ng g applicatie host --routing --style=css`

The above command will create a host application.

2.Run `ng g c home --project=host` to generate a new home component inside our host application.

3.Add a Route to app-routing.module.ts for HomeComponent :

```
{
    path: '',
    component: HomeComponent,
    pathMatch: 'full',
  },

```

4.Add router-outlet to app.component.html :

```
<h1>HOST</h1>
<router-outlet></router-outlet>
```

5.Run the application `ng serve host`

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI Overview and Command Reference](https://angular.io/cli) page.
