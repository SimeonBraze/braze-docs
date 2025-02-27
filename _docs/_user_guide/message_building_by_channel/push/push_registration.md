---
nav_title: "Push Registration"
article_title: Push Registration
page_order: 2
page_type: reference
description: "This reference article discusses what it means to be registered for push and how we send push messages and deal with push tokens at Braze."
channel:
  - push

---

# Push registration

> This article covers the process through which a user gets assigned a push token, and how Braze sends push messages to your users.

## Push tokens

Understanding push tokens and what they are is a fundamental piece of understanding how we send push messages here at Braze. A push token is an identifier that senders use to target specific devices with a push notification. If a device doesn't have a push token, there is no way to send push to it.

Push tokens are generated by push service providers. Braze connects with push service providers like Firebase Cloud Messaging Service (FCMs) for Android and Apple Push Notification Service (APNs) for iOS, and those providers send unique device tokens that identify your app. Each device has one, unique token that is used for messaging. Tokens can [expire](#push-token-expire) or be updated.

## Push token registration

Platforms deal with push token registration in different ways:

- **Android**: Automatically registered for push. Receives a token as soon as a user downloads and opens an application.
- **iOS**: Not automatically registered for push.
    - **iOS 12 (with Provisional Authorization)**: <br>If you have [provisional authorization]({{site.baseurl}}/user_guide/message_building_by_channel/push/ios/notification_options/#provisional-push) set up, you can send notifications silently to the Notification Center of the device. These notifications may prompt users to decide to continue to receive notifications. Their push subscription state depends on the user's response to this prompt. 
    - **iOS 11 and later & iOS 12 (without Provisional Authorization)**: <br>The native push notification prompt appears for new users to an application. Users respond to this prompt by selecting allow or deny. **Deny** denies push token registration, therefore denying push notifications to their phone from that application. **Allow** registers and creates a new push token, then and there.
- **Web:** Not automatically registered for push. The native push notification prompt appears for new users to your website. Users respond to this prompt by selecting allow or block. **Block** denies push token registration, therefore denying push notifications to their phone from that application. **Allow** registers and creates a new push token, then and there.

| Get a push token | Send a push token |
| ---------------- | ----------------- |
| Applications must register with a push provider in order to get a push token for a device. | Developers can then target the device using the push token generated by FCM/APNs.|
{: .reset-td-br-1 .reset-td-br-2}

### Check user's push subscription state

![Push Example]({% image_buster /assets/img/push_example.png %}){: style="float:right;max-width:35%;margin-left:15px;"}

There are two ways you can check a user's push subscription state with Braze:

1. **User Profile:** You can access individual user profiles through the Braze dashboard on the **User Search** page. Here, you can look up user profiles by email address, phone number, or external user ID. Once in a user profile, you can select the **Engagement** tab to view and manually adjust a user's subscription state. <br><br>
2. **Rest API Export:** You can export individual user profiles in JSON format using the export [Users by segment][segment] or [Users by identifier][identifier] endpoints. Braze will return a push tokens object that contains push enablement information per device.

## Push token management

Check out the chart below for actions that lead to push tokens changes or removal from user profiles. 

| Action | Description |
| ------ | ----------- |
| `changeUser()` method called | The Braze `changeUser()` method switches the user ID that the SDKs are assigning user behavior data to. This method is usually called when a user logs into an application. When `changeUser()` is called with a different or new user ID on a specific device, that device's push token will be moved to the appropriate Braze profile with corresponding user ID. |
| Push error occurs | Some common push errors that lead to token removal include `MismatchSenderId`, `InvalidRegistration`, and other types of push bounces. <br><br>Check out our full list of common [push errors][errors]. |
| User uninstalls | When a user uninstalls the application from a device, Braze will remove the user's push token from the profile. |
{: .reset-td-br-1 .reset-td-br-2}

### What does this look like on a broader scale?

When a user opens a new application, if push has been configured correctly, a call is made from the Braze SDK to the push providers. When that call is made, the push provider runs a check to see if everything is set up correctly. If so, a push token gets passed into your device. When that token arrives, the SDK communicates this to Braze. Once Braze has received the token from the push provider, we update or create a new user profile. These users are now considered registered.

If we want to launch a campaign, we create a campaign in Braze that generates a push payload to send to the push provider. From there, the provider delivers the push payload to the user's device and the SDK passes the messaging state to Braze.

![Picture][push-process]

| Registration steps | Messaging steps |
| ------------------ | --------------- |
| 1. Customer (device) registers to push provider<br>2. Provider generates and delivers push token<br>3. Flush tokens in Braze |1. Braze sends push payload to provider<br>2. Provider delivers the push payload to the device<br>3. SDK passes messaging stats to Braze |
{: .reset-td-br-1 .reset-td-br-2}

## Frequently asked questions

### What happens when an opted-in user deletes and then redownloads my app?

Suppose a user opts-in for push, receives some push messaging, and then later deletes the app. This will remove the push consent at the device level. From here, the first bounced push after the uninstall will automatically result in that user being opted-out of future push messaging. After this, if a user were to reinstall the app but not launch it, Braze won't be able to send a push to the user because push tokens have not been re-granted for your app.

Additionally, if a user were to re-enable foreground push, it would require a session start to update this information in their user profile to begin receiving push messaging.

### Under what circumstances do push tokens expire? {#push-token-expire}

Unfortunately, APNs and FCM don't really define this. Push tokens can expire when an app is updated, when users transfer their data to a new device, or when they re-install an operating system. For the most part, we don't really have insight into why push providers will expire certain push tokens.

To account for that ambiguity, our SDK push integrations always register and flush tokens on session start to ensure we have the most up-to-date token.


[errors]: {{site.baseurl}}/help/help_articles/push/push_error_codes/#push-bounced-mismatchsenderid
[identifier]: {{site.baseurl}}/api/endpoints/export/user_data/post_users_identifier/
[segment]: {{site.baseurl}}/api/endpoints/export/user_data/post_users_segment/
[push-process]: {% image_buster /assets/img/push_process.png %}
