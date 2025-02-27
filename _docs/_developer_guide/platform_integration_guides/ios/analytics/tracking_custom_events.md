---
nav_title: Tracking Custom Events
article_title: Tracking Custom Events for iOS
platform: iOS
page_order: 2
description: "This reference article covers how to add and track custom events for your iOS application."

---

# Tracking custom events for iOS

You can record custom events in Braze to learn more about your app's usage patterns and to segment your users by their actions on the dashboard.

Before implementation, be sure to review examples of the segmentation options afforded by custom events, custom attributes, and purchase events in our [Best practices][0], as well as our notes on [event naming conventions]({{site.baseurl}}/user_guide/data_and_analytics/custom_data/event_naming_conventions/).

## Adding a custom event

{% tabs %}
{% tab OBJECTIVE-C %}

```objc
[[Appboy sharedInstance] logCustomEvent:@"YOUR_EVENT_NAME"];
```

{% endtab %}
{% tab swift %}

```swift
Appboy.sharedInstance()?.logCustomEvent("YOUR_EVENT_NAME")
```

{% endtab %}
{% endtabs %}

### Adding properties

You can add metadata about custom events by passing an `NSDictionary` populated with `NSNumber`, `NSString`, or `NSDate` values.

{% tabs %}
{% tab OBJECTIVE-C %}

```objc
[[Appboy sharedInstance] logCustomEvent:@"YOUR_EVENT_NAME" withProperties:@{@"key1":"value1"}];
```

{% endtab %}
{% tab swift %}

```swift
Appboy.sharedInstance()?.logCustomEvent("YOUR_EVENT_NAME", withProperties:["key1":"value1"])
```

{% endtab %}
{% endtabs %}

See our [class documentation][4] for more information.

### Reserved keys {#event-reserved-keys}

The following keys are **RESERVED** and **CANNOT** be used as custom event properties:

- `time`
- `event_name`

**Additional Information**

- See the method declaration within the [`Appboy.h` file][2]. - In addition, you may refer to the [logCustomEvent Documentation][3] for more information.

[0]: {{site.baseurl}}/developer_guide/platform_wide/analytics_overview/#user-data-collection
[2]: https://github.com/Appboy/appboy-ios-sdk/blob/master/AppboyKit/include/Appboy.h
[3]: http://appboy.github.io/appboy-ios-sdk/docs/interface_appboy.html#ad80c39e8c96482a77562a5b1a1d387aa "logcustomevent documentation"
[4]: http://appboy.github.io/appboy-ios-sdk/docs/interface_appboy.html#a4f0051d73d85cb37f63c232248124c79 "logcustomevent:withproperties documentation"
