---
nav_title: Customization
article_title: In-App Message Customization for Web
platform: Web
channel: in-app messages
page_order: 3
page_type: reference
description: "This article covers customization of in-app messaging via the Braze SDK."

---

# Customization {#in-app-message-customization}

All of Braze's in-app message types are highly customizable across messages, images, [Font Awesome][15] icons, click actions, analytics, editable styling, custom display options, and custom delivery options. Multiple options can be configured on a per in-app message basis from within the [dashboard]({{site.baseurl}}/user_guide/message_building_by_channel/in-app_messages/create/). Braze additionally provides multiple levels of advanced customization to satisfy a variety of use cases and needs.

## Key-value pair extras

In-app message objects may carry key-value pairs as their `extras` property. These are specified on the dashboard under **Settings** when creating an in-app message campaign. These can be used to send data with an in-app message for further handling by your site. For example:

```javascript
import braze from "@braze/web-sdk";

braze.subscribeToInAppMessage(function(inAppMessage) {
  if (inAppMessage instanceof braze.InAppMessage) {
    var extras = inAppMessage.extras;
    if (extras) {
      for (var key in extras) {
        console.log("key: " + key + ", value: " + extras[key]);
      }
    }
  }
  braze.display.showInAppMessage(inAppMessage);
});
```

## In-app message default z-index

By default, in-app messages are displayed using `z-index: 1050`. This is configurable using the `inAppMessageZIndex ` [initialization option][41] in the scenario that your website styles elements with higher values than that.

```javascript
import braze from "@braze/web-sdk";
braze.initialize("YOUR-API-KEY", {
    baseUrl: "YOUR-API-ENDPOINT",
    inAppMessageZIndex: 9001
});
```

{% alert important %}
This option was introduced in Web SDK v3.3.0. Older SDKs must be upgraded to use this option.
{% endalert %}

## Custom styling

Braze UI elements come with a default look and feel that create a neutral in-app message experience and aim for consistency with other Braze mobile platforms. Braze's default styles are defined in CSS within the Braze SDK. By overriding selected styles in your application, you can customize our standard in-app message types with your own background images, font families, styles, sizes, animations, and more. For instance, the following is an example override that will cause an in-app message's headers to appear italicized:

```css
  body .ab-in-app-message .ab-message-header {
    font-style: italic;
  }
```

See the [JSDocs][2] for more information.

## Open message link in new tab

To set your in-app message links to open in a new tab, set the `openInAppMessagesInNewTab` option to `true` to force all links from in-app message clicks open in a new tab or window.

```javascript
appboy.initialize('api-key', { openInAppMessagesInNewTab: true} );
```

## In-app message dismissal

By default, when an in-app message is showing, pressing the escape button or a click on the greyed-out background of the page will dismiss the message. Configure the `requireExplicitInAppMessageDismissal` [initialization option][41] to `true` to prevent this behavior and require an explicit button click to dismiss messages. 

```javascript
import braze from "@braze/web-sdk";
braze.initialize("YOUR-API-KEY", {
    baseUrl: "YOUR-API-ENDPOINT",
    requireExplicitInAppMessageDismissal: true
});
```

[2]: https://js.appboycdn.com/web-sdk/latest/doc/ab.InAppMessage.html
[15]: https://fontawesome.com/?from=io
[41]: https://js.appboycdn.com/web-sdk/latest/doc/modules/appboy.html#initializationoptions
