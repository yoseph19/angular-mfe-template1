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