---
nav_title: Customization
article_title: Content Card Customization for Android/FireOS
page_order: 2
platform: 
  - Android
  - FireOS
description: "This article covers customization options for your Content Cards in your Android application."
channel:
  - content cards

---

# Customization

Customizing Content Cards and the feed they are located in must be done during in the integration process. Before customizing, developers should work with their marketing team to determine what customization approach works best for your brand needs. At Braze, we highlight three approaches to customization based on the associated level of effort and flexibility provided: crawl, walk, or run. Learn more about these [customization approaches][1] in our user guide.

## Default styling {#default-styling-for-android}

Braze in-app messages and Content Cards come with a default look and feel that matches the Android standard UI guidelines and provide a seamless experience. You can see these default styles in the [`res/values/styles.xml`][42] file in the Braze SDK distribution.

```xml
  <!-- Content Cards Example -->
  <style name="Braze.ContentCards.CaptionedImage.Description">
    <item name="android:textColor">@color/com_appboy_description</item>
    <item name="android:textSize">15.0sp</item>
    <item name="android:includeFontPadding">false</item>
    <item name="android:paddingBottom">8.0dp</item>
    <item name="android:layout_marginLeft">10.0dp</item>
    <item name="android:layout_marginRight">10.0dp</item>
    <item name="android:layout_marginTop">8.0dp</item>
    <item name="android:layout_width">match_parent</item>
    <item name="android:layout_below">@id/com_appboy_content_cards_captioned_image_card_title_container</item>
  </style>
```

## Overriding styles {#overriding-styles-for-android}

If you would prefer, you can override these styles to create a look and feel that better suits your app. To override a style, copy it in its entirety to the `styles.xml` file in your project and make modifications. The whole style must be copied over to your local `styles.xml` file in order for all of the attributes to be correctly set.

### Correct style override {#correct-style-override-for-android}

```xml
<style name="Braze.ContentCardsDisplay">
  <item name="android:background">@color/mint</item>
  <item name="android:cacheColorHint">@color/mint</item>
  <item name="android:divider">@android:color/transparent</item>
  <item name="android:dividerHeight">16.0dp</item>
  <item name="android:paddingLeft">12.5dp</item>
  <item name="android:paddingRight">5.0dp</item>
  <item name="android:scrollbarStyle">outsideInset</item>
</style>
```

### Incorrect style override {#incorrect-style-override-for-android}

```xml
<style name="Braze.ContentCardsDisplay">
  <item name="android:background">@color/mint</item>
  <item name="android:cacheColorHint">@color/mint</item>
</style>
```

## Customizing the default Content Card feed {#content-cards-fragment-customization}

This section covers customization of the [ContentCardsFragment][49] whose source can be found [here][54].

### Customizing the network connection error message

If the [ContentCardsFragment][49] determines that a Content Card refresh has failed, it will display a network connection error message.

A special adapter, the [AppboyEmptyContentCardsAdapter][50] replaces the standard [AppboyCardAdapter][53] to display the error message. To set the custom message itself, override the string resource `com_appboy_feed_empty`.

The style used to display this message can be found via [`Appboy.ContentCardsDisplay.Empty`][52] and is reproduced below.

```xml
<style name="Braze.ContentCardsDisplay.Empty">
  <item name="android:lineSpacingExtra">1.5dp</item>
  <item name="android:text">@string/com_appboy_feed_empty</item>
  <item name="android:textColor">@color/com_appboy_title</item>
  <item name="android:textSize">18.0sp</item>
  <item name="android:gravity">center</item>
  <item name="android:layout_height">match_parent</item>
  <item name="android:layout_width">match_parent</item>
</style>
```

To fully customize the network error behavior, you can extend the [ContentCardsFragment][54] and set the `mShowNetworkUnavailableRunnable` variable to perform some other action.

## Content Cards style elements {#content-cards-style-elements-for-android}

### Custom font {#setting-a-custom-font-for-android}

Braze allows for setting a custom font using the [font family guide][40]. To use it, override a style for cards and use the `fontFamily` attribute to instruct Braze to use your custom font family.

For example, to update the font on all titles for Captioned Image Cards, override the `Appboy.ContentCards.CaptionedImage.Title` style and reference your custom font family. The attribute value should point to a font family in your `res/font` directory.

Here is a truncated example with a custom font family, `my_custom_font_family`, referenced on the last line:

```xml
  <style name="Braze.ContentCards.CaptionedImage.Title">
    <item name="android:layout_width">wrap_content</item>
    ...
    <item name="android:fontFamily">@font/my_custom_font_family</item>
    <item name="fontFamily">@font/my_custom_font_family</item>
  </style>
```

### Custom pinned icon {#setting-a-custom-pinned-icon-for-android}

To set a custom pinned icon, override the `Appboy.ContentCards.PinnedIcon` style. Your custom image asset should be declared in the `android:src` element.

### Customizing displayed card order {#customizing-displayed-card-order-for-android}

The `ContentCardsFragment` relies on a [`IContentCardsUpdateHandler`][44] to handle any sorting or modifications of Content Cards before they are displayed in the feed. A custom update handler can be set via [`setContentCardUpdateHandler`][45] on your [`ContentCardsFragment`][49].

Filtering out Content Cards before they reach the user's feed is a common use-case and could be achieved by reading the key-value pairs set on the dashboard via [`Card.getExtras()`][36] and performing any logic you'd like in the update handler.

The following is the default `IContentCardsUpdateHandler` and can be used as a starting point for customizations.

{% tabs %}
{% tab JAVA %}

```java
public class DefaultContentCardsUpdateHandler implements IContentCardsUpdateHandler {

  // Interface that must be implemented and provided as a public CREATOR
  // field that generates instances of your Parcelable class from a Parcel.
  public static final Parcelable.Creator<DefaultContentCardsUpdateHandler> CREATOR = new Parcelable.Creator<DefaultContentCardsUpdateHandler>() {
    public DefaultContentCardsUpdateHandler createFromParcel(Parcel in) {
      return new DefaultContentCardsUpdateHandler();
    }

    public DefaultContentCardsUpdateHandler[] newArray(int size) {
      return new DefaultContentCardsUpdateHandler[size];
    }
  };

  @Override
  public List<Card> handleCardUpdate(ContentCardsUpdatedEvent event) {
    List<Card> sortedCards = event.getAllCards();
    // Sort by pinned, then by the 'updated' timestamp descending
    // Pinned before non-pinned
    Collections.sort(sortedCards, new Comparator<Card>() {
      @Override
      public int compare(Card cardA, Card cardB) {
        // A displays above B
        if (cardA.getIsPinned() && !cardB.getIsPinned()) {
          return -1;
        }

        // B displays above A
        if (!cardA.getIsPinned() && cardB.getIsPinned()) {
          return 1;
        }

        // At this point, both A & B are pinned or both A & B are non-pinned
        // A displays above B since A is newer
        if (cardA.getUpdated() > cardB.getUpdated()) {
          return -1;
        }

        // B displays above A since A is newer
        if (cardA.getUpdated() < cardB.getUpdated()) {
          return 1;
        }

        // At this point, every sortable field matches so keep the natural ordering
        return 0;
      }
    });

    return sortedCards;
  }

  // Parcelable interface method
  @Override
  public int describeContents() {
    return 0;
  }

  // Parcelable interface method
  @Override
  public void writeToParcel(Parcel dest, int flags) {
    // No state is kept in this class so the parcel is left unmodified
  }
}
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
class DefaultContentCardsUpdateHandler : IContentCardsUpdateHandler {
  override fun handleCardUpdate(event: ContentCardsUpdatedEvent): List<Card> {
    val sortedCards = event.allCards
    // Sort by pinned, then by the 'updated' timestamp descending
    // Pinned before non-pinned
    sortedCards.sortWith(Comparator sort@{ cardA: Card, cardB: Card ->
      // A displays above B
      if (cardA.isPinned && !cardB.isPinned) {
        return@sort -1
      }

      // B displays above A
      if (!cardA.isPinned && cardB.isPinned) {
        return@sort 1
      }

      // At this point, both A & B are pinned or both A & B are non-pinned
      // A displays above B since A is newer
      if (cardA.updated > cardB.updated) {
        return@sort -1
      }

      // B displays above A since A is newer
      if (cardA.updated < cardB.updated) {
        return@sort 1
      }
      0
    })
    return sortedCards
  }

  // Parcelable interface method
  override fun describeContents(): Int {
    return 0
  }

  // Parcelable interface method
  override fun writeToParcel(dest: Parcel, flags: Int) {
    // No state is kept in this class so the parcel is left unmodified
  }

  companion object {
    // Interface that must be implemented and provided as a public CREATOR
    // field that generates instances of your Parcelable class from a Parcel.
    val CREATOR: Parcelable.Creator<DefaultContentCardsUpdateHandler?> = object : Parcelable.Creator<DefaultContentCardsUpdateHandler?> {
      override fun createFromParcel(`in`: Parcel): DefaultContentCardsUpdateHandler? {
        return DefaultContentCardsUpdateHandler()
      }

      override fun newArray(size: Int): Array<DefaultContentCardsUpdateHandler?> {
        return arrayOfNulls(size)
      }
    }
  }
}
```

{% endtab %}
{% endtabs %}

> This code can also be found here, [DefaultContentCardsUpdateHandler][46].

And here's how to use the above class:

{% tabs %}
{% tab JAVA %}

```java
IContentCardsUpdateHandler cardUpdateHandler = new DefaultContentCardsUpdateHandler();

ContentCardsFragment fragment = getMyCustomFragment();
fragment.setContentCardUpdateHandler(cardUpdateHandler);
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
val cardUpdateHandler = DefaultContentCardsUpdateHandler()

val fragment = getMyCustomFragment()
fragment.setContentCardUpdateHandler(cardUpdateHandler)
```

{% endtab %}
{% endtabs %}

### Customizing card rendering {#customizing-card-rendering-for-android}

Here's information on how to change how any card is rendered in the recyclerView. The `IContentCardsViewBindingHandler` interface defines how all Content Cards get rendered. You can customize this to change anything you want.

{% tabs %}
{% tab JAVA %}

```java
public class DefaultContentCardsViewBindingHandler implements IContentCardsViewBindingHandler {
  // Interface that must be implemented and provided as a public CREATOR
  // field that generates instances of your Parcelable class from a Parcel.
  public static final Parcelable.Creator<DefaultContentCardsViewBindingHandler> CREATOR = new Parcelable.Creator<DefaultContentCardsViewBindingHandler>() {
    public DefaultContentCardsViewBindingHandler createFromParcel(Parcel in) {
      return new DefaultContentCardsViewBindingHandler();
    }

    public DefaultContentCardsViewBindingHandler[] newArray(int size) {
      return new DefaultContentCardsViewBindingHandler[size];
    }
  };

  /**
   * A cache for the views used in binding the items in the {@link android.support.v7.widget.RecyclerView}.
   */
  private final Map<CardType, BaseContentCardView> mContentCardViewCache = new HashMap<CardType, BaseContentCardView>();

  @Override
  public ContentCardViewHolder onCreateViewHolder(Context context, List<Card> cards, ViewGroup viewGroup, int viewType) {
    CardType cardType = CardType.fromValue(viewType);
    return getContentCardsViewFromCache(context, cardType).createViewHolder(viewGroup);
  }

  @Override
  public void onBindViewHolder(Context context, List<Card> cards, ContentCardViewHolder viewHolder, int adapterPosition) {
    Card cardAtPosition = cards.get(adapterPosition);
    BaseContentCardView contentCardView = getContentCardsViewFromCache(context, cardAtPosition.getCardType());
    contentCardView.bindViewHolder(viewHolder, cardAtPosition);
  }

  @Override
  public int getItemViewType(Context context, List<Card> cards, int adapterPosition) {
    Card card = cards.get(adapterPosition);
    return card.getCardType().getValue();
  }

  /**
   * Gets a cached instance of a {@link BaseContentCardView} for view creation/binding for a given {@link CardType}.
   * If the {@link CardType} is not found in the cache, then a view binding implementation for that {@link CardType}
   * is created and added to the cache.
   */
  @VisibleForTesting
  BaseContentCardView getContentCardsViewFromCache(Context context, CardType cardType) {
    if (!mContentCardViewCache.containsKey(cardType)) {
      // Create the view here
      BaseContentCardView contentCardView;
      switch (cardType) {
        case BANNER:
          contentCardView = new BannerImageContentCardView(context);
          break;
        case CAPTIONED_IMAGE:
          contentCardView = new CaptionedImageContentCardView(context);
          break;
        case SHORT_NEWS:
          contentCardView = new ShortNewsContentCardView(context);
          break;
        case TEXT_ANNOUNCEMENT:
          contentCardView = new TextAnnouncementContentCardView(context);
          break;
        default:
          contentCardView = new DefaultContentCardView(context);
          break;
      }
      mContentCardViewCache.put(cardType, contentCardView);
    }
    return mContentCardViewCache.get(cardType);
  }

  // Parcelable interface method
  @Override
  public int describeContents() {
    return 0;
  }

  // Parcelable interface method
  @Override
  public void writeToParcel(Parcel dest, int flags) {
    // Retaining views across a transition could lead to a
    // resource leak so the parcel is left unmodified
  }
}
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
class DefaultContentCardsViewBindingHandler : IContentCardsViewBindingHandler {
  // Interface that must be implemented and provided as a public CREATOR
  // field that generates instances of your Parcelable class from a Parcel.
  val CREATOR: Parcelable.Creator<DefaultContentCardsViewBindingHandler?> = object : Parcelable.Creator<DefaultContentCardsViewBindingHandler?> {
    override fun createFromParcel(`in`: Parcel): DefaultContentCardsViewBindingHandler? {
      return DefaultContentCardsViewBindingHandler()
    }

    override fun newArray(size: Int): Array<DefaultContentCardsViewBindingHandler?> {
      return arrayOfNulls(size)
    }
  }

  /**
    * A cache for the views used in binding the items in the [RecyclerView].
    */
  private val mContentCardViewCache: MutableMap<CardType, BaseContentCardView<*>?> = HashMap()

  override fun onCreateViewHolder(context: Context?, cards: List<Card?>?, viewGroup: ViewGroup?, viewType: Int): ContentCardViewHolder? {
    val cardType = CardType.fromValue(viewType)
    return getContentCardsViewFromCache(context, cardType)!!.createViewHolder(viewGroup)
  }

  override fun onBindViewHolder(context: Context?, cards: List<Card>, viewHolder: ContentCardViewHolder?, adapterPosition: Int) {
    if (adapterPosition < 0 || adapterPosition >= cards.size) {
      return
    }
    val cardAtPosition = cards[adapterPosition]
    val contentCardView = getContentCardsViewFromCache(context, cardAtPosition.cardType)
    if (viewHolder != null) {
      contentCardView!!.bindViewHolder(viewHolder, cardAtPosition)
    }
  }

  override fun getItemViewType(context: Context?, cards: List<Card>, adapterPosition: Int): Int {
    if (adapterPosition < 0 || adapterPosition >= cards.size) {
      return -1
    }
    val card = cards[adapterPosition]
    return card.cardType.value
  }

  /**
    * Gets a cached instance of a [BaseContentCardView] for view creation/binding for a given [CardType].
    * If the [CardType] is not found in the cache, then a view binding implementation for that [CardType]
    * is created and added to the cache.
    */
  @VisibleForTesting
  fun getContentCardsViewFromCache(context: Context?, cardType: CardType): BaseContentCardView<Card>? {
    if (!mContentCardViewCache.containsKey(cardType)) {
      // Create the view here
      val contentCardView: BaseContentCardView<*> = when (cardType) {
        CardType.BANNER -> BannerImageContentCardView(context)
        CardType.CAPTIONED_IMAGE -> CaptionedImageContentCardView(context)
        CardType.SHORT_NEWS -> ShortNewsContentCardView(context)
        CardType.TEXT_ANNOUNCEMENT -> TextAnnouncementContentCardView(context)
        else -> DefaultContentCardView(context)
      }
      mContentCardViewCache[cardType] = contentCardView
    }
    return mContentCardViewCache[cardType] as BaseContentCardView<Card>?
  }

  // Parcelable interface method
  override fun describeContents(): Int {
    return 0
  }

  // Parcelable interface method
  override fun writeToParcel(dest: Parcel?, flags: Int) {
    // Retaining views across a transition could lead to a
    // resource leak so the parcel is left unmodified
  }
}
```

{% endtab %}
{% endtabs %}

> This code can also be found here, [DefaultContentCardsViewBindingHandler][56].

And here's how to use the above class:

{% tabs %}
{% tab JAVA %}

```java
IContentCardsViewBindingHandler viewBindingHandler = new DefaultContentCardsViewBindingHandler();

ContentCardsFragment fragment = getMyCustomFragment();
fragment.setContentCardsViewBindingHandler(viewBindingHandler);
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
val viewBindingHandler = DefaultContentCardsViewBindingHandler()

val fragment = getMyCustomFragment()
fragment.setContentCardsViewBindingHandler(viewBindingHandler)
```

{% endtab %}
{% endtabs %}

There are additional relevant resources on this topic available [here](https://medium.com/google-developers/android-data-binding-recyclerview-db7c40d9f0e4).

### Custom Content Cards click listener

You can handle Content Cards clicks manually by setting a custom click listener. This enables use cases such as selectively using the native web browser to open web links.

#### Step 1: Implement a Content Cards click listener

Create a class that implements [IContentCardsActionListener][43] and register it with `BrazeContentCardsManager`. Implement the `onContentCardClicked()` method, which will be called when the user clicks a content card.

#### Step 2: Instruct Braze to use your Content Card click listener

You can see an example of steps 1 and 2 here:

{% tabs %}
{% tab JAVA %}

```java
BrazeContentCardsManager.getInstance().setContentCardsActionListener(new IContentCardsActionListener() {
  @Override
  public boolean onContentCardClicked(Context context, Card card, IAction cardAction) {
    return false;
  }

  @Override
  public void onContentCardDismissed(Context context, Card card) {

  }
});
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
BrazeContentCardsManager.getInstance().contentCardsActionListener = object : IContentCardsActionListener {
  override fun onContentCardClicked(context: Context, card: Card, cardAction: IAction): Boolean {
    return false
  }

  override fun onContentCardDismissed(context: Context, card: Card) {

  }
}
```

{% endtab %}
{% endtabs %}

### Dark theme customization

By default, Content Cards views will automatically respond to Dark Theme changes on the device with a set of themed colors and layout changes. 

To override this behavior, override the `values-night` values in `android-sdk-ui/src/main/res/values-night/colors.xml` and `android-sdk-ui/src/main/res/values-night/dimens.xml`.

### Fully custom Content Card display {#fully-custom-content-card-display-for-android}

If you would like to display the Content Cards in a completely custom manner, it is possible to do so by using your own views populated with data from our models. To obtain Braze’s Content Cards models, you will need to subscribe to content card updates and use the resulting model data to populate your views. You will also need to log analytics on the model objects as users interact with your views.

#### Part 1: Subscribing to Content Card updates

First, declare a private variable in your custom class to hold your subscriber:

{% tabs %}
{% tab JAVA %}

```java
// subscriber variable
private IEventSubscriber<ContentCardsUpdatedEvent> mContentCardsUpdatedSubscriber;
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
private var mContentCardsUpdatedSubscriber: IEventSubscriber<ContentCardsUpdatedEvent>? = null
```

{% endtab %}
{% endtabs %}

Next, add the following code to subscribe to Content Card updates from Braze, typically inside of your custom Content Cards activity's `Activity.onCreate()`:

{% tabs %}
{% tab JAVA %}

```java
// Remove the previous subscriber before rebuilding a new one with our new activity.
Braze.getInstance(context).removeSingleSubscription(mContentCardsUpdatedSubscriber, ContentCardsUpdatedEvent.class);
mContentCardsUpdatedSubscriber = new IEventSubscriber<ContentCardsUpdatedEvent>() {
    @Override
    public void trigger(ContentCardsUpdatedEvent event) {
        // List of all Content Cards
        List<Card> allCards = event.getAllCards();

        // Your logic below
    }
};
Braze.getInstance(context).subscribeToContentCardsUpdates(mContentCardsUpdatedSubscriber);
Braze.getInstance(context).requestContentCardsRefresh(true);
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
// Remove the previous subscriber before rebuilding a new one with our new activity.
Braze.getInstance(context).removeSingleSubscription(mContentCardsUpdatedSubscriber, ContentCardsUpdatedEvent::class.java)
mContentCardsUpdatedSubscriber = IEventSubscriber { event ->
  // List of all Content Cards
  val allCards = event.allCards

  // Your logic below
}
Braze.getInstance(context).subscribeToContentCardsUpdates(mContentCardsUpdatedSubscriber)
Braze.getInstance(context).requestContentCardsRefresh(true)
```

{% endtab %}
{% endtabs %}

We also recommend unsubscribing when your custom activity moves out of view. Add the following code to your activity's `onDestroy()` lifecycle method:

{% tabs %}
{% tab JAVA %}

```java
Braze.getInstance(context).removeSingleSubscription(mContentCardsUpdatedSubscriber, ContentCardsUpdatedEvent.class);
```

{% endtab %}
{% tab KOTLIN %}

```kotlin
Braze.getInstance(context).removeSingleSubscription(mContentCardsUpdatedSubscriber, ContentCardsUpdatedEvent::class.java)
```

{% endtab %}
{% endtabs %}

#### Part 2: Logging analytics

When using custom views, you will need to log analytics manually as well, since analytics are only handled automatically when using Braze views.

To log a display of the Content Cards, call [`Appboy.logContentCardsDisplayed()`][41].

To log an impression or click on a Card, call [`Card.logClick()`][7] or [`Card.logImpression()`][8] respectively.

For campaigns using Control Cards for A/B testing, you can use [`Card.isControl()`][55] to determine if a card will be blank, and used only for tracking purposes.

#### Manually dismissing a Content Card

You can manually log or set a Content Card as "dismissed" to Braze [for a particular card with `setIsDismissed`][57].

If a card is already marked as dismissed, it cannot be marked as dismissed again.

## Disabling swipe to dismiss

Disabling swipe-to-dismiss functionality is done on a per-card basis via the [`card.setIsDismissibleByUser()`][48] method. Cards can be intercepted before display using the [`ContentCardsFragment.setContentCardUpdateHandler()`][45] method.

## Key-value pairs

`Card` objects may optionally carry key-value pairs as `extras`. These can be used to send data down along with a `Card` for further handling by the application.

See the [KDoc][36] for more information.

{% alert note %}
Content Cards have a maximum size limit of 2 KB for content you enter in the Braze dashboard. This includes message text, image URLs, links, and key-value pairs. Exceeding that amount will prevent the card from sending.
{% endalert %}

## GIFs {#gifs-news-content-cards}

{% alert note %}
This section applies to integrations which use the Braze SDK's default Content Cards Fragment or Views to display Content Cards.
{% endalert %}

{% include archive/android/gifs.md channel="Content Cards" %}

## Use case: Carousel view

![Sample news app showing carousel of Content Cards in an article]({% image_buster/assets/img_archive/cc_politer_carousel_android.png %}){: style="max-width:30%;float:right;margin-left:15px;border:none;"}

This section covers how to implement a multi-card carousel feed where a user can swipe horizontally to view additional featured cards. In order to integrate a carousel view, you'll need to use a fully customized Content Card implementation—the "run" phase of the [crawl, walk, run approach][1].

With this approach, you will not use Braze’s views and default logic, but will instead display the Content Cards in a completely custom manner by using your own views populated with data from the Braze models.

In terms of level of development effort, the key differences between the out-of-the-box implementation and the carousel implementation include:

- Building your own views
- Logging Content Card analytics
- Introducing additional client-side logic to dictate how many and which cards to show in the carousel

### Implementation

#### Step 1: Create a custom view controller

To create the Content Cards carousel, create your own custom views and [subscribe for data updates]({{site.baseurl}}/developer_guide/platform_integration_guides/android/content_cards/customization/#fully-custom-content-card-display-for-android). Note that you won't be able to use Braze's default `ContentCardFragment`, as it's only able to handle our default Content Card types.

#### Step 2: Implement analytics

When creating a fully custom view controller, Content Card impressions, clicks, and dismissals are not automatically logged. You must implement the respective analytics methods to ensure impressions, dismissal events, and clicks get properly logged back to Braze's dashboard analytics.

For information on the analytics methods, refer to [Card analytics methods]({{site.baseurl}}/developer_guide/platform_integration_guides/android/content_cards/data_models/#card-analytics-methods).

{% alert note %}
The same page also details the different properties inherited from our generic Content Card model class, which you may find useful during your view implementation.
{% endalert %}

#### Step 3: Create a Content Card update handler

Follow the steps for [Using multiple Content Card feeds]({{site.baseurl}}/developer_guide/platform_integration_guides/android/content_cards/multiple_feeds/) to set key-value pairs on cards and create a Content Card update handler.

{% alert important %}
It's important to ensure your marketing and developer teams coordinate on which key-value pairs will be used (e.g, `feed_type = brand_homepage`), as any key-value pairs marketers input into the Braze dashboard must exactly match the key-value pairs which the developers build into the app logic.
{% endalert %}

For Android-specific developer documentation on the Content Cards class, methods, and attributes in Kotlin, refer to the Android [com.braze.ui.contentcards.view](https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.braze.ui.contentcards.view/index.html) documentation.

### Considerations

- By using completely custom views, you will not be able to extend or subclass the methods used in `ABKContentCardsController`. Instead, you'll need to integrate the data model methods and properties yourself.
- The logic and implementation of the carousel view is not a default type of Content Card in Braze, and therefore the logic for achieving the use case must be supplied and supported by your development team.
- You will need to implement client-side logic to display a specific number of cards in the carousel at any one time.

[1]: {{site.baseurl}}/user_guide/message_building_by_channel/content_cards/customize/#customization-approaches
[7]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.models.cards/-card/log-click.html
[8]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.models.cards/-card/log-impression.html
[36]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.models.cards/-card/get-extras.html
[40]: {{site.baseurl}}/developer_guide/platform_integration_guides/android/advanced_use_cases/font_customization/#font-customization
[41]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy/-appboy/log-content-cards-displayed.html
[42]: https://github.com/Appboy/appboy-android-sdk/blob/master/android-sdk-ui/src/main/res/values/styles.xml
[43]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.braze.ui.contentcards.listeners/-i-content-cards-action-listener/index.html
[44]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.braze.ui.contentcards.handlers/-i-content-cards-update-handler/index.html
[45]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.braze.ui.contentcards/-content-cards-fragment/set-content-card-update-handler.html
[46]: https://github.com/Appboy/appboy-android-sdk/blob/v11.0.0/android-sdk-ui/src/main/java/com/appboy/ui/contentcards/handlers/DefaultContentCardsUpdateHandler.java
[48]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.models.cards/-card/set-is-dismissible-by-user.html
[49]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.braze.ui.contentcards/-content-cards-fragment/index.html
[50]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.ui.contentcards/-appboy-empty-content-cards-adapter/index.html
[52]: https://github.com/Appboy/appboy-android-sdk/blob/master/android-sdk-ui/src/main/res/values/styles.xml#L552-L560
[53]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.ui.contentcards/-appboy-empty-content-cards-adapter/index.html
[54]: https://github.com/Appboy/appboy-android-sdk/blob/master/android-sdk-ui/src/main/java/com/braze/ui/contentcards/ContentCardsFragment.java
[55]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.models.cards/-card/is-control.html
[56]: https://github.com/Appboy/appboy-android-sdk/blob/v11.0.0/android-sdk-ui/src/main/java/com/appboy/ui/contentcards/handlers/DefaultContentCardsViewBindingHandler.java
[57]: https://appboy.github.io/appboy-android-sdk/kdoc/braze-android-sdk/com.appboy.models.cards/-card/set-is-dismissed.html
