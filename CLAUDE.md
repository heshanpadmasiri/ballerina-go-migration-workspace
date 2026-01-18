Aim of this project is to line by line migrate the existing java based Ballerina compiler into go

# Source structure

- `ballerina-lang`
  Contains the original java source code
- `ballerina-lang-go`
  Contains the migrated go source code
- `javaGo`
  Contains the machine translation code

# Migrating given java source file to go

1. First use javaGo migration tool

   ```bash
   javaGo ${pathToJavaFile} ${pathToGoSource}
   ```

   Assuming you are running this in javaGo project directory it already has the configuration file to give you the correct license header. You may need to fix the package name

2. Then check if the migrated file has compilation errors and if so prompt user if they want you to attempt to fix them
