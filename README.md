 <div align="center">

# 🛡️ Android Hilt Setup

### 💉 Modern Dependency Injection for Android

**Kotlin • Jetpack Compose • Hilt • KSP**

<p>
  <img src="https://img.shields.io/badge/Kotlin-Project%20Dependent-purple?style=for-the-badge&logo=kotlin" />
  <img src="https://img.shields.io/badge/Hilt-2.60.1-blue?style=for-the-badge&logo=google" />
  <img src="https://img.shields.io/badge/KSP-2.3.4-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Compose-Ready-green?style=for-the-badge&logo=jetpackcompose" />
</p>

<p>
  A clean and reusable Hilt setup<br>
  for modern Android applications.
</p>

</div>

---

## 🚀 About

This repository contains a **reusable Dagger Hilt setup** for Android projects using **Kotlin and Jetpack Compose**.

It includes:

* 💉 Dependency Injection with Hilt
* ⚡ KSP configuration
* 🧠 Hilt + ViewModel
* 🧭 Hilt + Navigation Compose
* 🏗️ DI Module structure
* 📱 Jetpack Compose support
* 🔄 Reusable project configuration

---

## 🧰 Tech Stack

<div align="center">

|     Technology     |      Version      |
| :----------------: | :---------------: |
|      🟣 Kotlin     | Project dependent |
|        ⚡ KSP       |      `2.3.4`      |
|       💙 Hilt      |      `2.60.1`     |
|  🧩 AndroidX Hilt  |      `1.4.0`      |
| 🎨 Jetpack Compose | Project dependent |

</div>

---

## 🔄 Version Updates

> ⚠️ **Important:** Versions used in this repository are **not permanent**.

Hilt, AndroidX Hilt, KSP, Kotlin, Android Gradle Plugin and other Android dependencies are actively updated over time.

Because of this, **versions may change in future Android projects**.

For example:

```text
Hilt
2.60.1
   ↓
Future version
```

```text
AndroidX Hilt
1.4.0
   ↓
Future version
```

```text
KSP
2.3.4
   ↓
Future compatible version
```

### 📌 Before using this setup

Always check that:

* KSP is compatible with your Kotlin version.
* Hilt is compatible with your project configuration.
* AndroidX Hilt dependencies use the appropriate version.
* Gradle and Android Gradle Plugin versions are compatible.

> 💡 **The Hilt architecture and annotations generally remain familiar, while dependency/plugin versions may need to be updated over time.**

---

# ⚙️ Setup

## 1️⃣ Project-Level Plugins

Add the following plugins to your **project-level `build.gradle.kts`**:

```kotlin
plugins {
    id("com.google.devtools.ksp") version "2.3.4" apply false
    id("com.google.dagger.hilt.android") version "2.60.1" apply false
}
```

---

## 2️⃣ App-Level Plugins

Add the plugins to your **app-level `build.gradle.kts`**:

```kotlin
plugins {
    id("com.google.devtools.ksp")
    id("com.google.dagger.hilt.android")
}
```

---

## 3️⃣ Hilt Dependencies

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

# 🏁 Application Setup

Create an Application class:

```kotlin
@HiltAndroidApp
class MyApplication : Application()
```

Register it inside `AndroidManifest.xml`:

```xml
<application
    android:name=".MyApplication"
    android:theme="@style/Theme.MyApp">

</application>
```

---

# 🧩 Dependency Injection Module

Create a Hilt module using `@Module` and `@InstallIn`:

```kotlin
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

---

# 🧠 Hilt ViewModel

Use `@HiltViewModel` with constructor injection:

```kotlin
@HiltViewModel
class HomeViewModel @Inject constructor(
    private val repository: HomeRepository
) : ViewModel()
```

Inside Compose:

```kotlin
@Composable
fun HomeScreen(
    viewModel: HomeViewModel = hiltViewModel()
) {
    // UI
}
```

---

# 🧭 Navigation Compose

Example:

```kotlin
composable("home") {

    val viewModel: HomeViewModel = hiltViewModel()

    HomeScreen(
        viewModel = viewModel
    )
}
```

---

# 📦 Repository Injection

Example:

```kotlin
class HomeRepository @Inject constructor(
    private val apiService: ApiService
)
```

Hilt can automatically provide the repository wherever it is required.

---

# 📁 Recommended Structure

```text
com.example.myapp
│
├── di
│   └── AppModule.kt
│
├── data
│   ├── repository
│   │   └── HomeRepository.kt
│   │
│   └── remote
│       └── ApiService.kt
│
├── presentation
│   ├── viewmodel
│   │   └── HomeViewModel.kt
│   │
│   └── screen
│       └── HomeScreen.kt
│
└── MyApplication.kt
```

---

# 🔄 Dependency Flow

<div align="center">

```text
             ┌──────────────────┐
             │   Hilt Container │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │    AppModule     │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │    Repository    │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │    ViewModel     │
             └────────┬─────────┘
                      ↓
             ┌──────────────────┐
             │   Compose UI     │
             └──────────────────┘
```

</div>

---

# ⚠️ Common Issues

<details>
<summary><b>❌ @HiltAndroidApp not working?</b></summary>

Make sure the Hilt plugin is applied:

```kotlin
plugins {
    id("com.google.dagger.hilt.android")
}
```

And your Application class contains:

```kotlin
@HiltAndroidApp
class MyApplication : Application()
```

</details>

<br>

<details>
<summary><b>❌ @HiltViewModel not generated?</b></summary>

Check the Hilt compiler:

```kotlin
ksp("com.google.dagger:hilt-android-compiler:2.60.1")
```

Then sync and rebuild the project.

</details>

<br>

<details>
<summary><b>❌ hiltViewModel() unresolved?</b></summary>

Make sure Navigation Compose integration is included:

```kotlin
implementation("androidx.hilt:hilt-navigation-compose:1.4.0")
```

Then sync Gradle.

</details>

---

# ✅ Setup Checklist

```text
☑ KSP plugin
☑ Hilt plugin
☑ Hilt dependencies
☑ @HiltAndroidApp
☑ Application registered in Manifest
☑ @AndroidEntryPoint
☑ @HiltViewModel
☑ @Module
☑ @InstallIn
☑ KSP compatible with Kotlin
☑ Dependencies compatible with project versions
```

---

## 📌 Quick Reference

<div align="center">

| Component            | Purpose                                   |
| :------------------- | :---------------------------------------- |
| `@HiltAndroidApp`    | Starts Hilt in the application            |
| `@AndroidEntryPoint` | Enables injection into Android components |
| `@HiltViewModel`     | Creates Hilt-enabled ViewModels           |
| `@Module`            | Defines dependency providers              |
| `@Provides`          | Provides object instances                 |
| `@Binds`             | Binds interfaces to implementations       |
| `@InstallIn`         | Defines Hilt component scope              |
| `hiltViewModel()`    | Gets Hilt ViewModel in Compose            |

</div>

---

# 🎯 Purpose

This repository is designed as a **reusable Hilt reference and setup** for modern Android development.

You can use it as a starting point for projects using:

**Kotlin + Jetpack Compose + MVVM + Repository Pattern + Hilt**

---

<div align="center">

### ⭐ Keep the setup updated as Android dependencies evolve.

**Versions change over time — always use versions compatible with your current project.**

</div>
