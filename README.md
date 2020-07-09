# Android BuildConfig properties
Generate BuildConfig fields for build types as well as product flavors from a property file.

## Overview
- All property files are stored under `etc/config` directory, which is located in a root directory of your project.
- Each build type/product flavor has its own property file: `etc/config/<build type/product flavor>-config.properties`.
- Shared properties between all build types/product flavors are stored in `etc/config/default-config.properties` file.
- In case of a multi-module Android project, the configuration properties are shared between all modules and located in `etc/config` directory, under the root project. 
- Comments (`#`) and blank lines in property files are ignored
- While generating BuildConfig fields, property names are uppercased and invalid characters from their names (`.`, `-`) are replaced by underscore (`_`). 

## Supported property data-types
> At the moment we do support `String`, `int` as well as `boolean`.

- String:
    > Strings **can** contain spaces or special characters

    ```properties
    com.android.properties.string.sample=This is a sample string 123
    ```

- Integer:
    ```properties
    com.android.properties.integer.sample=123
    ```

- Boolean:
    > Supported values are `true` and `false`.

    ```properties
    com.android.properties.boolean.sample=true
    ```

## Set up
1. Create a directory structure and config property files as described in overview.
2. Copy `build-config-properties.gradle` file from this repository into `etc/config/scripts` directory.
3. Configure `build.gradle`:
    - Apply configuration properties script:
        ```groovy
        apply {
            from "$rootDir/etc/config/scripts/build-config-properties.gradle"
        }
        ```
    - Apply BuildConfig properties generation under each build type/product flavor block, you want to apply configuration properties for:
        ```groovy
        android {
            buildTypes {
                debug {
                    processBuildConfigProperties it // Use properties for this build type
                }
        
                release {
                    // Don't use properties for this build type
                }
            }
        
            productFlavors {
                mock {
                    processBuildConfigProperties it // Use properties for this product flavor
                }
        
                prod {
                    // Don't use properties for this product flavor
                }
            }
        }
        ```

## Sample
Let's say we've got an Android project which has two build types (`debug`, `release`) and two flavors (`mock`, `prod`), you can have a look at a sample configuration for such a project in our [sample](sample) directory.

Configuration properties directory structure:
```
+---build.gradle                                // build.gradle file
+---etc
|   /---config
|       +---default-config.properties           // default properties, shared between all build types and product flavors
|       +---debug-config.properties             // properties applyied only to debug build type
|       +---mock-config.properties              // properties applyied only to mock product flavor
|       +---prod-config.properties              // properties applyied only to prod product flavor
|       +---release-config.properties           // properties applyied only to release build type
|       \---scripts
|           \---build-config-properties.gradle  // script responsible for generating BuildConfig fields
```
