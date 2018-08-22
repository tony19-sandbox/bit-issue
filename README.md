> Demo of bitsrc problem

I followed the setup instructions at the project setup page in `https://bitsrc.io`:

<img width="400" alt="screen shot 2018-08-22 at 2 03 46 am" src="https://user-images.githubusercontent.com/26580/44449588-28ae2d80-a5b4-11e8-91b4-9fbd0c58522e.png">


But after importing the `env`s for the compiler and tester, the `bit build` step failed. It doesn't seem to know how to resolve paths with the `@` alias, even when `bit.json` contains the appropriate `resolveModules` config. How do I fix this?

## Expected Behavior
`bit build` is able to use `bit.json` config's `resolveModules` to resolve paths with aliases.

## Actual Behavior
`bit build` fails because it cannot resolve paths with aliases.

### Commands Run

<details>
<summary>Commands Run</summary>

```shell
➜ bit init
successfully initialized a bit workspace.

➜ bit add src/components/* -t 'src/components/{PARENT}/*.spec.js'
tracking component components/hello-world:
added src/components/HelloWorld/HelloWorld.spec.js
added src/components/HelloWorld/HelloWorld.vue

➜ bit login
already logged in

➜ bit status
new components
(use "bit tag --all [version]" to lock a version with all your changes)

     > components/hello-world ...  issues found
       missing packages dependencies (use your package manager to make sure all package dependencies are installed):
          src/components/HelloWorld/HelloWorld.spec.js -> @/components

see troubleshooting at https://docs.bitsrc.io/docs/troubleshooting-isolating.html
```

After learning the appropriate setup from the [troubleshooting link](https://docs.bitsrc.io/docs/troubleshooting-isolating.html), I updated `bit.json` to include:

    "resolveModules": {
        "aliases": {
            "@": "./src"
        }
    }

*(As an aside, it would be a more pleasant/seamless/convenient developer experience if `bit` automatically picked up the Webpack config for this.)*

And then I tried to pick up where I left off in the setup:

```shell
➜ bit status
new components
(use "bit tag --all [version]" to lock a version with all your changes)

     > components/hello-world ... ok

➜ bit import bit.envs/bundlers/vue --compiler
the following component environments were installed
- bit.envs/bundlers/vue@0.0.3

➜ bit import bit.envs/testers/jest --tester
the following component environments were installed
- bit.envs/testers/jest@0.0.34

➜ bit build
error: bit failed to build components/hello-world with the following exception:
Module not found: Error: Can't resolve '@/components/HelloWorld' in '/Users/tony/src/tmp/bit-issue/src/components/HelloWorld'.
ModuleNotFoundError: Module not found: Error: Can't resolve '@/components/HelloWorld' in '/Users/tony/src/tmp/bit-issue/src/components/HelloWorld'
    at factoryCallback (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/webpack/lib/Compilation.js:282:40)
    at factory (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/webpack/lib/NormalModuleFactory.js:237:20)
    at resolver (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/webpack/lib/NormalModuleFactory.js:60:20)
    at asyncLib.parallel (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/webpack/lib/NormalModuleFactory.js:127:20)
    at /Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/async/dist/async.js:3888:9
    at /Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/async/dist/async.js:473:16
    at iteratorCallback (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/async/dist/async.js:1062:13)
    at /Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/async/dist/async.js:969:16
    at /Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/async/dist/async.js:3885:13
    at resolvers.normal.resolve (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/webpack/lib/NormalModuleFactory.js:119:22)
    at onError (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/enhanced-resolve/lib/Resolver.js:65:10)
    at loggingCallbackWrapper (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/enhanced-resolve/lib/createInnerCallback.js:31:19)
    at runAfter (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/enhanced-resolve/lib/Resolver.js:158:4)
    at innerCallback (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/enhanced-resolve/lib/Resolver.js:146:3)
    at loggingCallbackWrapper (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/enhanced-resolve/lib/createInnerCallback.js:31:19)
    at next (/Users/tony/src/tmp/bit-issue/.git/bit/components/bundlers/vue/bit.envs/0.0.3/node_modules/tapable/lib/Tapable.js:252:11)
```
</details>

## Steps to Reproduce the Problem

 1. Run the following commands:

      ```shell
      git clone https://github.com/tony19-sandbox/bit-issue.git
      cd bit-issue
      bit build
      ```

## Specifications

  - Bit version: 13.0.4
  - Node version: 10.5.0
  - npm / yarn version: 6.1.0
  - Platform: macOS High Sierra 10.13.6
  - Bit compiler (include version): `bit.envs/bundlers/vue@0.0.3`
  - Bit tester (include version): `bit.envs/testers/jest@0.0.34`
