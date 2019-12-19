# The symptom

$refs are not being rendered correctly when generating the output file. 

Command used to output the file:

```sh
$ npx speccy resolve -v index.yaml -o output.yaml
```

# Expected outcome

That every $ref will have its content rendered to the output.yaml file.

# Details

```
├── README.md
├── index.yaml
├── output.yaml
├── shared
│   └── schemas
│       └── error.yaml
└── v1
    ├── books
    │   └── index.yaml
    └── pets
        └── index.yaml
```

I have a single definition for error messages in the ```shared/schemas/error.yaml``` file.

For the resources ```books``` and ```pets``` I'd like to reuse that when describing error responses as you can see in ```/v1/books/index.yaml``` and ```/v1/pets/index.yaml``` files.

When I render the output file, the first occurence of the $ref to the ```shared/schemas/error.yaml``` is merged correctly, but the following references don't.

They get merged, for all other reference to```shared/schemas/error.yaml``` like this:

```yaml
$ref: "#/paths/~1v1~1books/get/responses/401/content/application~1json/schema"
```

The base path ```#/paths/~1v1~1books```, method ```get``` and status code ```401``` are all comming from the first $ref that was rendered correctly.

That creates outputs like this:

```yaml
  /v1/pets:
    get:
      responses:
        "200":
          description: success
        "500":
          description: Invalid request
          content:
            application/json:
              schema:
                $ref: "#/paths/~1v1~1books/get/responses/401/content/application~1j\
                  son/schema"
```

Where, the path is ```/v1/pets```, but the ```$ref``` for the ```500``` status code is referencing the ```/v1/books``` path and the ```401``` status code.


**Is there anything I'm doing wrong or is it a bug in Speccy?**
