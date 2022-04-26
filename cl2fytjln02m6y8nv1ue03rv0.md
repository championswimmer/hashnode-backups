## Managing libraries and dependencies in Android projects with Gradle version catalog

In large multi-module Android (or for that matter any gradle-based) project, version management starts to become a bit of a pain. 

The most common way people have been coping with that [was using buildSrc](https://proandroiddev.com/better-dependencies-management-using-buildsrc-kotlin-dsl-eda31cdb81bf), but the problem with that is `buildSrc` changes cause cache invalidations across your build and increases build times. 

There obviously now calls to action to [move from buildSrc to includeBuild](https://medium.com/bumble-tech/how-to-use-composite-builds-as-a-replacement-of-buildsrc-in-gradle-64ff99344b58) 

If you have other custom build logic, and build plugins, it ofcourse makes sense to move to the `includeBuild` mechanism, but if you needed `buildSrc` only for version management of dependencies, there is a better way - [gradle version catalogs](https://docs.gradle.org/current/userguide/platforms.html)

Elaborate explanations of how it works is available in the previous link, I will quickly cover the basic set of steps 

### Add versions and libraries to `settings.gradle(.kts)` 

> NOTE: All my examples are in kts because buildscripts in kotlin have better DX (autocomplete, linting etc) 

This is for example what I added in my `settings.gradle.kts` (expanding on the lines afterwards) 

```
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }

    versionCatalogs {
        create("libs") {
            version("androidx.navigation", "2.4.1")
            version("android.material", "1.7.0-alpha01")
            version("moshi", "1.13.0")
            version("retrofit", "2.9.0")
            version("moshi", "1.13.0")

            library("android.material", "com.google.android.material", "material").versionRef("android.material")
            library("androidx.navigation.fragment", "androidx.navigation", "navigation-fragment-ktx").versionRef("androidx.navigation")
            library("androidx.navigation.ui", "androidx.navigation", "navigation-ui-ktx").versionRef("androidx.navigation")
            library("androidx.core", "androidx.core:core-ktx:1.7.0")
            library("androidx.appcompat", "androidx.appcompat:appcompat:1.4.1")
            library("okhttp", "com.squareup.okhttp3:okhttp:5.0.0-alpha.6")
            library("retrofit", "com.squareup.retrofit2", "retrofit").versionRef("retrofit")
            library("retrofit.converter.moshi", "com.squareup.retrofit2", "converter-moshi").versionRef("retrofit")
            library("moshi", "com.squareup.moshi", "moshi-kotlin").versionRef("moshi")
            library("moshi.compiler", "com.squareup.moshi", "moshi-kotlin-codegen").versionRef("moshi")

            bundle("androidx.appcompat", listOf("androidx.core", "androidx.appcompat"))
            bundle("androidx.navigation", listOf("androidx.navigation.fragment", "androidx.navigation.ui"))
            bundle("retrofit", listOf("retrofit", "retrofit.converter.moshi"))
        }
    }
}
```

#### 1. Creating 'libs' block 


```
dependencyResolutionManagement {
  versionCatalogs {
    create("libs") {...}
  }
}
```

#### 2. Add version constants 

```
version("android.material", "1.7.0-alpha01")
version("moshi", "1.13.0")
version("retrofit", "2.9.0")

```

#### 3. Add each dependency 
You can refer versions constants via `versionRef()` 
```
library("android.material", "com.google.android.material", "material").versionRef("android.material")

```

#### 4. Create 'bundles' if there are a set of libraries that get used together 

```
bundle("androidx.appcompat", listOf("androidx.core", "androidx.appcompat"))
``` 

### Using version catalog libraries in `build.gradle(.kts)` files 

The way to use these in your module(s)' build.gradle is as follows 

```
dependencies {

    implementation(libs.bundles.androidx.appcompat)
    implementation(libs.android.material)
    implementation("androidx.constraintlayout:constraintlayout:2.1.3")
    implementation(libs.bundles.androidx.navigation)
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.3")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.4.0")
}

```

Normal libraries are used like this 

```
    implementation(libs.android.material)
```

While an entire bundle can be added like this 


```
    implementation(libs.android.material)
```

### What does not work ? 

There are a few caveats (as of April 2022, most I expect to improve) 

#### Gradle 7.4+ 
If you are using Gradle 7.3 or below, then version catalogs are unstable/experimental API and you need to mark that as so. From 7.4 they are default enabled 

#### Android Studio Support of Version Management 

Dependencies inside version catalogs do not show up in **Project Structure** options of Android Studio nor does studio remind you to update the dependencies that are out-of-date. 

As you can see below there is a warning regarding version catalogs, and the version catalog dependencies do not show up 
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650964851253/DJUqK8dvR.png)

#### Autocomplete works with Kotlin only 

If you use `build.gradle` (groovy) instead of `build.gradle.kts` (kotlinscript), then the `libs.x.y` dependencies will not have autocomplete or type-checking

Below is autocomplete working only inside `.kts` files. 
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1650964965128/RjHh6mos9.png)
