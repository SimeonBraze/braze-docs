---
nav_title: Initial SDK Setup
article_title: Initial SDK Setup for Web
platform: Web
page_order: 0
page_type: reference
description: "This article covers initial SDK setup for the Braze Web SDK."

---

# Initial SDK setup

The Braze Web SDK lets you collect analytics and display rich in-app messages, push, and Content Card messages to your web users.

Please see our [JavaScript Documentation][9] for a complete technical reference.

## Step 1: Install the Braze library

There are three easy ways to integrate the Web SDK to include analytics and messaging components on your site. Be sure to view our [Push integration guide][16] if you plan to use Web push features. 

If your website uses a `Content-Security-Policy`, then follow our [CSP Header Guide][19] in addition to the integration steps below.

### Option 1: NPM or Yarn {#install-npm}

If your site uses NPM or Yarn package managers, you can add the [Braze NPM package](https://www.npmjs.com/package/@braze/web-sdk) as a dependency.

Typescript definitions are now included as of v3.0.0. For notes on upgrading from 2.x to 3.x, see our [Changelog][17].

```bash
npm install --save @braze/web-sdk
# or, using yarn:
# yarn add @braze/web-sdk
```

Once installed, you can `import` or `require` the library in the typical fashion:

```javascript
import appboy from "@braze/web-sdk";
// or, using `require`
const appboy = require("@braze/web-sdk");
```

### Option 2: Google tag manager {#install-gtm}

The Braze Web SDK can be quickly installed from the Google Tag Manager Template Library. Two tags are supported:

1. Initialization tag: loads the Web SDK onto your website and optionally sets the External User ID.
2. Actions tag: used to trigger custom events, purchases, change user IDs, or toggle SDK tracking.

Visit the [Google Tag Manager integration guide][18] for more information.

### Option 3: Braze CDN {#install-cdn}

Add the Braze Web SDK directly to your HTML by referencing our CDN-hosted script, which loads the library asynchronously.

```javascript
<script type="text/javascript">
  +function(a,p,P,b,y){a.appboy={};a.appboyQueue=[];for(var s="DeviceProperties BrazeSdkMetadata BrazeSdkMetadata.GOOGLE_TAG_MANAGER BrazeSdkMetadata.MPARTICLE BrazeSdkMetadata.SEGMENT BrazeSdkMetadata.TEALIUM BrazeSdkMetadata.NPM BrazeSdkMetadata.CDN BrazeSdkMetadata.MANUAL Card Card.prototype.dismissCard Card.prototype.removeAllSubscriptions Card.prototype.removeSubscription Card.prototype.subscribeToClickedEvent Card.prototype.subscribeToDismissedEvent Card.fromContentCardsJson Banner CaptionedImage ClassicCard ControlCard ContentCards ContentCards.prototype.getUnviewedCardCount Feed Feed.prototype.getUnreadCardCount ControlMessage InAppMessage InAppMessage.SlideFrom InAppMessage.ClickAction InAppMessage.DismissType InAppMessage.OpenTarget InAppMessage.ImageStyle InAppMessage.Orientation InAppMessage.TextAlignment InAppMessage.CropType InAppMessage.prototype.closeMessage InAppMessage.prototype.removeAllSubscriptions InAppMessage.prototype.removeSubscription InAppMessage.prototype.subscribeToClickedEvent InAppMessage.prototype.subscribeToDismissedEvent InAppMessage.fromJson FullScreenMessage ModalMessage HtmlMessage SlideUpMessage User User.Genders User.NotificationSubscriptionTypes User.prototype.addAlias User.prototype.addToCustomAttributeArray User.prototype.addToSubscriptionGroup User.prototype.getUserId User.prototype.incrementCustomUserAttribute User.prototype.removeFromCustomAttributeArray User.prototype.removeFromSubscriptionGroup User.prototype.setAvatarImageUrl User.prototype.setCountry User.prototype.setCustomLocationAttribute User.prototype.setCustomUserAttribute User.prototype.setDateOfBirth User.prototype.setEmail User.prototype.setEmailNotificationSubscriptionType User.prototype.setFirstName User.prototype.setGender User.prototype.setHomeCity User.prototype.setLanguage User.prototype.setLastKnownLocation User.prototype.setLastName User.prototype.setPhoneNumber User.prototype.setPushNotificationSubscriptionType InAppMessageButton InAppMessageButton.prototype.removeAllSubscriptions InAppMessageButton.prototype.removeSubscription InAppMessageButton.prototype.subscribeToClickedEvent display display.automaticallyShowNewInAppMessages display.destroyFeed display.hideContentCards display.showContentCards display.showFeed display.showInAppMessage display.toggleContentCards display.toggleFeed changeUser destroy getDeviceId initialize isPushBlocked isPushGranted isPushPermissionGranted isPushSupported logCardClick logCardDismissal logCardImpressions logContentCardsDisplayed logCustomEvent logFeedDisplayed logInAppMessageButtonClick logInAppMessageClick logInAppMessageHtmlClick logInAppMessageImpression logPurchase openSession registerAppboyPushMessages removeAllSubscriptions removeSubscription requestContentCardsRefresh requestFeedRefresh requestImmediateDataFlush resumeWebTracking enableSDK isDisabled setLogger setSdkAuthenticationSignature addSdkMetadata stopWebTracking disableSDK subscribeToContentCardsUpdates subscribeToFeedUpdates subscribeToInAppMessage subscribeToNewInAppMessages subscribeToSdkAuthenticationFailures toggleAppboyLogging trackLocation unregisterAppboyPushMessages wipeData".split(" "),i=0;i<s.length;i++){for(var m=s[i],k=a.appboy,l=m.split("."),j=0;j<l.length-1;j++)k=k[l[j]];k[l[j]]=(new Function("return function "+m.replace(/\./g,"_")+"(){window.appboyQueue.push(arguments); return true}"))()}window.appboy.getCachedContentCards=function(){return new window.appboy.ContentCards};window.appboy.getCachedFeed=function(){return new window.appboy.Feed};window.appboy.getUser=function(){return new window.appboy.User};(y=p.createElement(P)).type='text/javascript';
    y.src='https://js.appboycdn.com/web-sdk/3.5/appboy.min.js';
    y.async=1;(b=p.getElementsByTagName(P)[0]).parentNode.insertBefore(y,b)
  }(window,document,'script');
</script>
```
## Step 2: Initialize Braze

Once the Braze Web SDK is added to your website, initialize the library with the `API Key` and [SDK Endpoint URL]({{site.baseurl}}/user_guide/administrative/access_braze/sdk_endpoints) found in **Manage Settings > Settings** within your Braze dashboard.

{% alert note %}
If you've configured your Braze initialization options in a Tag Manager, you can skip this step.
{% endalert %}

For a complete list of options for `appboy.initialize()` see our [JavaScript documentation](https://js.appboycdn.com/web-sdk/latest/doc/module-appboy.html#.initialize).

```javascript
// initialize the SDK
appboy.initialize('YOUR-API-KEY-HERE', {
    baseUrl: "YOUR-SDK-ENDPOINT-HERE"
});

// optionally show all in-app messages without custom handling
appboy.display.automaticallyShowNewInAppMessages();

// optionally set the current user's External ID
if (isLoggedIn){
    appboy.changeUser(userIdentifier);
}

// start (or continue) a session
appboy.openSession();
```

Please see our [JavaScript reference documentation][9] for all other JavaScript methods.

{% alert note %}
Anonymous users on mobile or web devices may be counted towards your [MAU]({{site.baseurl}}/user_guide/data_and_analytics/your_reports/understanding_your_app_usage_data/#monthly-active-users). As a result, you may want to conditionally load or initialize the SDK to exclude these users from your MAU count.
{% endalert %}

## Step 3: Web push (optional)

Additional setup is required to use Web push notifications. See [Push notifications][16] for instructions.

## Troubleshooting {#error-logging}

To assist in troubleshooting, you can enable verbose logging in the SDK. This is useful for development but is visible to all users, so you should remove this option or provide an alternate logger with `appboy.setLogger()` in your production environment.

```javascript
appboy.initialize("YOUR-API-KEY-HERE", {
    baseUrl: "",
    enableLogging: true
});

// or, after initialization:

appboy.toggleAppboyLogging()
```

## Upgrading the SDK

When you reference the Braze Web SDK from our content delivery network, for example, `https://js.appboycdn.com/web-sdk/a.a/appboy.min.js` (as recommended by our default integration instructions), your users will receive minor updates (bug fixes and backward compatible features, versions `a.a.a` through `a.a.z` in the above examples) automatically when they refresh your site. 

However, when we release major changes, we require you to upgrade the Braze Web SDK manually to ensure that nothing in your integration will be impacted by any breaking changes. Additionally, if you download our SDK and host it yourself, you won't receive any version updates automatically and should upgrade manually to receive the latest features and bug fixes.

You can keep up-to-date with our latest release [following our release feed](https://github.com/Appboy/appboy-web-sdk/tags.atom) with the RSS Reader or service of your choice, and see [our changelog](https://github.com/Appboy/appboy-web-sdk/blob/master/CHANGELOG.md) for a full accounting of our Web SDK release history. To upgrade the Braze Web SDK:

- Update the Braze library version by changing the version number of `https://js.appboycdn.com/web-sdk/[OLD VERSION NUMBER]/appboy.min.js`, or in your package manager's dependencies.
- If you have web push integrated, update the service worker file on your site - by default, this is located at `/service-worker.js` at your site's root directory, but the location may be customized in some integrations. You must access the root directory to host a service worker file. 

These two files must be updated in coordination with each other to ensure proper functionality.

## Alternative integration methods

### AMD module loader
If you are using Google Tag Manager alongside an AMD module loader such as RequireJS to load Braze's SDK you will need to use the RequireJS-compatible integration snippet in your `<head>` tag.

Check out the appropriate section of our [Braze Web SDK GitHub Repository][2] for more details.

### Tealium iQ
Tealium iQ offers a basic turnkey Braze integration. To configure the integration, search for Braze in the Tealium Tag Management interface, and provide the Web SDK API key from your dashboard.

For more details or in-depth Tealium configuration support, check out our [integration documentation]({{site.baseurl}}/partners/data_and_infrastructure_agility/customer_data_platform/tealium/#about-tealium) or reach out to your Tealium Account Manager.

### Other tag managers
Braze may also be compatible with other tag management solutions by following our integration instructions within a custom HTML tag. Please reach out to a Braze representative if you need help evaluating these solutions.

[2]: https://github.com/Appboy/appboy-web-sdk#getting-started "Braze Web SDK GitHub Repository"
[9]: https://js.appboycdn.com/web-sdk/latest/doc/module-appboy.html "JSDocs"
[16]: {{site.baseurl}}/developer_guide/platform_integration_guides/web/push_notifications/integration/
[17]: https://github.com/Appboy/appboy-web-sdk/blob/master/CHANGELOG.md#300
[18]: {{site.baseurl}}/developer_guide/platform_integration_guides/web/google_tag_manager/
[19]: {{site.baseurl}}/developer_guide/platform_integration_guides/web/content_security_policy/
<!-- wesley wanted an empty line at the end -->