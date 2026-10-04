# AppDiscovery SDK for Android

A lightweight Android SDK that adds an offerwall to your app. Your users earn rewards by completing offers, and your app is notified through simple callbacks.

The SDK is configured with the host of your own offerwall (given to you by the network you publish for). There is no default host.

## Requirements

| Requirement | Version |
|---|---|
| Android minSdk | 21 (Android 5.0) |
| Java | 17 |
| Kotlin | 1.9.0+ |
| AndroidX | Required |

## Installation

Add the JitPack repository (`settings.gradle`):

```groovy
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
}
```

Add the dependency (`app/build.gradle`):

```groovy
dependencies {
    implementation 'com.github.appdiscoverysdk:appdiscovery-android:1.0.0'
}
```

Kotlin DSL:

```kotlin
implementation("com.github.appdiscoverysdk:appdiscovery-android:1.0.0")
```

The manifest permissions (`INTERNET`, `ACCESS_NETWORK_STATE`, `AD_ID`) and the offerwall activity are merged automatically.

## Quick start

Kotlin:

```kotlin
import com.appdiscoverysdk.AppDiscovery

val offerwall = AppDiscovery.create(
    host = "offers.example.com",  // Host of your offerwall (required)
    appId = "YOUR_APP_ID",
    sdkKey = "YOUR_SDK_KEY",
    playerId = "player_123"       // Unique id of the player in your app
)

offerwall.onReward = { reward ->
    val amount = reward["amount"]
    val txid = reward["txid"]
    Log.d("AppDiscovery", "Reward: $amount (transaction $txid)")
}
offerwall.onClose = { Log.d("AppDiscovery", "Offerwall closed") }

offerwall.launch(this)
```

Java:

```java
import com.appdiscoverysdk.AppDiscovery;
import com.appdiscoverysdk.Offerwall;

Offerwall offerwall = AppDiscovery.INSTANCE.create(
    "offers.example.com", "YOUR_APP_ID", "YOUR_SDK_KEY", "player_123", null);
offerwall.setOnReward(reward -> {
    Log.d("AppDiscovery", "Reward: " + reward.get("amount"));
    return null;
});
offerwall.launch(this);
```

## API

### AppDiscovery

| Method | Parameters | Description |
|---|---|---|
| `create()` | `host`, `appId`, `sdkKey`, `playerId`, `trackerHost = null` | Creates an `Offerwall`. `host` is a plain host name (an `https://` URL is accepted); throws `IllegalArgumentException` when blank or invalid. `trackerHost` is only needed when reward sync runs on a different host than the offerwall. |
| `syncPendingRewards()` | `host`, `appId`, `sdkKey`, `playerId`, `trackerHost = null`, `callback` | Delivers rewards earned while the app was closed, without opening the offerwall. |

### Offerwall

| Member | Description |
|---|---|
| `launch(activity)` | Opens the offerwall and syncs pending rewards. |
| `onReward` | `((Map<String, Any?>) -> Unit)?` called for every reward. |
| `onClose` | `(() -> Unit)?` called when the offerwall closes. |
| `tags`, `options` | Optional maps for custom tags and display options. |
| `syncPendingRewards(callback)` | Sync pending rewards for this player. |

### Reward fields

| Field | Description |
|---|---|
| `amount` / `payout` | Reward amount |
| `txid` | Unique transaction id, use it to avoid crediting twice |
| `status` | `approved`, `pending`, `reversed` or `rejected` |
| `player_id` | The player id you passed in |
| `offer_id`, `offer_name` | The completed offer, when available |

Any additional parameters sent by the server are preserved in the map.

## Troubleshooting

- **Zero offers shown:** check that the package name matches the one registered for your `appId`, that `appId`, `sdkKey` and `host` are correct, and that `playerId` is not empty.
- **`IllegalArgumentException` on `create`:** `host` must be a plain host name such as `offers.example.com`.

## License

MIT, see [LICENSE](LICENSE).
