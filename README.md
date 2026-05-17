# Gradle C++ Example

A minimal multi-project Gradle build demonstrating a C++ shared/static library consumed by a C++ executable.

## Project structure

```
.
├── settings.gradle
├── lib/
│   ├── build.gradle
│   └── src/main/
│       ├── public/greeting.h      # exported API header
│       └── cpp/greeting.cpp       # implementation
└── app/
    ├── build.gradle
    └── src/main/cpp/main.cpp      # executable entry point
```

## Prerequisites

- Gradle 7+ (`gradle --version`)
- GCC or Clang toolchain

## Build

Generate the Gradle wrapper (first time only):

```bash
gradle wrapper
```

Build everything:

```bash
./gradlew assemble
```

## Run

```bash
./app/build/exe/main/debug/app
```

Expected output:

```
Hello, World!
```

## Useful tasks

| Command | Description |
|---|---|
| `./gradlew assemble` | Compile library and executable |
| `./gradlew :lib:assemble` | Build only the library |
| `./gradlew :app:assemble` | Build only the executable (and its deps) |
| `./gradlew clean` | Delete all build outputs |
| `./gradlew tasks` | List available tasks |

## Changing the compiler (g++ → clang++)

**Option 1 — `model {}` block (recommended)**

Add to any `build.gradle` that uses `cpp-library` or `cpp-application`:

```groovy
import org.gradle.nativeplatform.toolchain.Clang

model {
    toolChains {
        clang(Clang)
    }
}
```

To keep both and control priority by order:

```groovy
import org.gradle.nativeplatform.toolchain.Clang
import org.gradle.nativeplatform.toolchain.Gcc

model {
    toolChains {
        clang(Clang)   // tried first
        gcc(Gcc)       // fallback
    }
}
```

To specify a versioned binary (e.g. `clang++-18`):

```groovy
model {
    toolChains {
        clang(Clang) {
            path '/usr/bin'
            cppCompiler.executable = 'clang++-18'
        }
    }
}
```

Place the `model {}` block in the **root `build.gradle`** to affect all subprojects, or inside an individual subproject's `build.gradle` to scope it there.

**Option 2 — PATH manipulation**

Gradle walks PATH looking for `g++`, `clang++`, etc. Prepend the LLVM bin directory before building:

```bash
PATH=/usr/lib/llvm-18/bin:$PATH ./gradlew assemble
```
