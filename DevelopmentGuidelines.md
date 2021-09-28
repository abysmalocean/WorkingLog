# Development Guidelines

## Perception Specific Guidelines

1. Use `#pragma once` instead of trying to come up with unique headier guards. 
2. Put all your code in `namespace av`
   1. Define a new namespace for stuff you want to hide like 'detail' or 'internal' or anonymous namespaces.
   2. If there is a namespace collision, then first reason why the collision exits. If you really not able to avoid the collision then create a namespace inside namespace av to specialize the name. 
3. Follow the file/folder/variable name convention of the module that you are currently working on. 
4. There are 5 folders under the `src` directory of perception. Each of them have a purpose
   1. `adaptors` : Adaptors are abstract classes used to access some specific data of functionality that is provided by an external module.
      1. This folder contains top level adaptors like transform helper which are shred by multiple modules. 
   2. `datatypes` : Generic data types that are used to communicate between modules/libs
   3. `lib` : libs are general libraries that implement particular functionality. Their scope must be limited to that one functionality. 
   4. `modules` - Modules are thin wrappers around an executable.
      1. They must not depend on any "middleware".
      2. They must be easily to testable without launching any other task. 
      3. They use adaptors to access middleware functionality. 
      4. All application logic is implemented within a module. 
   5. `task` : Tasks use modules to implement the executable.
      1. They can use the "middleware"
      2. The do not implement any application logic. 
      3. They only provide a mechanism to read the configurations and communicate using the middleware. 
5. Following restrictions apply and must be enforced during Code Review. 
   1. Modules cannot depend on each other. Each module is independent of other modules. 
   2. Tasks cannot depend on each other. Each task is independent of other task. 
   3. A task is depends on one and only one module. 
   4. A module is depended on by one and only one task. 
6. All tests are in a test folder within the folder in which an adaptor/lib/module/task is implemented.
   1. Each adaptor/lib/module/task must be independently tested. They cannot be tested through another adaptor/lib/module/task.
   2. Refer - C++ Unit [Testing Guide](https://confluence.ci.motional.com/confluence/pages/createpage.action?spaceKey=SAG&title=C%2B%2B+Unit+Testing+Guide)
7. Minimize the public API that is exposed for each adaptor/lib/module/task
   1. Minimize the number of public exposed headers. 
   2. Minimize the number of publicly exposed functions, data members, etc. 
   3. Use plmpl design [link](https://en.cppreference.com/w/cpp/language/pimpl), where performance is not affected significantly. 
8. Reduce complication time. 
   1. Refactor includes. 
   2. Use forward declarations where possible. 
   3. If your are using templated follow [link](https://confluence.ci.motional.com/confluence/display/PCD/4+-+Templates+and+the+agony+of+endless%2C+gigantic%2C+includes)
9. Parallelize whenever possible. 

## Git Hooks

1. pre-commit
   1. Check formatting. If formatting is incorrect, the it will correct it. Add the changes, and commit again. 
   2. Checks that branch name matches the expected templates. 
   3. Files greater than 1 MB should be put in Git LFS. Otherwise they will be rejected. 
2. Pre-commit-msg
   1. If commit is in the `feature/bugfix/hotfix` branches then pre-populate the commit message with the commit template