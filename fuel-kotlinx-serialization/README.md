# kotlinx.serialzationn

_requires the [kotlinx-serialization extension](#dependency---fuel-kotlinx-serialization)_  
_requires [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization#gradlejvm)_

## kotlinx-serialization setup

_requires kotlinx-serialization_

`build.gradle.kts`

```kotlin
plugin {
    id("kotlinx-serialization") version "1.3.0" // must match kotlin version
}

repositories {
    jcenter()
    // artifacts are published to this repository
    maven(url = "https://kotlin.bintray.com/kotlinx")
}

dependencies {
    implementation(group = "org.jetbrains.kotlinx", name = "kotlinx-serialization", version = "0.9.0")
}

```

`settings.gradle.kts` 

```kotlin
pluginManagement {
    repositories {
        mavenCentral()
        jcenter()
        gradlePluginPortal()
    }
    resolutionStrategy {
        eachPlugin {
            if (requested.id.id == "kotlinx-serialization") {
                useModule("org.jetbrains.kotlin:kotlin-serialization:${requested.version}")
            }
        }
    }
}
```

# Deserialization

```kotlin
@Serializable
data class HttpBinUserAgentModel(var userAgent: String = "")

Fuel.get("/user-agent").responseObject<HttpBinUserAgentModel> { _, _, result ->}

// alternatively

val (request, response, result) = "/user-agent".httpGet()
    .responseObject<HttpBinUserAgentModel>()
```

this is by default strict and will reject unknown keys, for that you can pass a custom `JSON` instance

JSON(nonstrict = true)

```kotlin
@Serializable
data class HttpBinUserAgentModel(var userAgent: String = "")

Fuel.get("/user-agent").responseObject<HttpBinUserAgentModel>(json = JSON(nonstrict = true)) { _, _, result -> }

// alternatively

val (request, response, result) = "/user-agent".httpGet()
    .responseObject<HttpBinUserAgentModel>(json = JSON(nonstrict = true))

```

kotlinx.serialization can not always guess the correct serialzer to use, when generics are involved for example

```kotlin
@Serializable
data class HttpBinUserAgentModel(var userAgent: String = "")

Fuel.get("/list/user-agent").responseObject<HttpBinUserAgentModel>(loader = HttpBinUserAgentModel.serilaizer().list) { _, _, result ->}

// alternatively

val (request, response, result) = "/list/user-agent".httpGet()
    .responseObject<HttpBinUserAgentModel>(loader = HttpBinUserAgentModel.serilaizer().list)

```

it can be used with coroutines by using `kotlinxDeserilaizerOf()` it takes the same `json` and `loader` as parameters

```kotlin
@Serializable
data class HttpBinUserAgentModel(var userAgent: String = "")

Fuel.get("/user-agent").awaitResponseObject<HttpBinUserAgentModel>(kotlinxDeserializerOf()) { _, _, result ->
}

// alternatively

val (request, response, result) = "/user-agent".httpGet()
    .awaitResponseObject<HttpBinUserAgentModel>(kotlinxDeserializerOf())

```