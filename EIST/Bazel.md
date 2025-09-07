>[!danger] **DO NOT FORGET BAZEL SETUP**
>add `common --enable_bzlmod` in ` .bazelrc` 

# Python (Server side)
#bazelpython #python 
- **pip.parse** rule does not have a name attribute but a ==**hub_name**==
- any directory with a **BUILD** file is a package in Bazel
- <:> can only be used in the following syntax `[Package]:[Target]` 
	- that is why it cannot be used for something like relative paths `src:[target]` with src sub-directory in current package
- <//> for absolute paths
- `</>` for relative paths but do not start with / or . for references

>[!warning] **Name for ` pip.parse ` is ` hub_name` **
## Requirements
#pip_parse
```python
# This is actually bazel not python

#This should be there by default but in case it isn't
pip = use_extension("@rules_python//python/extensions:pip.bzl", "pip")
# end of |

pip.parse(

python_version = "3.8",

# TODO: Add target name and the path to the requirements.txt file.

hub_name = "pip_deps_server", #need hub_name

requirements_lock = "//server-side:requirements_lock.txt", # need : not /

)
``` 
>[!info] **Requirements_lock**
>the setup for the requirements lock should be provided in the **MODULE.bazel**
>*use ":" for requirements_lock.txt as it is considered a Target*
## py_binary example
#py_binary
```python
#Should be provided
load("@rules_python//python:defs.bzl", "py_binary")
#end of |
  

# TODO: Add a py_binary target with the name 'token_generator_microservice'

py_binary(

name = "token_generator_microservice",

srcs = ["src/token_generator_microservice.py"],

deps = ["@pip_deps_server//flask"], # important reference to specified UP

main = "src/token_generator_microservice.py",

)
```


# Java 
## Client side
- **java_library()** does not need a main function
- **java_binary** needs a **main_class** attribute defined as the <something.java> file that contains the entry point main
- <something.java> files needed for this target go in the sources section
- bazel target ( including libraries in or out of the current package) go in the dependencies section
- [":target"] if in the current package / BUILD file
- ["[package]:[Target]"] if in another package

## Java Library example
#java_library #java 
```java 
java_library(

name = "token-generator-client",

srcs = ["src/eist/TokenGeneratorClient.java", "src/eist/Employee.java", "src/eist/Event.java"],

deps = [

'@maven//:org_springframework_boot_spring_boot_starter_web',

'@maven//:org_springframework_boot_spring_boot_starter_webflux',

'@maven//:org_springframework_spring_web',

],

)
```
>[!info] Make sure to remove the "special characters" from the dependencies (other than //:org) if copy pasting
### Java library with target dependencies
#deps
```java
java_library(

name = "event-facade",

srcs = ["src/eist/EventFacade.java", "src/eist/Employee.java", "src/eist/Event.java"],
// DO NOT !!!you can remove Employee.java and Event.java since they are part of the dependencies

deps = [":event-client", ":token-generator-client"],

)
```

>[!info] Bazel apparently cannot resolve indirect dependencies 
>so if you need a <something.java> that is included in the dependency of a dependency, Bazel cannot see this and will error on missing dependency => always include everything at every level except maybe the final binary.


## Java binary
#java_binary
``` java
java_binary(

name = "client",

srcs = ["src/eist/Main.java",],

deps = [":event-facade"],

main_class = "src/eist/Main.java",

)
```

## Target Visibility
by default visibility is private within the target's own package
` visibility = ["//visibility:<public | private>"] ` 

- `"//visibility:public"`: Grants access to all packages.
    
- `"//visibility:private"`: Does not grant any additional access; only targets in this location's package can use this target.
    
- `"//foo/bar:__pkg__"`: Grants access to `//foo/bar` (but not its subpackages).
    
- `"//foo/bar:__subpackages__"`: Grants access to `//foo/bar` and all of its direct and indirect sub-packages.
>[!info] The path that references the package is the path to the BUILD file not the .java files

# Bazel commands

### List all targets
``` bash
bazek query //...
```
### List all targets in a Package
```bash
bazel query //src/main/java/my_package/...
```
### Experimental to figure out missing dependencies
```bash 
bazel build //your:target --java_deps
```
