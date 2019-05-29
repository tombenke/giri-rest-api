giri-rest-api
=============

## About

This is the specification of the giri garden irrigator REST API endpoints.

This repository holds only the definitions of the REST endpoints
which the giri-web backend has to provide to its clients.

## Artifacts

The `rest-api` folder contains:

- the `api.yml` The root swagger file that refers to the other partials of the API description.
- the `docs/swagger.json`, that can be generated by the `npm run swagger` command, and contains the whole API specification in one file.
- the service endpoint specifications, placed into subfolders.

The API descriptors can be either JSON or YAMl format files, or a mixture of them.

See the [humar-friendly format API docs](https://tombenke.github.io/giri-rest-api/) in two different formats.

### Service endpoints

Each endpoint definition is placed into a separate folder, accompanied with its request and response schemas and examples.

The endpoints are described by the `endpoint.yml` file, and the endpoints can be organized into an arbitrary structure, but each enpoint should be placed into its own subdirectory.
It means, every endpoint directory must contain one `endpoint.yml` file, and optionally any other file, that is needed to the documentation or implementation of the service.

Intermediate directories can hold endpoint definition files, not only the leafs of the directory tree.

## Usage

1. Create as many endpoint definitions you need.
2. Run the following command to check the integrity and validity of the API:

```bash
    npm run swagger
```

You can import this module as a node dependency of a JavaScript project, or simply clone it from the github repository, and use with any kind of tool that is able to consume OpenAPI/Swagger format API description.

