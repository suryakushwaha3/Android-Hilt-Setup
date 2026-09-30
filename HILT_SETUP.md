<div align="center">

# 🛡️ Android Hilt Setup

### 💉 Modern Dependency Injection for Android

<p>
  <b>Kotlin</b> •
  <b>Jetpack Compose</b> •
  <b>Hilt</b> •
  <b>KSP</b> •
  <b>MVVM</b>
</p>

<p>
  <img src="https://img.shields.io/badge/Kotlin-2.x-purple?style=for-the-badge&logo=kotlin" alt="Kotlin">
  <img src="https://img.shields.io/badge/Hilt-2.60.1-blue?style=for-the-badge" alt="Hilt">
  <img src="https://img.shields.io/badge/KSP-2.3.4-orange?style=for-the-badge" alt="KSP">
  <img src="https://img.shields.io/badge/Jetpack%20Compose-UI-green?style=for-the-badge&logo=jetpackcompose" alt="Compose">
</p>

</div>

---

## 📖 Overview

**Hilt** is a dependency injection library built on top of **Dagger** and designed specifically for Android applications.

It helps manage dependencies between:

* 🏗️ Application
* 📦 Repository
* 🌐 API Service
* 🧠 ViewModel
* 💾 Database
* 🎨 Compose UI

Instead of manually creating objects, Hilt manages their creation and injection.

---

## ⚠️ Version Information

<div align="center">

> 🔄 **Versions change over time.**

</div>

The versions used in this documentation are based on the current project setup.

The following technologies can receive updates:

| Technology      | Version can change? |
| --------------- | :-----------------: |
| Kotlin          |          ✅          |
| KSP             |          ✅          |
| Hilt            |          ✅          |
| AndroidX Hilt   |          ✅          |
| AGP             |          ✅          |
| Gradle          |          ✅          |
| Jetpack Compose |          ✅          |

> **Important:** Before starting a new project, verify that the selected Kotlin, KSP, Hilt, AGP and Gradle versions are compatible.

---

# ⚙️ 1. Project-Level Plugins

Open the project-level:

```text
build.gradle.kts
```

Add:

```kotlin
plugins {
    id("com.google.devtools.ksp") version "2.3.4" apply false
    id("com.google.dagger.hilt.android") version "2.60.1" apply false
}
```

### 🔍 Plugin Purpose

| Plugin                           | Purpose                   |
| -------------------------------- | ------------------------- |
| `com.google.devtools.ksp`        | Kotlin Symbol Processing  |
| `com.google.dagger.hilt.android` | Hilt Dependency Injection |

---

# 📱 2. App-Level Plugins

Open:

```text
app/build.gradle.kts
```

Add:

```kotlin
plugins {
    id("com.google.devtools.ksp")
    id("com.google.dagger.hilt.android")
}
```

---

# 📦 3. Hilt Dependencies

Inside:

```kotlin
dependencies {
    
}
```

add:

```kotlin
dependencies {

    // 💉 Hilt Dependency Injection
    implementation("com.google.dagger:hilt-android:2.60.1")
    ksp("com.google.dagger:hilt-android-compiler:2.60.1")

    // 🧠 Hilt + Lifecycle ViewModel + Compose
    implementation("androidx.hilt:hilt-lifecycle-viewmodel-compose:1.4.0")

    // 🧭 Hilt + Navigation Compose
    implementation("androidx.hilt:hilt-navigation-compose:1.4.0")

    // ⚡ AndroidX Hilt Compiler
    ksp("androidx.hilt:hilt-compiler:1.4.0")
}
```

---

# 🚀 4. Application Setup

Hilt requires an Application class.

Create:

```text
MyApplication.kt
```

```kotlin
import android.app.Application
import dagger.hilt.android.HiltAndroidApp

@HiltAndroidApp
class MyApplication : Application()
```

The most important annotation is:

```kotlin
@HiltAndroidApp
```

This initializes Hilt for the entire application.

---

# 📝 5. AndroidManifest Setup

Open:

```text
AndroidManifest.xml
```

Add your Application class:

```xml
<application
    android:name=".MyApplication"
    android:theme="@style/Theme.MyApp">

</application>
```

If your Application class is inside another package, use the correct package path.

---

# 🧩 6. Activity Setup

Add:

```kotlin
@AndroidEntryPoint
```

to your Activity.

Example:

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import dagger.hilt.android.AndroidEntryPoint

@AndroidEntryPoint
class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            App()
        }
    }
}
```

---

# 🗂️ 7. Create DI Package

Recommended structure:

```text
com.example.myapp
│
├── di
│   ├── AppModule.kt
│   └── RepositoryModule.kt
│
├── data
│   ├── remote
│   └── repository
│
├── presentation
│   ├── screen
│   └── viewmodel
│
├── MainActivity.kt
└── MyApplication.kt
```

---

# 🔧 8. Hilt Module

Create:

```text
di/AppModule.kt
```

Example:

```kotlin
package com.example.myapp.di

import dagger.Module
import dagger.Provides
import dagger.hilt.InstallIn
import dagger.hilt.components.SingletonComponent
import javax.inject.Singleton

@Module
@InstallIn(SingletonComponent::class)
object AppModule {

    @Provides
    @Singleton
    fun provideSomeDependency(): SomeDependency {
        return SomeDependency()
    }
}
```

### 🧠 What happens here?

```text
@Module
     ↓
Defines dependencies

@InstallIn
     ↓
Defines component scope

@Provides
     ↓
Tells Hilt how to create object

@Singleton
     ↓
Keeps one instance
```

---

# 💉 9. Constructor Injection

Hilt can automatically inject dependencies through constructors.

Example:

```kotlin
class HomeRepository @Inject constructor(
    private val apiService: ApiService
)
```

If Hilt already knows how to create `ApiService`, it can automatically provide it to `HomeRepository`.

---

# 🏗️ 10. @Provides

Use `@Provides` when you need to manually tell Hilt how to create an object.

Example:

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object AppModule {

    @Provides
    @Singleton
    fun provideApiService(): ApiService {
        return ApiService()
    }
}
```

---

# 🔗 11. @Binds

Use `@Binds` when connecting an interface with its implementation.

### Interface

```kotlin
interface UserRepository {

    fun getUser()
}
```

### Implementation

```kotlin
class UserRepositoryImpl @Inject constructor() : UserRepository {

    override fun getUser() {
        // Implementation
    }
}
```

### Module

```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {

    @Binds
    abstract fun bindUserRepository(
        implementation: UserRepositoryImpl
    ): UserRepository
}
```

---

# 🧠 12. Hilt ViewModel

Create a ViewModel using:

```kotlin
@HiltViewModel
```

Example:

```kotlin
import androidx.lifecycle.ViewModel
import dagger.hilt.android.lifecycle.HiltViewModel
import javax.inject.Inject

@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repository: HomeRepository
) : ViewModel() {

}
```

---

# 🎨 13. ViewModel in Jetpack Compose

Use:

```kotlin
hiltViewModel()
```

Example:

```kotlin
import androidx.compose.runtime.Composable
import androidx.hilt.lifecycle.viewmodel.compose.hiltViewModel

@Composable
fun HomeScreen(
    viewModel: HomeViewModel = hiltViewModel()
) {

}
```

---

# 🗃️ 14. Repository Injection

Example:

```kotlin
class HomeRepository @Inject constructor(
    private val apiService: ApiService
) {

    fun getHomeData() {
        // API logic
    }
}
```

Hilt automatically provides the required dependency when it is available in the dependency graph.

---

# 🧭 15. Navigation Compose

Example:

```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {

    composable("home") {

        val viewModel: HomeViewModel = hiltViewModel()

        HomeScreen(
            viewModel = viewModel
        )
    }
}
```

---

# 🔄 16. Dependency Flow

<div align="center">

```text
┌─────────────────────┐
│        HILT         │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│      DI MODULE      │
│    AppModule.kt     │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│     API / DATA      │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│     REPOSITORY      │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│      VIEWMODEL      │
└──────────┬──────────┘
           ↓
┌─────────────────────┐
│    COMPOSE UI       │
└─────────────────────┘
```

</div>

---

# 📁 17. Recommended Architecture

```text
com.example.myapp
│
├── di
│   ├── AppModule.kt
│   └── RepositoryModule.kt
│
├── data
│   ├── remote
│   │   └── ApiService.kt
│   │
│   └── repository
│       └── HomeRepository.kt
│
├── presentation
│   ├── viewmodel
│   │   └── HomeViewModel.kt
│   │
│   └── screen
│       └── HomeScreen.kt
│
├── MainActivity.kt
└── MyApplication.kt
```

---

# 📦 18. Complete Gradle Configuration

<details>
<summary><b>🔽 Project-Level build.gradle.kts</b></summary>

```kotlin
plugins {
    id("com.google.devtools.ksp") version "2.3.4" apply false
    id("com.google.dagger.hilt.android") version "2.60.1" apply false
}
```

</details>

<details>
<summary><b>🔽 App-Level build.gradle.kts</b></summary>

```kotlin
plugins {
    id("com.google.devtools.ksp")
    id("com.google.dagger.hilt.android")
}

dependencies {

    // Hilt
    implementation("com.google.dagger:hilt-android:2.60.1")
    ksp("com.google.dagger:hilt-android-compiler:2.60.1")

    // Hilt + Lifecycle ViewModel + Compose
    implementation("androidx.hilt:hilt-lifecycle-viewmodel-compose:1.4.0")

    // Hilt + Navigation Compose
    implementation("androidx.hilt:hilt-navigation-compose:1.4.0")

    // AndroidX Hilt Compiler
    ksp("androidx.hilt:hilt-compiler:1.4.0")
}
```

</details>

---

# 🔍 19. Kotlin Serialization Note

If your project contains:

```kotlin
kotlin("plugin.serialization") version "1.9.0"
```

this is **not a Hilt plugin**.

It belongs to **Kotlin Serialization**.

### Hilt Plugin

```kotlin
id("com.google.dagger.hilt.android")
```

### KSP Plugin

```kotlin
id("com.google.devtools.ksp")
```

### Kotlin Serialization

```kotlin
kotlin("plugin.serialization")
```

These are separate plugins.

---

# ⚠️ 20. Version Compatibility

The dependency versions should not be copied blindly into every future project.

Keep in mind:

```text
Kotlin
   │
   └── KSP
        │
        ├── AGP
        │
        └── Gradle
```

And separately:

```text
Hilt
 │
 └── AndroidX Hilt
```

### Important Points

* KSP must match the required Kotlin compatibility.
* Hilt versions can change.
* AndroidX Hilt versions can change.
* AGP versions can change.
* Gradle versions can change.
* Compose versions can change.

> 🔄 **Always verify compatible versions when updating the project.**

---

# 🧩 21. Hilt Annotations

| Annotation           | Purpose                             |
| -------------------- | ----------------------------------- |
| `@HiltAndroidApp`    | Initializes Hilt in the Application |
| `@AndroidEntryPoint` | Enables Hilt in Android components  |
| `@HiltViewModel`     | Enables Hilt injection in ViewModel |
| `@Inject`            | Constructor/field injection         |
| `@Module`            | Defines dependency module           |
| `@Provides`          | Manually provides dependency        |
| `@Binds`             | Binds implementation to interface   |
| `@InstallIn`         | Defines Hilt component              |
| `@Singleton`         | Provides a single scoped instance   |

---

# 🏛️ 22. Common Hilt Components

```text
SingletonComponent
        │
        ├── ActivityRetainedComponent
        │
        ├── ViewModelComponent
        │
        └── ActivityComponent
```

For application-wide dependencies:

```kotlin
@InstallIn(SingletonComponent::class)
```

is commonly used.

---

# 🚨 23. Common Errors

<details>
<summary><b>❌ @HiltAndroidApp Not Working</b></summary>

Make sure your Application class contains:

```kotlin
@HiltAndroidApp
class MyApplication : Application()
```

And your manifest contains:

```xml
<application
    android:name=".MyApplication">
```

</details>

---

<details>
<summary><b>❌ Injection Not Working in Activity</b></summary>

Make sure the Activity contains:

```kotlin
@AndroidEntryPoint
class MainActivity : ComponentActivity()
```

</details>

---

<details>
<summary><b>❌ ViewModel Injection Error</b></summary>

Make sure your ViewModel contains:

```kotlin
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repository: HomeRepository
) : ViewModel()
```

And Compose uses:

```kotlin
val viewModel: HomeViewModel = hiltViewModel()
```

</details>

---

<details>
<summary><b>❌ KSP Error</b></summary>

Check compatibility between:

```text
Kotlin
↓
KSP
↓
AGP
↓
Gradle
```

Also verify that the Hilt compiler dependency is present.

</details>

---

# ✅ 24. Setup Checklist

```text
☑ KSP plugin added
☑ Hilt plugin added
☑ Hilt dependency added
☑ Hilt compiler added
☑ AndroidX Hilt dependencies added

☑ @HiltAndroidApp
☑ Application registered in Manifest
☑ @AndroidEntryPoint
☑ @HiltViewModel
☑ @Inject
☑ @Module
☑ @Provides / @Binds
☑ @InstallIn
☑ hiltViewModel()

☑ Kotlin + KSP compatibility checked
☑ AGP + Gradle compatibility checked
☑ Hilt versions checked
```

---

# ⚡ 25. Quick Reference

| Requirement       | Code                             |
| ----------------- | -------------------------------- |
| Hilt Plugin       | `com.google.dagger.hilt.android` |
| KSP Plugin        | `com.google.devtools.ksp`        |
| Application       | `@HiltAndroidApp`                |
| Activity          | `@AndroidEntryPoint`             |
| ViewModel         | `@HiltViewModel`                 |
| Injection         | `@Inject`                        |
| Module            | `@Module`                        |
| Provider          | `@Provides`                      |
| Interface Binding | `@Binds`                         |
| Component         | `@InstallIn`                     |
| Singleton         | `@Singleton`                     |
| Compose ViewModel | `hiltViewModel()`                |

---

# 🎯 26. Where Hilt Can Be Used

Hilt is useful in projects using:

<div align="center">

|     Technology     | Hilt |
| :----------------: | :--: |
|       Kotlin       |   ✅  |
|   Jetpack Compose  |   ✅  |
|        MVVM        |   ✅  |
|      Firebase      |   ✅  |
|      REST API      |   ✅  |
|      Retrofit      |   ✅  |
|        Room        |   ✅  |
| Repository Pattern |   ✅  |
|      StateFlow     |   ✅  |
|     Coroutines     |   ✅  |

</div>

---

# 📌 27. Final Notes

This documentation is designed as a **reusable Hilt setup reference** for Android projects.

It can be used for:

* 📱 Kotlin Android applications
* 🎨 Jetpack Compose projects
* 🏗️ MVVM architecture
* 🌐 REST API applications
* 🔥 Firebase applications
* 💾 Room Database projects
* 📦 Repository-based architecture
* 🚀 Scalable Android applications

---

<div align="center">

## 🛡️ Hilt + KSP + Jetpack Compose

### Clean Dependency Injection • Better Architecture • Easier Maintenance

<br>

**Keep this file updated whenever your project's Hilt configuration changes.**

</div>
