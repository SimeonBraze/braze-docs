---
nav_title: Refreshing the Feed
article_title: Refeshing the Content Card Feed for Web
page_order: 3
platform: Web
channel: content cards
page_type: reference
description: "This reference article describes how to queue a manual refresh of your Content Cards."

---

# Refreshing Content Cards

You can queue a manual refresh of the Braze Content Cards at any time by calling [`requestContentCardsRefresh()`](https://js.appboycdn.com/web-sdk/latest/doc/modules/appboy.html#requestcontentcardsrefresh). 

You can also call [`getCachedContentCards`](https://js.appboycdn.com/web-sdk/latest/doc/module-appboy.html#.getCachedContentCards) to get all currently available cards from the last Content Cards refresh. 

The feed will refresh automatically on new session or when the feed is opened and more than 60 seconds have elapsed since the last refresh.
