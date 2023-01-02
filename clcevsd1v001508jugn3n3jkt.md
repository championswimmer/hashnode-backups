# Publishing a Kotlin Multiplatform Project in all platforms (Win, Mac, Linux, JVM, JS) with Github Actions

So I recently made a Parking Lot CLI program (the kind of ones you make for low-level system design interview questions). You can check the following terminal recording.

[![asciicast](https://asciinema.org/a/549083.svg align="left")](https://asciinema.org/a/549083)

The interesting thing is, it is made using Kotlin Multiplatform, which means it can be compiled to the following formats

* Native Windows .exe executable (via mingw)
    
* Native MacOS executable
    
* Native Linux binary
    
* .jar file for running on JVM
    
* .js file that can be run using NodeJS
    

And they way I have set it up on Github, on every tag, it releases all these binaries.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672668339377/d5ebbb52-670c-4e75-9365-b644fd402e3b.png align="center")

So how does that work, you'll ask? Here's how.

## Compilation Steps

### 1\. JavaScript

```yaml
jobs:
  build_js:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'microsoft'
      - name: Build with Gradle
        uses: gradle/gradle-build-action@v2
        with:
          gradle-version: current
          arguments: compileProductionExecutableKotlinJs
      - name: Tar Artifact
        run: tar -czf ParkingLot-LLD-Kotlin-MPP.nodejs.tgz build/js/packages/ParkingLot-LLD-Kotlin-MPP
      - name: Upload JS Build
        uses: actions/upload-artifact@v2
        with:
          name: js-build
          path: ParkingLot-LLD-Kotlin-MPP.nodejs.tgz
```

### 2\. JVM

```yaml
  build_jvm:
    ...
    steps:
      ...
      - name: Build with Gradle
        with:
          arguments: assembleDist # java distribution
      - name: Upload JVM Build
        uses: actions/upload-artifact@v2
        with:
          name: jvm-build
          path: build/distributions/ParkingLot-LLD-Kotlin-MPP-*.zip # this is the path where it is created
```

### 3\. MacOS

```yaml
  build_macos:
    runs-on: macos-latest # build this on a Mac
    steps:
      ...
      - name: Build with Gradle
        with:
          arguments: macosX64Binaries # to build mac binary
      - name: Upload MacOS Build
        uses: actions/upload-artifact@v2
        with:
          name: macos-build
          path: build/bin/macosX64/releaseExecutable/ParkingLot-LLD-Kotlin-MPP.kexe
```

---

Similarly Windows and Linux are done too. You can check the entire workflow here

%[https://github.com/championswimmer/ParkingLot-LLD-Kotlin-MPP/blob/main/.github/workflows/build.yml]