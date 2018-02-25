# Bench Spec

## Data Structure & File Format

Bench files are required to be saved in `UTF-8` encoding, other encodings are forbidden. Allowed formats are: limited to [`JSON`][json] & [`YAML`][yaml].

Summary of Bench object types:

- [version](#version)
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

name        | type     | required | default | description               
----------- | -------- | -------- | ------- | --------------------------
**version** | `Object` | ✔        | `-`     | Schema [Version](#version)
**jobs**    | `Object` | ✖        | `-`     | [Jobs](#jobs)             

### Version

The version of this spec your Bench file uses. Format must follow [semver][].

### Jobs

An array that contains at least one [`benchmark`](#benchmark) object.

### Benchmark

```json
{
  "name": "my awesome benchmark",
  "runs": 100,
  "benchmark": {},
  "before": [],
  "after": []
}
```

name          | type     | required | default | description                                                                              
------------- | -------- | -------- | ------- | -----------------------------------------------------------------------------------------
**name**      | `String` | ✔        | `-`     | Name of the benchmark                                                                    
**runs**      | `Number` | ✖        | `100`   | number of invocations of the `benchmark`                                                 
**benchmark** | `Object` | ✔        | `-`     | a [`command`](#command) object to run as the benchmark                                   
**before**    | `Array`  | ✖        | `[]`    | an array of [`command`](#command) objects to run before executing the `benchmark` command
**after**     | `Array`  | ✖        | `[]`    | an array of [`command`](#command) objects to run after executing the `benchmark` command 

### Command

Used for invoking all command-line programs, taking either a map of configuration values, or, when called in its short-form, a string that will be used as both the `command` and `name`. Run commands are executed using non-login shells by default, so you must explicitly source any dotfiles as part of the command.

```json
{
  "name": "performance test",
  "command": "node bench/performance.js",
  "background": false,
  "timeout": 30000,
  "cwd": "/app",
  "environment": {
    "NODE_ENV": "production"
  }
}
```

name        | Type      | required | default          | Description                                                           
----------- | --------- | -------- | ---------------- | ----------------------------------------------------------------------
name        | `String`  | ✖        | `command` value  | Title of the step to be shown in the UI                               
command     | `String`  | ✔        | `-`              | Command to run via the shell                                          
background  | `Boolean` | ✖        | `false`          | Whether or not this step should run in the background (default: false)
timeout     | `Number`  | ✖        | `30000`          | Elapsed time in milliseconds the command can run **without output**.  
cwd         | `String`  | ✖        | application root | In which directory to run this command                                
environment | `Map`     | ✖        | `-`              | Additional environmental variables, locally scoped to command         

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
>
>   - name: database migration
>     runs: 100
>     benchmark: node bench/migrate.js
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
