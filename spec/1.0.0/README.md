# Bench Spec

## Data Structure & File Format

Bench files are required to be saved in `UTF-8` encoding, other encodings are forbidden. Allowed formats are limited to [`JSON`][json] & [`YAML`][yaml].

Summary of Bench object types:

- [bench.json](#benchjson)
  - [jobs](#jobs)
    - [name](#jobs)
    - [runs](#jobs)
    - [benchmark](#command)
    - [before](#command)
    - [after](#command)

### File Naming

Acceptable file name must match the following pattern:

```regex
\.?bench(?:rc)?\.?(?:json|yaml|yml)?
```

###### Example

- [`.benchrc`](#json-example)
- [`bench.json`](#json-example)
- [`.bench.yml`](#yaml-example)

---

## `bench.json`

```json
{
  "version": "1.0.0",
  "jobs": []
}
```

name        | type     | required | default | description                                
----------- | -------- | -------- | ------- | -------------------------------------------
**version** | `String` | ✔        | `-`     | Spec version. Format must follow [semver][]
**jobs**    | `Object` | ✖        | `-`     | [Jobs](#jobs)                              

### Jobs

An array that contains at least one [`job`](#job) object.

### Job

```json
{
  "name": "my awesome benchmark",
  "runs": 100,
  "benchmark": {},
  "before": [],
  "after": [],
  "environment": [
    "node:8-alpine",
    "node:8-slim"
  ],
}
```

name            | type      | required | default | description                                                                              
--------------- | --------- | -------- | ------- | -----------------------------------------------------------------------------------------
**name**        | `String`  | ✔        | `-`     | Name of the Job                                                                          
**runs**        | `Integer` | ✖        | `100`   | number of invocations for the `benchmark`                                                
**benchmark**   | `Object`  | ✔        | `-`     | a [`command`](#command) object to run as the benchmark                                   
**before**      | `Array`   | ✖        | `[]`    | an array of [`command`](#command) objects to run before executing the `benchmark` command
**after**       | `Array`   | ✖        | `[]`    | an array of [`command`](#command) objects to run after executing the `benchmark` command 
**environment** | `Array`   | ✖        | `[]`    | [Environment][environments-schema] Name to run this job in                               

###### Short Form

You can also use short-form for the following properties

name            | type     | required | default | description                                                                
--------------- | -------- | -------- | ------- | ---------------------------------------------------------------------------
**benchmark**   | `String` | ✔        | `-`     | a single [`command`](#command) string to run as the benchmark via the shell
**environment** | `String` | ✖        | `[]`    | a single [Environment][environments-schema] Identifier to run this job in  

### Command

Used for invoking all command-line programs, taking either a map of configuration values, or, when called in its short-form, a string that will be used as both the `command` and `name`. Run commands are executed using non-login shells by default, so you must explicitly source any dotfiles as part of the command.

```json
{
  "name": "performance test",
  "command": "node bench/performance.js",
  "background": false,
  "timeout": 30000,
  "cwd": "/app",
  "env": {
    "NODE_ENV": "production"
  }
}
```

name           | type      | required | default          | description                                                         
-------------- | --------- | -------- | ---------------- | --------------------------------------------------------------------
**name**       | `String`  | ✖        | `command` value  | Title of the step to be shown in the UI                             
**command**    | `String`  | ✔        | `-`              | Command to run via the shell                                        
**background** | `Boolean` | ✖        | `false`          | Whether or not this step should run in the background               
**timeout**    | `Integer` | ✖        | `30000`          | Elapsed time in milliseconds the command can run **without output**.
**cwd**        | `String`  | ✖        | application root | In which directory to run this command                              
**env**        | `Map`     | ✖        | `-`              | Environmental variables, locally scoped to the command              

---

###### JSON Example

> ```json
> {
>   "version": "1.0.0",
>   "jobs": [
>     {
>       "name": "response speed",
>       "runs": 10000,
>       "benchmark": "node bench/response.js"
>     },
>     {
>       "name": "database migration",
>       "runs": 100,
>       "benchmark": "node bench/migrate.js",
>       "before": [
>         {
>           "name": "prep",
>           "command": "node bench/helpers/reset-db.js"
>         }
>       ],
>       "after": [
>         {
>           "name": "cleanup",
>           "command": "node bench/helpers/reset-db.js",
>           "background": true
>         }
>       ]
>     }
>   ]
> }
> ```

###### YAML Example

> ```yml
> version: 1.0.0
>
> jobs:
>   - name: response speed
>     runs: 10000
>     benchmark: node bench/response.js
>     environment:
>       - node:9
>       - node:8
>       - node:6
>
>   - name: database migration
>     runs: 100
>     benchmark: node bench/migrate.js
>     environment: node:9
>     before:
>       - name: prep
>         command: node bench/helpers/reset-db.js
>     after:
>       - name: cleanup
>         command: node bench/helpers/cleanup.js
>         background: true
> ```

[json]: https://www.json.org/
[semver]: https://semver.org
[yaml]: http://www.yaml.org/
[environments-schema]: https://github.com/BenchCI/environments
