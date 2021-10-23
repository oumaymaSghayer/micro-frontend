# AngularMfeExample

This project was achieved based on this [article](https://dev.to/sbhuvane/micro-frontend-in-angular-using-module-federation-31om)

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

## Create MicroFrontend Application

1.Run `ng g application mfe1 --routing --style=css`

2.Run `ng g c home --project=mfe1` to generate a new home component inside our mfe1 application.

3.Create a new feature module under mfe1: `ng g m mfefeature --routing --project=mfe1`

4.Create a mfefeature component under mfe1: `ng g c mfefeature --project=mfe1`

5.Add the route to the mfefeature component in the mfefeature-routing.module.ts :

```
{
    path: '',
    component: MfefeatureComponent,
  },

```

6.Update routing module to add path to home component under mfe1:

```
const routes: Routes = [
  {
    path: '',
    component: HomeComponent,
    pathMatch: 'full',
  },
  {
    path: 'mfe1',
    loadChildren: () =>
      import('./mfefeature/mfefeature.module').then((m) => m.MfefeatureModule),
  },
];
```

7.Update home.component.html inside mfe1 :

```
<p>home in mfe1 works!</p>

```

8.Update app.component.html to include links to home and mfe1:

```
<h1>MFE1 LANDING PAGE</h1>
<a routerLink="/">Home </a>
<a routerLink="/mfe1">MFE1</a>
<router-outlet></router-outlet>
```

9.Run the application `ng serve mfe1`

## Add Module Federation

1.Add @angular-architects/module-federation package to both the projects:

`ng add @angular-architects/module-federation --project host --port 4200`

`ng add @angular-architects/module-federation --project mfe1 --port 5000`

The above command creates web pack config files and updates angular.json.

## Webpack Config changes

1. Add webpack5 to the workspace. Add the below entry to package.json under private entry:

```
"resolutions": {
    "webpack": "^5.4.0",
    "license-webpack-plugin": "2.3.17"
  },
```

2. Run `yarn install` to add license-webpack-plugin@2.3.17 as Angular11 uses 2.3.11 version which gives an error when used with webpack5.

3. Add Modulefederated plugin to mfe1.

Locate webpack.config.js under mfe1 project and uncomment the config values under // For remotes (please adjust)

Make the following changes

```
name: "mfe1",
filename: "mfe1remoteEntry.js",
exposes: {
    './MfefeatureModule': './projects/mfe1/src/app/mfefeature/mfefeature.module.ts',
        },
```

4. Add Modulefederated plugin to host

Locate webpack.config.js under host project and uncomment the lines under // For hosts (please adjust)

Make the following changes

```
remotes: {
     "mfe1": "mfe1@http://localhost:5000/mfe1remoteEntry.js",
},

```

## Route changes in Host

1. Add path to mfe1 and lazy load the mfe feature module in host's app-routing.module.ts.

```
{
    path: 'mfe1',
    loadChildren: () =>
      import('mfe1/MfefeatureModule').then((m) => {
        return m.MfefeatureModule;
      }),
  }

```

2. Declare MfeFeatureModule

The path mfe1/MfeFeatureModule mentioned in the import statement does not "exist" within host project. When we compile the host project it will throw an error.

To fix the error, we will create decl.d.ts under host and declare the module

`declare module 'mfe1/MfefeatureModule'`

3. In app.component.html under host, make the following changes

```
<h1>Angular MFE Host</h1>
<a routerLink='/'>Main</a> &#160;
<a routerLink='/mfe1'>Link to MFE</a>
<router-outlet></router-outlet>
```

## Run applications :

1. Run `ng serve host`

2. Run `ng serve mfe1`
