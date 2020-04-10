# Notes

My notes on testing using schemathesis.

## Builder

1. Ran `npm install`.
1. Created and activated a python virtual environment using `virtualenv`.
1. Determined that the run command was `node index.js` using the Heroku Procfile.
1. Determined what the port was by reading the source code, which makes the [default port `3000` in `index.js`](./index.js). This is also printed to the console when the app starts.
1. Found an [OpenAPI spec](https://ghibliapi.herokuapp.com/swagger.yaml) on their [website](https://ghibliapi.herokuapp.com/). Downloaded this and checked it into [index.yml](./index.yml).
1. Ran `pip install schemathesis`.
1. Changed https://ghibliapi.herokuapp.com to http://localhost:3000 in the openapi spec.
1. Ran `schemathesis 
run --base-url http://localhost:3000 .\index.yml` and got an error: `Error: jsonschema.exceptions.ValidationError: Invalid schema`.
1. Used swaggerhub to resolve several issues in the yaml and convert to OpenAPI 3.0, as Swagger broke `schemathesis` for some reason (even though it claims to support swagger).
1. This allowed `schemathesis` to run succesfully.

## Runner

```bash
schemathesis 
run --base-url http://localhost:3000 .\index.yml
=================== Schemathesis test session starts ===================
platform Windows -- Python 3.7.3, schemathesis-1.1.0, hypothesis-5.8.0, 
hypothesis_jsonschema-0.12.0, jsonschema-3.2.0
rootdir: C:\Users\MikeSolomon\devel\test-apis\ghibliapi
hypothesis profile 'default' -> database=DirectoryBasedExampleDatabase('C:\\Users\\MikeSolomon\\devel\\test-apis\\ghibliapi\\.hypothesis\\examples')
Schema location: file:///C:/Users/MikeSolomon/devel/test-apis/ghibliapi/index.yml
Base URL: http://localhost:3000
Specification version: Open API 3.0.0
Workers: 1
collected endpoints: 10

GET /films F                                                     [ 10%]
GET /films/{id} .                                                [ 20%]
GET /people F                                                    [ 30%]
GET /people/{id} .                                               [ 40%]
GET /locations F                                                 [ 50%]
GET /locations/{id} .                                            [ 60%]
GET /species F                                                   [ 70%]
GET /species/{id} .                                              [ 80%]
GET /vehicles F                                                  [ 90%]
GET /vehicles/{id} .                                             [100%]

=============================== FAILURES ===============================
_____________________________ GET: /films ______________________________
1. Received a response with 5xx status code: 500

Check           : not_a_server_error
Query           : {'limit': -1}

Run this Python code to reproduce this failure: 

    requests.get('http://localhost:3000/films', params={'limit': -1})   

Or add this option to your command line parameters: --hypothesis-seed=310927823299942249682541552854878198140
_____________________________ GET: /people _____________________________
1. Received a response with 5xx status code: 500

Check           : not_a_server_error
Query           : {'limit': -1}

Run this Python code to reproduce this failure: 

    requests.get('http://localhost:3000/people', params={'limit': -1})  

Or add this option to your command line parameters: --hypothesis-seed=218508986312951396327504817342200596206
___________________________ GET: /locations ____________________________
1. Received a response with 5xx status code: 500

Check           : not_a_server_error
Query           : {'limit': -1}

Run this Python code to reproduce this failure: 

    requests.get('http://localhost:3000/locations', params={'limit': -1})

Or add this option to your command line parameters: --hypothesis-seed=223180699499852924637492896811273566514
____________________________ GET: /species _____________________________
1. Received a response with 5xx status code: 500

Check           : not_a_server_error
Query           : {'limit': -1}

Run this Python code to reproduce this failure: 

    requests.get('http://localhost:3000/species', params={'limit': -1}) 

Or add this option to your command line parameters: --hypothesis-seed=257588204810464894292569481949039753615
____________________________ GET: /vehicles ____________________________
1. Received a response with 5xx status code: 500

Check           : not_a_server_error
Query           : {'limit': -1}

Run this Python code to reproduce this failure: 

    requests.get('http://localhost:3000/vehicles', params={'limit': -1})

Or add this option to your command line parameters: --hypothesis-seed=100707149409218263343769199340182530891
=============================== SUMMARY ================================

not_a_server_error            568 / 631 passed          FAILED

===================== 5 passed, 5 failed in 11.86s =====================
```

This is a legitimate bug in the ghibliapi. When `-1` is used as a limit it crashes the app.