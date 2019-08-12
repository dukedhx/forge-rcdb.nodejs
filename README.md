
## Description

This is Forge Responsive Connected Database: A responsive React-based web application that showcases the use of Autodesk Forge Viewer and Forge web services, working in a connected environment with integrated data from multiple databases.

** If you are only interested in the extensions/plugins alone, see [library-javascript-viewer-extensions](https://github.com/Autodesk-Forge/library-javascript-viewer-extensions) **

## Live Demo

[https://maximo-poc.azurewebsites.net](https://maximo-poc.azurewebsites.net)

## Prerequisites

To run these samples, you need your own Forge API credentials:

 * Visit the [Forge Developer Portal](https://developer.autodesk.com), sign up for an account
 * [Create a new App](https://developer.autodesk.com/myapps/create)
 * For this new App, you can use <b>http://localhost:3000/api/forge/callback/oauth</b> as Callback URL.
 * Take note of the <b>Client ID</b> and <b>Client Secret</b>, those are your API keys that must remain hidden
 * Install [Python ^2.7](https://www.python.org/downloads/release/python-2714/) and [NodeJS](https://nodejs.org) and make sure its version is between 8.x (LTS) to 10.x (LTS).

## Project Setup

### Download/Clone the Project

   * `git clone <ProjectURL> --single-branch --branch remastered`

### Environment Setup

   //TODO: fill out ./config jsons

### MongoDB database

   //TODO: database configuration using scripts in `resources`


### Client and Server

   In **development**, the client is dynamically built by the
   [webpack-dev-middleware](https://github.com/webpack/webpack-dev-middleware), so just run:

    * `npm install`    *(downloads project dependencies locally)*

    * `npm run dev` or `node -r @babel/register ./src/server/index.js`     *(builds/runs server on the fly with or w/o monitoring code changes)*

    * `npm run serve`    *(builds/runs client on the fly and monitors code changes)*

    * open [http://localhost:8080](http://localhost:8080) in your favorite browser


   In **production**, the client requires a build step, so run:

    * `npm install` *(not required if you already run at previous step)*

    * `npm run build` *(builds client)*

    * `npm start` *(run the server)*

    * open [http://localhost:3000](http://localhost:3000) in your favorite browser

### References

- Model Schema:

```json
    {
      "_id" : "mongoDB Id",
      "urn" : "model URN",
      "name" : "Model name",
      "path" : "...path of local svf for dev env ...",
      "env" : "AutodeskProduction" || "Local",
      "materialCategories": ["Material"],
      "sequence" : [],
      "states" : [],
      "options" : {
      //extensions options
      },
      "thumbnail" : "... base64 encoded thumbnail ... "
    }
```

- Material Schema:

```json
    {
      "_id" : ObjectId("57ee6b26dfda94c109157449"),
      "name" : "Steel",
      "supplier" : "Autodesk",
      "currency" : "USD",
      "price" : 2.5
    }
```

An export of my database records is provided in `/resources/db`

## Cloud Deployment

It may be a bit tricky to deploy that sample in the Cloud because it requires two steps in order to be run:

 * 1/ You need to translate at least one model using your own Forge credentials, so it can be loaded by your app. In order to do that take a look at the [step-by-step tutorial](https://developer.autodesk.com/en/docs/model-derivative/v2/tutorials/prepare-file-for-viewer/) from the [Model Derivative API](https://developer.autodesk.com/en/docs/model-derivative/v2/overview/) to understand exactly what this is about.

If you want to skip that manual process you can use one of our live sample: [https://models.autodesk.io](https://models.autodesk.io). This app lets you put your credentials and translate a model on your behalf. Another option would be to deploy to heroku the [Forge boiler plate sample #5](https://github.com/Autodesk-Forge/forge-boilers.nodejs). Make sure you deploy Project #5. This set up is more straightforward since it doesn't require any Cloud database or pre-translated models. It will let you upload, translate and manage further models as well.

Once you have translated at least one model, take note of its URN, that's the base64 encoded objectId. You also need a model which has some "Material" properties to be compatible with forge-rcdb because it is expecting components with that property. You can use Engine.dwf placed in the [resource/models](https://github.com/Autodesk-Forge/forge-rcdb.nodejs/tree/master/resources/models) directory of this project.

 * 2/ You need valid credentials to a MongoDB Cloud database that holds materials and models records. I suggest [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) or [Compose MongoDB](https://elements.heroku.com/addons/mongohq). With Atlas you can set up an account for free, that's way enough for running multiple samples. Creating the database, with one user, will give you the credentials you need: dbname, dbhost, user, password and dbport.
 **Important:** your database name needs to be "forge-rcdb", it is currently hardcoded in the sample, or you need to change that accordingly in the project config.

With those credentials you can use a tool such as [http://3t.io/mongochef](http://3t.io/mongochef) a mongoDB GUI which lets you easily connect remotely to the database from your machine in order to administrate it. With Mongochef you can easily import the two sample collections I placed in the [resources/db directory](https://github.com/Autodesk-Forge/forge-rcdb.nodejs/tree/master/resources/db)

Import the two collections as 'rcdb.materials' and 'rcdb.models', then edit rcdb.model to replace the URN of your custom translated Engine.dwf model from step 1/

You should be ready to deploy to heroku, providing the same Forge credentials used to translate the model and valid credentials of the database when prompted for the environment settings.

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)


## Tips and Tricks

> What should I do if I get the error `Module build failed: Error: Node Sass does not yet support your current environment: OS X 64-bit with Unsupported runtime (64)` when I tried to build the front end?

Rebuild `node-ass` with the command `npm rebuild node-sass` and build to production again.

> Error: ENOENT: no such file or directory, scandir 'F:\Projects\forge-rcdb\node_modules\node-sass\vendor'

Run `node ./node_modules/node-sass/scripts/install.js`

> Various errors when building modules with 'node-gyp' on Windows

Install Python ^2.7 and the build tools with `npm install --global --production windows-build-tools`

> Starting the application in production hangs at "Cleaning Dir"?

The log output is misleading - actually it's your MongoDB Atlas Cluster taking time to pin up.

> How should I migrate from mLab to MongoDB Atlas?

See [here](https://docs.mongodb.com/guides/cloud/migrate-from-mlab/) for their official guide. Also pull the latest changes from forge-rcdb and follow the instructions to set up your connection strings.

> What connection string should I use for my MongoDB instance?

If you are using MongoDB version earlier than 3.4 (default version as this sample), use the sharded schema: `mongodb://<username>:<password>@cluster0-shard-00-00-u9dtd.mongodb.net:27017,cluster0-shard-00-01-u9dtd.mongodb.net:27017,cluster0-shard-00-02-u9dtd.mongodb.net:27017/test?ssl=true&replicaSet=Cluster0-shard-0&authSource=admin&retryWrites=true`

Otherwise go with the service schema which is much simpler: `mongodb+srv://<username>:<password>@cluster0-u9dtd.mongodb.net/test?retryWrites=true`

See [here](https://docs.mongodb.com/manual/reference/connection-string/) for details on connection string schema.

## Written by

Forge Partner Development - [http://forge.autodesk.com](http://forge.autodesk.com)
