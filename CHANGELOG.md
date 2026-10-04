# Changelog

## 1.0.0

- First public release.
- `AppDiscovery.create(host, appId, sdkKey, playerId, trackerHost?)` returns an `Offerwall`; the offerwall host is a required setting, there is no default.
- `Offerwall.launch(activity)`, `onReward` / `onClose` callbacks, `tags` and `options`.
- `Offerwall.syncPendingRewards` and `AppDiscovery.syncPendingRewards` for rewards earned while the app was closed.
- minSdk 21, Java 17.
