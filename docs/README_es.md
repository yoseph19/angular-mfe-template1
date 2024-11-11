# 🌐 Plantilla de Inicio para Micro Frontends en Angular (Monorepo Modular con Carpeta de Proyectos)

Bienvenido a esta guía educativa sobre cómo configurar un monorepo modular con Micro Frontends en Angular utilizando Native Federation. Esta guía te llevará a través de cada paso, facilitando la creación de una arquitectura de aplicación modular en Angular.

# 📋 Requisitos Previos
- **Node.js** y **npm** instalados
- **Angular 18.2** instalado (puedes verificar tu versión con `ng version`)
- Comprensión básica de **Angular** y de la **arquitectura de micro-frontend**

# 🚀 Empezando
## Paso 1: Crear el Workspace de Angular

Inicializa un nuevo workspace de Angular sin crear una aplicación predeterminada:

> ng new angular-mfe-template1 --create-application=false

Este comando configura un workspace limpio llamado `angular-mfe-template1`, permitiéndonos agregar múltiples proyectos de Angular dentro del mismo workspace para una estructura modular.

## Paso 2: Generar las aplicaciones shell y mfe1

Crea dos aplicaciones, **shell** y **mfe1**, con prefijos únicos para evitar conflictos de componentes:

> ng generate application shell --prefix app-shell
> ng generate application mfe1 --prefix app-mfe1

- `shell`: La aplicación principal (host) que cargará las aplicaciones micro-frontend.
- `mfe1`: Un micro-frontend independiente que se integrará en la aplicación shell.

## Paso 3: Instalar la Dependencia de Native Federation

Instala el paquete `@angular-architects/native-federation` como una dependencia de desarrollo:

> npm i -D @angular-architects/native-federation

Esta biblioteca proporciona herramientas para implementar Native Federation en Angular, permitiendo que `mfe1` se integre como un micro-frontend en `shell`.

## Paso 4: Inicializar `shell` como un Host Dinámico

> ng g @angular-architects/native-federation:init --project shell --port 4200 --type dynamic-host

Esto configura Module Federation en `shell`, configurándolo en el puerto `4200` como un host.

## Paso 5: Inicializar `mfe1` como un Remote

Configura `mfe1` como un remote que será cargado por el host `shell`:

> ng g @angular-architects/native-federation:init --project mfe1 --port 4201 --type remote

Esto configura Module Federation en `mfe1`, estableciéndolo en el puerto `4201` como un remote que será cargado por `shell`.

## Paso 6: Actualizar la Ruta de `federation.manifest.json` en el Host

En `shell`, ve a la carpeta `public` y abre `federation.manifest.json`. Actualiza la ruta para `mfe1` al puerto correcto:

```json
{
    "mfe1": "http://localhost:4201/remoteEntry.json"
}
```

## Paso 7: Modificar `main.ts` en `shell` para Cargar el Manifiesto de Federación

En `shell/src/main.ts`, actualiza la ruta a `federation.manifest.json`:

```typescript
import { initFederation } from '@angular-architects/native-federation';

initFederation('federation.manifest.json')
  .catch(err => console.error(err))
  .then(_ => import('./bootstrap'))
  .catch(err => console.error(err));
```
Esto elimina el prefijo `public/`, ya que el contenido de `public` ya está disponible en el nivel raíz al servir la aplicación.

## Paso 8: Ejecutar las Aplicaciones `shell` y `mfe1`

En la carpeta raíz `angular-mfe-template1`, ejecuta cada aplicación en terminales separadas:

```bash	
ng serve shell
```
and
```bash	
ng serve mfe1
```
Esto iniciará `shell` en `http://localhost:4200` y `mfe1` en `http://localhost:4201`. Mantén ambas aplicaciones en ejecución para que `shell` cargue `mfe1` como un micro-frontend.

## Paso 9: Crear el `HomeComponent` en `shell`

Genera un `HomeComponent` en `shell` para servir como la página principal:

```bash
ng g c pages/home --project shell
``` 
Este componente mostrará el contenido principal de `shell` y proporcionará opciones de navegación para cargar `mfe1`.

## Paso 10: Configurar el Enrutamiento en `shell`

Abre `shell/src/app/app.routes.ts` y agrega las siguientes rutas:

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

- `path: ' '`: Carga `HomeComponent` como la página principal.
- `path: 'mfe1'`: Carga dinámicamente el `AppComponent` desde `mfe1`.
- `path: '**'`: Redirige cualquier ruta no definida a `HomeComponent`.

## 🔍 Verificación Final

Después de iniciar ambas aplicaciones, abre las siguientes URLs para asegurarte de que todo funciona correctamente:

**Página principal de** `shell`: `http://localhost:4200/` — Debería mostrar `HomeComponent` de `shell`.

**Micro-frontend** `mfe1`: `http://localhost:4200/mfe1` — Debería cargar `AppComponent` de `mfe1` dentro de `shell`.

Si ambas rutas funcionan como se espera, ¡tu configuración de micro-frontends en Angular con Native Federation está completa! 🎉

