# list-editor-component
quasar app extension which gives you a crud on a simple list

#Usage of the app-extension

## Installation 

## Usage


# Development of the app extension
For developping this component there is an example application in the git repo (not distributed with the npm). To develop locally the npm package without distributing it.

Add the component as extension to the testapp :
Change in the testapp Directory
````
yarn add --dev file:///<Path to your component not relative>
````
for example :`

````
yarn add --dev file:////Users/sc/dev/list-editor-component
````
register your component in your application :

````
quasar ext invoke list-editor-component
````

start the server :
````
quasar dev
`````

Everytime you change something in the  component, you have to yarn add again and restart your server...... If somebody knows a better solution let me know.


