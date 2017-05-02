---
layout: post
title:  "Super simple: Progressive rollouts"
date:   2017-05-02 18:00:00 +0000
categories: ios firefox activity-streams tools super-simple
---
![A pretty header image]({{site_url}}/images/windmill-ship-in-the-distance.png)

We've been working on a number of [enormous features][5] recently. This short post isn't about them, but rather a tiny utility we're using to mitigate some of the risk of rolling out such large code changes.

Many developers are already using [feature flags][6]: we use them quite extensively to maintain a stable master branch, while still allowing the flexibility to land new features on that branch.

In the case of Firefox for iOS, we have an `AppConstants.swift` class, with a set of `static Bool` constants which allow control over which channels have the feature enabled. 

```swift
    ///  Enables/disables the activity stream
    public static let MOZ_AS_PANEL: Bool = {
        #if MOZ_CHANNEL_RELEASE
            return false
        #elseif MOZ_CHANNEL_BETA
            return false
        #elseif MOZ_CHANNEL_NIGHTLY
            return true
        #elseif MOZ_CHANNEL_FENNEC
            return true
        #elseif MOZ_CHANNEL_AURORA
            return true
        #else
            return true
        #endif
    }()
```

Where the feature is actually implemented, we do a simple check before executing the code:

```swift
    if AppConstants.MOZ_AS_PANEL {
        // use the new activity stream panel
    } else {
        // use the old top sites panel
    }
```

This is great for development work, and works if you have enough beta testers, but if you haven't enough beta testers, this is still a risky scheme: you have to flip on your feature for all release users at once.

For our [activity stream][5] work: we want to roll out the feature to only a small tranche of our release users.

Other approaches to the same problem might use [Firebase Remote Config][1] or [Facebook's Gatekeeper][2], to allow a remotely configurable switch. Both these solutions offer ways of progressively rolling out new features to a proportion of users.

We wanted something simpler. We didn't want to integrate a new SDK, we release fairly often, and we wanted to add as little complexity as we could to make achieve the effect. 

So, introducing a class called [`FeatureSwitch`][4] we're able to switch on a proportion of our release users, and steadily increase it as our confidence in the stability of the feature increases.

```swift
/// Steadily growing set of feature switches controlling access to features by populations of Release users.
open class FeatureSwitches {
    open static let activityStream =
        FeatureSwitch(named: "activity_stream", AppConstants.MOZ_AS_PANEL, allowPercentage: 10)
}
```

The feature switch generates and stores a random number between 0 and 100. When we check if the feature is allowed, we pass in the preference store, and then re-calculate whether or not the user should get the new feature.

At the implementation point, we've changed the check to use the `FeatureSwitch` instead.

```swift
    if FeatureSwitches.activityStream.isMember(profile.prefs) {
        // use the new activity stream panel
    } else {
        // use the old top sites panel
    }
```

From experience, we've found that switching code and probablistic code like this has a few important corner cases which are easy to get wrong, and disasterous when it fails, so we've covered the code with more [lines of testing code][3] than code itself.

Since socializing the code internally, we have a number of our product managers who want to roll out their features with `FeatureSwitch`es, so we will be using it more.

We don't think there is enough yet to make a standalone project to put into [Cocoapods][7] or [Carthage][8]. Because we were trying to minimize risk and complexity, we deliberately opted not to make something too standalone or general purpose. However, if you like the sound of some of the ideas here, you can find [the code][4] online.

As with all of our code, it's all open source, so please feel free to take it, adapt it, and use it as you will. We'd love to hear from you if you do, or you have any feedback about it.

[1]: https://firebase.google.com/docs/remote-config/
[2]: https://www.quora.com/How-does-Facebooks-Gatekeeper-service-work
[3]: https://github.com/mozilla-mobile/firefox-ios/blob/master/SharedTests/FeatureSwitchTests.swift
[4]: https://github.com/mozilla-mobile/firefox-ios/blob/master/Shared/FeatureSwitch.swift
[5]: https://testpilot.firefox.com/experiments/activity-stream
[6]: https://www.martinfowler.com/articles/feature-toggles.html
[7]: https://cocoapods.org/
[8]: https://github.com/Carthage/Carthage
