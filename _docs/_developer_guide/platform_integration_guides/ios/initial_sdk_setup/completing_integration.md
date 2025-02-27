---
nav_title: Completing the Integration
article_title: Completing the iOS SDK Integration
platform: iOS
description: "This reference article shows how to finish integrating the Braze SDK after installing it via one of the integration options."
page_order: 2

---

# Completing the integration

## Requirements

Before following these steps, make sure you have integrated the SDK using one of the installation options listed before this page.

## Step 1: Update your app delegate

{% tabs %}
{% tab OBJECTIVE-C %}

If you are integrating the Braze SDK with CocoaPods, Carthage, or with a [dynamic manual integration]({{site.baseurl}}/developer_guide/platform_integration_guides/ios/initial_sdk_setup/installation_methods/manual_integration_options/), add the following line of code to your `AppDelegate.m` file:

```objc
#import "Appboy-iOS-SDK/AppboyKit.h"
```

If you are integrating with Swift Package Manager or with a [static manual integration]({{site.baseurl}}/developer_guide/platform_integration_guides/ios/initial_sdk_setup/installation_methods/manual_integration_options/), use this line instead:

```objc
#import "AppboyKit.h"
```

Within your `AppDelegate.m` file, add the following snippet within your `application:didFinishLaunchingWithOptions:` method:

```objc
[Appboy startWithApiKey:@"YOUR-APP-IDENTIFIER-API-KEY"
          inApplication:application
      withLaunchOptions:launchOptions];
```

{% endtab %}
{% tab swift %}

If you are integrating the Braze SDK with CocoaPods, Carthage, or with a [dynamic manual integration]({{site.baseurl}}/developer_guide/platform_integration_guides/ios/initial_sdk_setup/installation_methods/manual_integration_options/), add the following line of code to your `AppDelegate.swift` file:

```swift
import Appboy_iOS_SDK
```

If you are integrating with Swift Package Manager or with a [static manual integration]({{site.baseurl}}/developer_guide/platform_integration_guides/ios/initial_sdk_setup/installation_methods/manual_integration_options/), use this line instead:

```swift
import AppboyKit
```

For more information about using Objective-C code in Swift projects, please see the [Apple Developer Docs](https://developer.apple.com/library/ios/documentation/swift/conceptual/buildingcocoaapps/MixandMatch.html).

In `AppDelegate.swift`, add following snippet to your `application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool`:

```swift
Appboy.start(withApiKey: "YOUR-APP-IDENTIFIER-API-KEY", in:application, withLaunchOptions:launchOptions)
```

{% endtab %}
{% endtabs %}

**Note**: Braze's `sharedInstance` singleton will be nil before `startWithApiKey:` is called, as that is a prerequisite to using any Braze functionality.

{% alert important %}
Be sure to update `YOUR-APP-IDENTIFIER-API-KEY` with the correct value from your **Settings** page. For more information on where to find your App Identifier API key, check out our [API documentation]({{site.baseurl}}/api/api_key/#the-app-identifier-api-key).
{% endalert %}

{% alert warning %}
Be sure to initialize Braze in your application's main thread. Initializing asynchronously can lead to broken functionality.
{% endalert %}


## Step 2: Specify your data cluster

{% alert note %}
Note that as of December 2019, custom endpoints are no longer given out, if you have a pre-existing custom endpoint, you may continue to use it. For more details, refer to our <a href="{{site.baseurl}}/api/basics/#endpoints">list of available endpoints</a>.
{% endalert %}

### Compile-time endpoint configuration (recommended)

If given a pre-existing custom endpoint...
- Starting with Braze iOS SDK v3.0.2, you can set a custom endpoint using the `Info.plist` file. Add the `Braze` dictionary to your `Info.plist` file. Inside the `Braze` dictionary, add the `Endpoint` string subentry and set the value to your custom endpoint URL's authority (for example, `sdk.iad-01.braze.com`, not `https://sdk.iad-01.braze.com`). Note that prior to Braze iOS SDK v4.0.2, the dictionary key `Appboy` must be used in place of `Braze`.

Your Braze representative should have already advised you of the [correct endpoint]({{site.baseurl}}/user_guide/administrative/access_braze/sdk_endpoints/).

### Runtime endpoint configuration

If given a pre-existing custom endpoint...
- Starting with Braze iOS SDK v3.17.0+, you can override set your endpoint via the `ABKEndpointKey` inside the `appboyOptions` parameter passed to `startWithApiKey:inApplication:withLaunchOptions:withAppboyOptions:`. Set the value to your custom endpoint URL's authority (for example, `sdk.iad-01.braze.com`, not `https://sdk.iad-01.braze.com`).

{% alert important %}
To find out your specific cluster, please ask your Customer Success Manager or reach out to our support team.
{% endalert %}

## SDK integration complete

Braze should now be collecting data from your application and your basic integration should be complete. {% if include.platform == 'iOS' %}Please see the following sections in order to enable custom event tracking, push messaging, the news-feed and the complete suite of Braze features.{% endif %}

## Customizing Braze on startup

If you wish to customize Braze on startup, you can instead use the Braze initialization method `startWithApiKey:inApplication:withLaunchOptions:withAppboyOptions:` and pass in an optional `NSDictionary` of Braze startup keys.
{% tabs %}
{% tab OBJECTIVE-C %}

In your `AppDelegate.m` file, within your `application:didFinishLaunchingWithOptions:` method, add the following Braze method:

```objc
[Appboy startWithApiKey:@"YOUR-APP-IDENTIFER-API-KEY"
          inApplication:application
      withLaunchOptions:launchOptions
      withAppboyOptions:appboyOptions];
```

{% endtab %}
{% tab swift %}

In `AppDelegate.swift`, within your `application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool` method, add the following Braze method:

```swift
Appboy.start(withApiKey: "YOUR-APP-IDENTIFIER-API-KEY",
                 in:application,
                 withLaunchOptions:launchOptions,
                 withAppboyOptions:appboyOptions)
```

where `appboyOptions` is a `Dictionary` of startup configuration values.

{% endtab %}
{% endtabs %}

**Note**: This method would replace the `startWithApiKey:inApplication:withLaunchOptions:` initialization method from above.

This method is called with the following parameters:

- `YOUR-APP-IDENTIFIER-API-KEY` – Your [App Identifier]({{site.baseurl}}/api/api_key/#the-app-identifier-api-key) API Key from the Braze dashboard.
- `application` – The current app
- `launchOptions` – The options `NSDictionary` that you get from `application:didFinishLaunchingWithOptions:`
- `appboyOptions` – An optional `NSDictionary` with startup configuration values for Braze

See [Appboy.h][1] for a list of Braze startup keys.

## Appboy.sharedInstance() and Swift nullability
Differing somewhat from common practice, the `Appboy.sharedInstance()` singleton is optional. The reason for this is that, as noted above, `sharedInstance` is `nil` before `startWithApiKey:` is called, and there are some non-standard but not-invalid implementations in which a delayed initialization can be used.

If you call `startWithApiKey:` in your `didFinishLaunchingWithOptions:` delegate before any access to Appboy's `sharedInstance` (the standard implementation), you can use optional chaining, like `Appboy.sharedInstance()?.changeUser("testUser")`, to avoid cumbersome checks. This will have parity with an Objective-C implementation that assumed a non-null `sharedInstance`.

## Documentation

[Full iOS class documentation][2] is available to provide additional guidance on any of the SDK's methods.

[1]: https://github.com/braze-inc/braze-ios-sdk/blob/master/AppboyKit/include/Appboy.h
[2]: http://appboy.github.io/appboy-ios-sdk/docs/annotated.html "full iOS class documentation"
