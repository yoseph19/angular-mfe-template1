# 🌐 Starter Template for Angular Micro Frontends (Modular Monorepo with Projects Folder)
Welcome to this educational guide on setting up a modular monorepo with Angular Micro Frontends using Native Federation. This guide will take you through each step, making it easy to create a modular application architecture in Angular.

# 📋 Prerequisites

- **Node.js** and **npm** installed
- **Angular 18.2** installed (you can check your version with `ng version`)
- Basic understanding of **Angular** and **micro-frontend architecture**

# 🚀 Getting Started
## Step 1: Create the Angular Workspace
Initialize a new Angular workspace without creating a default application:

> ng new angular-mfe-template1 --create-application=false

This command sets up a clean workspace named `angular-mfe-template1`, allowing us to add multiple Angular projects within the same workspace for a modular structure.

## Step 2: Generate Applications shell and mfe1
Create two applications, `shell` and `mfe1`, with unique prefixes to avoid component conflicts:

> ng generate application shell --prefix app-shell
> ng generate application mfe1 --prefix app-mfe1

- `shell`: The main (host) application that will load the micro-frontend applications.
- `mfe1`: An independent micro-frontend that will be integrated into the shell application.

## Step 3: Install Native Federation Dependency
Install the ` @angular-architects/native-federation `  package as a development dependency:

> npm i -D @angular-architects/native-federation

This library provides tools to implement Native Federation in Angular, enabling `mfe1` to be integrated as a micro-frontend in `shell`.

## Step 4: Initialize the `shell` as a Dynamic Host
> ng g @angular-architects/native-federation:init --project shell --port 4200 --type dynamic-host

This configures Module Federation in `shell`, setting it up on port `4200` as a host.

## Step 5: Initialize the `mfe1` as a Remote
Set up `mfe1` as a remote that will be loaded by the `shell` host:
> ng g @angular-architects/native-federation:init --project mfe1 --port 4201 --type remote

This configures Module Federation in `mfe1`, setting it on port `4201` as a remote to be loaded by `shell`.

## Step 6: Update `federation.manifest.json` Path in the Host
In `shell`, go to the `public` folder and open `federation.manifest.json`. Update the path for `mfe1` to the correct port:

```json
{
    "mfe1": "http://localhost:4201/remoteEntry.json"
}
```
## Step 7: Modify `main.ts` in `shell` to Load Federation Manifest

In `shell/src/main.ts`, update the path to `federation.manifest.json`:

```typescript
import { initFederation } from '@angular-architects/native-federation';

initFederation('federation.manifest.json')
  .catch(err => console.error(err))
  .then(_ => import('./bootstrap'))
  .catch(err => console.error(err));
```
This removes the `public/` prefix, as the contents of `public` are already available at the root level when serving the application.

## Step 8: Run `shell` and `mfe1` Applications
In the root folder `angular-mfe-template1`, run each application in separate terminals:
```bash	
ng serve shell
```
and
```bash	
ng serve mfe1
```
This will start shell on `http://localhost:4200` and `mfe1` on `http://localhost:4201`. Keep both applications running for `shell` to load `mfe1` as a micro-frontend.

## Step 9: Create `HomeComponent` in `shell`
Generate a `HomeComponent` in `shell` to serve as the main page:
```bash
ng g c pages/home --project shell
``` 
This component will display the main content of `shell` and provide navigation options for loading `mfe1`.

## Step 10: Configure Routing in `shell`
Open `shell/src/app/app.routes.ts` and add the following routes:
```typescript
import { Routes } from '@angular/router';
import { loadRemoteModule } from '@angular-architects/native-federation';
import { HomeComponent } from './pages/home/home.component';

export const routes: Routes = [
    { path: '', component: HomeComponent },
    {
        path: 'mfe1',
        loadComponent: () =>
            loadRemoteModule('mfe1', './Component').then((m) => m.AppComponent),
    },
    {
        path: '**',
        component: HomeComponent,
    },
];
```
- `path: ' '`: Loads `HomeComponent` as the main page.
- `path: 'mfe1'`: Dynamically loads the `AppComponent` from `mfe1`.
- `path: '**'`: Redirects any undefined routes to HomeComponent.

# 🔍 Final Verification
After starting both applications, open the following URLs to ensure everything is working correctly:

**Main page of** `shell`: `http://localhost:4200/` — Should display `HomeComponent` from `shell`.
**Micro-frontend** `mfe1`: `http://localhost:4200/mfe1` — Should load `AppComponent` from `mfe1` within `shell`.

If both routes work as expected, your Angular micro-frontend setup with Native Federation is complete! 🎉