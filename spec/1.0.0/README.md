# Bench Spec

## Data Structure & File Format

Bench files are required to be saved in `UTF-8` encoding, other encodings are forbidden. Allowed formats are: limited to [`JSON`][json] & [`YAML`][yaml].

### File Naming:

Acceptable file name must be one of the following:

- `.bench.yml`
- `.bench.json`
- `.benchrc`

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

---

### version

The version of this spec your bench file follows

---

### jobs

An array that contains at least one [`benchmark`](#benchmark) object.

---

### benchmark

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

---

### command

Used for invoking all command-line programs, taking either a map of configuration values, or, when called in its short-form, a string that will be used as both the `command` and `name`. Run commands are executed using non-login shells by default, so you must explicitly source any dotfiles as part of the command.

```json
{
  "name": "",
  "command": "",
  "background": false,
  "timeout": "",
  "cwd": "",
  "environment": {
    "FOO": "bar"
  }
}
```

name        | required | Type      | default              | Description                                                           
----------- | -------- | --------- | -------------------- | ----------------------------------------------------------------------
name        | ✖        | `String`  | full `command` value | Title of the step to be shown in the UI                               
command     | ✔        | `String`  | `-`                  | Command to run via the shell                                          
background  | ✖        | `Boolean` | `false`              | Whether or not this step should run in the background (default: false)
timeout     | ✖        | `Number`  | `30000`              | Elapsed time in milliseconds the command can run **without output**.  
cwd         | ✖        | `String`  | application root     | In which directory to run this command                                
environment | ✖        | `Map`     | `-`                  | Additional environmental variables, locally scoped to command         

---
