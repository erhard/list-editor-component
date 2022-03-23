# list-editor-component

quasar app extension which gives you a crud on a simple list

## Usage of the app-extension

### Installation 

quasar ext add list-editor-component


### Usage

Look at testapp/pages/Index.vue this is an implementation blueprint.

The component is a dump component. You put in a data array and you get events for create update and delete. To do this actions is the task of the calling component (for example via a store).
Be aware to have a field __uuid in your data. This is necessary to give you an anchor for udate or deletion.




# Development of the app extension
For developping this component there is an example application (testapp) in the git repo (not distributed with the npm). To develop locally the npm package without distributing it.

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



