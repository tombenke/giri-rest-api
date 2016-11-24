# giri-rest-api

## About

This is the specification of the giri garden irrigator REST API endpoints.

This repository holds only the definitions of the REST endpoints
which the giri-web backend has to provide to its clients.

This project also acts as a very simple JavaScript module, which makes possible to load the enpoint definitions into your application,
and access to them via other JavaScript modules, as well as access to the mock data files and validation schema files,
or any other kind of content that was put beside the endpoint descriptions.

## Artifacts

The `lib` folder contains:

- the `services` folder, which contains the service definition subfolders,
- the `config.yml` file, which lists the available services via the paths to their folder,
- the `index.js` module definition file.

### Service endpoints

The endpoint definitions can be found under the `lib/services` folder.

The endpoints are described by the `service.yml` file,
and the endpoints can be organized into an arbitrary structure,
but each enpoint should be placed into its own subdirectory.
It means, every enpoint directory must contain one `service.yml` file, and optionally any other file,
that is needed to the documentation or implementation of the service.

Intermediate directories can hold endpoint definition files, not only the leafs of the directory tree.

You can create a new service description manually, or by copying the content of an existing however it is not the
best approach.  Instead of doing manually, use the kickoff tool to generate the descritions 
using template repositores (archetypes) that were created for typical endpoint stereotypes, such as:

- [COLLECTION endpoint archetype](https://github.com/tombenke/rest-endpoint-collection-archetype)
- [RESOURCE endpoint archetype](https://github.com/tombenke/rest-endpoint-resource-archetype)
- [OPERATION endpoint archetype](https://github.com/tombenke/rest-endpoint-operation-archetype)

These archetypes are available on github, but you also can create your own archetypes,
in case the default ones are not satifying.

The following sample demostrates how can you create a new service endpoint for a single RESOURCE entity,
that is called `new_endpoint`:

To create a new endpoint, execute the following command in the root folder othe project:

    $ kickoff -s tombenke/rest-endpoint-resource-archetype -d lib/services/new_endpoint

    You are generating a RESOURCE type REST API endpoint

    ? The name of the endpoint: New Endpoint
    ? The name of the resource: NewEndpoint
    ? The short description of the endpoint: This is an endpoint that provides REST operations to a resource
    ? The complete URI pattern of the endpoint: /new_endpoint
    Converting service.yml as Handlebars template

    Next steps:

     - Add your endpoint to the config.yml of the REST API project

     - Modify the generated endpoint descriptor and the corresponding fixtures and schemas

     - Run the tests in order to verify the correctness of the new endpoint description

        npm test

As you can see on the console log, the `kickoff` utility will ask some data from you, that you have to type in.

Read more about [kickoff](https://github.com/tombenke/kickoff) to learn how to install and use it, and how to create your own archetypes.

As a result, the `kickoff` produces the following content into the `lib/services/new_endpoint` directory:

    $ tree lib/services/new_endpoint/
    
    lib/services/new_endpoint/
    ├── deleteResource-responseBody.json
    ├── getResource-responseBody.json
    ├── putResource-requestBody.json
    ├── putResource-responseBody.json
    └── service.yml

When the endpoint folder created with its initial content, you have to execute the following steps:

1. Add the previously created `new_endpoint` to the `lib/config.yml` file,
   adding the `/new_endpoint` item to the `services:` list.

2. Edit the endpoint descriptor file (`lib/new_endpoint/service.yml`, and modify its default contend according to
   your needs.

3. Optionally put files beside the `service.yml` that might referred by the service descriptor, 
   such as schema validations, mock content, etc.

On the [rest-tool documentation pages](http://tombenke.github.io/rest-tool/docs/documentation.html) you can read more about 
[how to reate and maintain services](http://tombenke.github.io/rest-tool/docs/services.html#create-and-maintain-services).

## Usage

1. Create as many endpoint definitions you need.
2. Run test to check the integrity and validity of the API, via running the:

    npm run test

command.

3. Use the API as a module in other projects (such as the mock server, the frontend REST client layer, etc.).
   `giri-rest-api` will act as any other normal module. You can publish it to the npm store, or directly refer from its github repo.
   You only need to add it to the dependencies of your project, add load it via require(`giri-rest-api`) call.

    "dependencies": {
        // ...
        "giri-rest-api": "1.0.0"
    }

The sample code below demonstrates how to register the endpoint into a web server using seneca.
You find the full code in the [giri-web](https://github.com/tombenke/giri-web) component:

    const restApi = require('giri-rest-api')
    const _ = require('underscore')

    var giriRoutes = []
    _.map(restApi.services.getServices(), service => {
        const uri = service.uriTemplate
        const methods = service.methodList
        _.map(methods, method => {
            const route = {
                method: method.methodName,
                path: uri,
                handler: function(request, reply) {
                    console.log('requested: ', method.methodName, uri);
                    seneca.act({ role: 'web', method: method.methodName, uri: uri, endpoint: service, request: request }, function(err, out) {
                        return replyWrapper(reply, err, out)
                    })
                }
            }
            giriRoutes.push(route)
        })
    })

## todos

- add test
    - Verify that the services properly loaded.
    - Verify that every endpoint definition exist, that are defined in comfig.yml.
    - Verify that the referred mock files and schema files are exist.
    - Verify the integrity of the services (schema).
    - Verify the internal functions working.

- Eliminate `config.yml`.
  Traverse the `services` folder and automatically load the endpoint descriptors, detecting their names.

## References
- [rest-tool documentation pages](http://tombenke.github.io/rest-tool/docs/documentation.html)
- [rest-tool-commons](https://github.com/tombenke/rest-tool-common)
- [kickoff](https://github.com/tombenke/kickoff)
- [COLLECTION endpoint archetype](https://github.com/tombenke/rest-endpoint-collection-archetype)
- [RESOURCE endpoint archetype](https://github.com/tombenke/rest-endpoint-resource-archetype)
- [OPERATION endpoint archetype](https://github.com/tombenke/rest-endpoint-operation-archetype)
