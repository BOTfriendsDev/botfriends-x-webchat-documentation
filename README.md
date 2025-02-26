# BOTfriends X Webchat
## Usage

### Script Tag

Add the following code towards the end of the `head` section on your page and replace `<integration-id>` with your integration id at the end of the script.

<!-- prettier-ignore -->
```html
<script>
    !function(s,r,o){var p,a,i,c=[],u=[];function e(){try{var e;if((e="string"==typeof this.response?JSON.parse(this.response):this.response).url){var t=r.getElementsByTagName("script")[0],n=r.createElement("script");n.async=!0,n.src=e.url,t.parentNode.insertBefore(n,t)}}catch(e){}}s[o]={init:function(){p=arguments;var t={then:function(e){return u.push({type:"t",next:e}),t},catch:function(e){return u.push({type:"c",next:e}),t}};return t},on:function(){c.push(arguments)},render:function(){a=arguments},destroy:function(){i=arguments}},s.__onWebMessengerHostReady__=function(e){if(delete s.__onWebMessengerHostReady__,s[o]=e,p)for(var t=e.init.apply(e,p),n=0;n<u.length;n++){var r=u[n];t="t"===r.type?t.then(r.next):t.catch(r.next)}a&&e.render.apply(e,a),i&&e.destroy.apply(e,i);for(n=0;n<c.length;n++)e.on.apply(e,c[n])};var t=new XMLHttpRequest;t.addEventListener("load",e),t.open("GET","https://www.cdn.botfriendsx.com/loader.json",!0),t.responseType="json",t.send()}(window,document,"BotfriendsWebchat");
</script>
```

then initialize the Web Messenger by placing this snippet towards the end of the `body` section of your page.

```html
<script>
    BotfriendsWebchat.init({integrationId: '<integration-id>'}).then(function() {
        // Your code after init is complete
    });
</script>
```

## Browser support

Web Messenger supports all popular browsers.

#### Desktop versions

-   Chrome: Latest and one major version behind
-   Edge: Latest and one major version behind
-   Firefox: Latest and one major version behind
-   Internet Explorer: 11+
-   Safari: Latest and one major version behind

#### Mobile versions

-   Stock browser on Android 4.1+
-   Safari on iOS 8+

#### Other browsers

Web Messenger is likely compatible with other and older browsers but we only test against the versions above.

## API

### Individual functions

#### init(options)

Initializes the BotfriendsWebchat widget in the web page using the specified options. It returns a promise that will resolve when the Web Messenger is ready. Note that except`on` and `off`, all methods needs to be called after a successful `init`.

##### Options

| Option                           | Optional? | Default value                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------- | --------- | ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| appId                            | Yes       | -                             | Your app id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| integrationId                    | No        | -                             | Your integration id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| region                           | Yes       | -                             | The target region in which the app is located.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| userId                           | Yes       | -                             | Optional. User's id, which can be passed in `init()` as an alternative to `login()` (see [below](#authenticating-the-user-init-vs-login))                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| jwt                              | Yes       | -                             | Optional. User's authentication token, which can be passed in `init()` as an alternative to `login()` (see [below](#authenticating-the-user-init-vs-login))                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| authCode                         | Yes       | -                             | An auth code for linking to an existing conversation                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| locale                           | Yes       | `en-US`                       | Locale used for date formatting using the `<language>-<COUNTRY>` format. Language codes can be found [here](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) and country codes [here](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2). <br /> **Note 1 : ** The country part is optional, and if a country is either not recognized or supported, it will fallback to using the generic language. If the language isn't supported, it will fallback to `en-US`. <br /> **Note 2:** this is _only_ used for date formatting and doesn't provide built-in translations for Web Messenger. |
| soundNotificationEnabled         | Yes       | `true`                        | Enables the sound notification for new messages                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| imageUploadEnabled               | Yes       | `true`                        | Enables the image upload feature. (deprecated: use menuItems.imageUpload; if this option is `false`, it will disable `menuItems.imageUpload` and `menuItems.fileUpload`)                                                                                                                                                                                                                                                                                                                                                                                                                        |
| fixedIntroPane                   | Yes       | `false`                       | When enabled, the introduction pane will be pinned at the top of the conversation instead of scrolling with it.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| embedded                         | Yes       | False                         | Tells the widget it will be embedded. (see Embedded section below)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| displayStyle                     | Yes       | `button`                      | Choose how the messenger will appear on your website. Must be either `button` or `tab`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| buttonIconUrl                    | Yes       | -                             | When the `displayStyle` is `button`, you have the option of selecting your own button icon. The image must be at least 200 x 200 pixels and must be in either JPG, PNG, or GIF format.                                                                                                                                                                                                                                                                                                                                                                                                          |
| buttonWidth                      | Yes       | `58px`                        | When the `displayStyle` is `button`, you have the option of specifying the button width.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| buttonHeight                     | Yes       | `58px`                        | When the `displayStyle` is `button`, you have the option of specifying the button height.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| businessName                     | Yes       | -                             | A custom business name.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| businessIconUrl                  | Yes       | -                             | A custom business icon url. The image must be at least 200 x 200 pixels and must be in either JPG, PNG, or GIF format.                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| backgroundImageUrl               | Yes       | -                             | A background image url for the conversation. Image will be tiled to fit the window.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| integrationOrder                 | Yes       | -                             | Array of integration IDs. When set, only integrations from this list will be displayed. If an empty array is used, no integrations will be displayed. _Note_: Listing an integration in the array doesn't guarantee that it will be displayed in the Web Messenger.                                                                                                                                                                                                                                                                                                                             |
| customColors                     | Yes       | [See below.](#customcolors)   | Colors used in the Web Messenger UI.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| customText                       | Yes       | See the example below         | Strings used in the Web Messenger UI. You can use these to either customize the text or translate it. _Note_: Some strings include variables (surrounded by `{}`) which must remain in your customized text.                                                                                                                                                                                                                                                                                                                                                                                    |
| menuItems                        | Yes       | [See below.](#menuitems)      | Choose menu items.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| notificationChannelPromptEnabled | Yes       | `true`                        | Enables displaying a prompt to new users after their first message to suggest linking their chat instance with their other 3rd-party apps.                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| browserStorage                   | Yes       | `localStorage`                | Choose the storage type to use for storing user identity in the browser. Must be either `localStorage` or `sessionStorage`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| delegate                         | Yes       | `undefined`                   | Sets a delegate on the conversation. See the [delegate](#delegate) section for more details.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| prechatCapture                   | Yes       | [See below.](#prechatcapture) | Enables automatically capturing a user's name and email via a form before the start of a conversation. Disables the chat input until the form has been submitted.                                                                                                                                                                                                                                                                                                                                                                                                                               |

##### `region`

BotfriendsWebchat is available in the following regions:

| Region         | Region Identifier   |
| -------------- | ------------------- |
| United States  | _Leave unspecified_ |
| European Union | `eu-1`              |

##### `customColors`

| Option            | Optional? | Default value | Description                                                                                                                           |
| ----------------- | --------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| brandColor        | Yes       | `65758e`      | This color will be used in the messenger header and the button or tab in idle state. Must be a 3 or 6-character hexadecimal color.    |
| conversationColor | Yes       | `0099ff`      | This color will be used for customer messages, quick replies and actions in the footer. Must be a 3 or 6-character hexadecimal color. |
| actionColor       | Yes       | `0099ff`      | This color will be used for call-to-actions inside your messages. Must be a 3 or 6-character hexadecimal color.                       |

##### `customText`

The list of localizable strings. These strings can be modified. _If an option is not given a custom string, the default value will be used._

| Option                                   | Default value                                                                                                                                                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| actionPaymentCompleted                   | Payment Completed                                                                                                                                                                                                    |
| actionPaymentError                       | An error occurred while processing the card. `<br>` Please try again or use a different card.                                                                                                                        |
| actionPostbackError                      | An error occurred while processing your action. Please try again.                                                                                                                                                    |
| clickToRetry                             | Message not delivered. Click to retry.                                                                                                                                                                               |
| clickToRetryForm                         | Form not submitted. Click anywhere on the form to retry.                                                                                                                                                             |
| connectNotificationText                  | Give us a way to reach out:                                                                                                                                                                                          |
| connectNotificationSingleText            | Be notified when you get a reply.                                                                                                                                                                                    |
| connectNotificationSingleButtonText      | Turn on `<name>` notifications                                                                                                                                                                                       |
| connectNotificationOthersText            | others                                                                                                                                                                                                               |
| conversationTimestampHeaderFormat        | MMMM D YYYY, h:mm A                                                                                                                                                                                                  |
| couldNotConnect                          | Couldn't connect. You can {retry}.                                                                                                                                                                                   |
| couldNotConnectInner                     | retry connecting now                                                                                                                                                                                                 |
| couldNotConnectWithRetry                 | Couldn't connect. We'll keep retrying, or you can {retry}.                                                                                                                                                           |
| couldNotConnectWithRetryInner            | try now                                                                                                                                                                                                              |
| emailChangeAddress                       | Change my email                                                                                                                                                                                                      |
| emailDescription                         | To be notified by email when you get a reply, enter your email address.                                                                                                                                              |
| emailFieldLabel                          | Your email                                                                                                                                                                                                           |
| emailFieldPlaceholder                    | Your email address                                                                                                                                                                                                   |
| emailFormButton                          | Continue                                                                                                                                                                                                             |
| fetchHistory                             | Load more                                                                                                                                                                                                            |
| fetchingHistory                          | Retrieving history...                                                                                                                                                                                                |
| fileTooLargeError                        | Max file size limit exceeded ({size})                                                                                                                                                                                |
| fileTypeError                            | Unsupported file type.                                                                                                                                                                                               |
| formErrorInvalidEmail                    | Email is invalid                                                                                                                                                                                                     |
| formErrorNoLongerThan                    | Must contain no more than ({characters}) characters                                                                                                                                                                  |
| formErrorNoShorterThan                   | Must contain at least ({characters}) characters                                                                                                                                                                      |
| formErrorUnknown                         | This doesn't look quite right                                                                                                                                                                                        |
| formFieldSelectPlaceholderFallback       | Choose one...                                                                                                                                                                                                        |
| frontendEmailChannelDescription          | To talk to us using email just send a message to our email address and we\'ll reply shortly:                                                                                                                         |
| headerText                               | How can we help?                                                                                                                                                                                                     |
| imageClickToReload                       | Click to reload image.                                                                                                                                                                                               |
| imageClickToView                         | Click to view {size} image.                                                                                                                                                                                          |
| imagePreviewNotAvailable                 | Preview not available.                                                                                                                                                                                               |
| inputPlaceholder                         | Type a message...                                                                                                                                                                                                    |
| inputPlaceholderBlocked                  | Complete the form above...                                                                                                                                                                                           |
| introAppText                             | Message us below or from your favorite app.                                                                                                                                                                          |
| introductionText                         | We\'re here to talk, so ask us anything!                                                                                                                                                                             |
| lineChannelDescription                   | To talk to us using LINE, scan this QR code using the LINE app and send us a message.                                                                                                                                |
| linkError                                | An error occurred when attempting to generate a link for this channel. Please try again.                                                                                                                             |
| locationNotSupported                     | Your browser does not support location services or it’s been disabled. Please type your location instead.                                                                                                            |
| locationSecurityRestriction              | This website cannot access your location. Please type your location instead.                                                                                                                                         |
| locationSendingFailed                    | Could not send location                                                                                                                                                                                              |
| locationServicesDenied                   | This website cannot access your location. Allow access in your settings or type your location instead.                                                                                                               |
| messageError                             | An error occured while sending your message. Please try again.                                                                                                                                                       |
| messageIndicatorTitlePlural              | (`{count}`) New messages                                                                                                                                                                                             |
| messageIndicatorTitleSingular            | (`{count}`) New message                                                                                                                                                                                              |
| messageRelativeTimeDay                   | `{value}`d ago                                                                                                                                                                                                       |
| messageRelativeTimeHour                  | `{value}`h ago                                                                                                                                                                                                       |
| messageRelativeTimeJustNow               | Just now                                                                                                                                                                                                             |
| messageRelativeTimeMinute                | `{value}`m ago                                                                                                                                                                                                       |
| messageTimestampFormat                   | h:mm A                                                                                                                                                                                                               |
| messageSending                           | Sending...                                                                                                                                                                                                           |
| messageDelivered                         | Delivered                                                                                                                                                                                                            |
| messengerChannelDescription              | Connect your Facebook Messenger account to be notified when you get a reply and continue the conversation on Facebook Messenger.                                                                                     |
| notificationSettingsChannelsDescription  | You can also talk to us from your favorite app or service.                                                                                                                                                           |
| notificationSettingsChannelsTitle        | Other Channels                                                                                                                                                                                                       |
| notificationSettingsConnected            | Connected                                                                                                                                                                                                            |
| notificationSettingsConnectedAs          | Connected as `{username}`                                                                                                                                                                                            |
| prechatCaptureGreetingText               | Hi there 👋\nTo start off, we\'d like to know a little bit more about you:                                                                                                                                           |
| prechatCaptureNameLabel                  | Your name                                                                                                                                                                                                            |
| prechatCaptureNamePlaceholder            | Type your name...                                                                                                                                                                                                    |
| prechatCaptureEmailLabel                 | Email                                                                                                                                                                                                                |
| prechatCaptureEmailPlaceholder           | name@company.com                                                                                                                                                                                                     |
| prechatCaptureConfirmationText           | Thanks for that! What can we help you with?                                                                                                                                                                          |
| prechatCaptureMailgunLinkingConfirmation | You\'ll be notified here and by email at {email} once we reply.                                                                                                                                                      |
| sendButtonText                           | Send                                                                                                                                                                                                                 |
| settingsHeaderText                       | Settings                                                                                                                                                                                                             |
| shareLocation                            | Share location                                                                                                                                                                                                       |
| smsBadRequestError                       | We were unable to communicate with this number. Try again or use a different one.                                                                                                                                    |
| smsCancel                                | Cancel                                                                                                                                                                                                               |
| smsChangeNumber                          | Change my number                                                                                                                                                                                                     |
| smsChannelDescription                    | Connect your SMS number to be notified when you get a reply and continue the conversation over SMS.                                                                                                                  |
| smsChannelPendingDescription             | Check your messages at `{number}` to confirm your phone number.                                                                                                                                                      |
| smsContinue                              | Continue                                                                                                                                                                                                             |
| smsInvalidNumberError                    | Your phone number isn\'t valid. Please try again.                                                                                                                                                                    |
| smsLinkCancelled                         | Link to `{appUserNumber}` was cancelled.                                                                                                                                                                             |
| smsLinkPending                           | Pending                                                                                                                                                                                                              |
| smsPingChannelError                      | There was an error sending a message to your number.                                                                                                                                                                 |
| smsSendText                              | Send me a text                                                                                                                                                                                                       |
| smsStartTexting                          | Start Texting                                                                                                                                                                                                        |
| smsTooManyRequestsError                  | A connection for that number was requested recently. Please try again in {minutes} minutes.                                                                                                                          |
| smsTooManyRequestsOneMinuteError         | A connection for that number was requested recently. Please try again in 1 minute.                                                                                                                                   |
| smsUnhandledError                        | Something went wrong. Please try again.                                                                                                                                                                              |
| tapToRetry                               | Message not delivered. Tap to retry.                                                                                                                                                                                 |
| tapToRetryForm                           | Form not submitted. Tap anywhere on the form to retry.                                                                                                                                                               |
| telegramChannelDescription               | Connect your Telegram account to be notified when you get a reply and continue the conversation on Telegram                                                                                                          |
| uploadDocument                           | Upload document                                                                                                                                                                                                      |
| uploadInvalidError                       | Invalid file                                                                                                                                                                                                         |
| uploadPhoto                              | Upload photo                                                                                                                                                                                                         |
| uploadVirusError                         | A virus was detected in your file and it has been rejected                                                                                                                                                           |
| unsupportedMessageType                   | Unsupported message type.                                                                                                                                                                                            |
| unsupportedActionType                    | Unsupported action type.                                                                                                                                                                                             |
| viberChannelDescription                  | Connect your Viber account to be notified when you get a reply and continue the conversation on Viber. To get started, scan the QR code using the Viber app.                                                         |
| viberChannelDescriptionMobile            | Connect your Viber account to be notified when you get a reply and continue the conversation on Viber. To get started, install the Viber app and tap Connect.                                                        |
| viberQRCodeError                         | An error occurred while fetching your Viber QR code. Please try again.                                                                                                                                               |
| wechatChannelDescription                 | Connect your WeChat account to be notified when you get a reply and continue the conversation on WeChat. To get started, scan this QR code using the WeChat app.                                                     |
| wechatChannelDescriptionMobile           | Connect your WeChat account to be notified when you get a reply and continue the conversation on WeChat. To get started, save this QR code image and upload it `<a href=\'weixin://dl/scan\'>`QR code scanner`</a>`. |
| wechatQRCodeError                        | An error occurred while fetching your WeChat QR code. Please try again.                                                                                                                                              |
| whatsappChannelDescriptionDesktop        | Sync your account to WhatsApp by scanning the QR code or clicking the link below.\nThen, send the pre-populated message to validate the sync request. (Your code: {{code}}).                                         |
| whatsappChannelDescriptionMobile         | Sync your account to WhatsApp by clicking the link below.\nThen, send the pre-populated message to validate the sync request. (Your code: {{code}}).                                                                 |
| whatsappLinkingError                     | An error occurred while fetching your WhatsApp linking information. Please try again.                                                                                                                                |

[See below](#example) for an example.

##### `menuItems`

| Option        | Optional? | Default value | Description                           |
| ------------- | --------- | ------------- | ------------------------------------- |
| imageUpload   | Yes       | `true`        | Enables the image upload menu item.   |
| fileUpload    | Yes       | `true`        | Enables the file upload menu item.    |
| shareLocation | Yes       | `true`        | Enables the share location menu item. |

[See below](#example) for an example.

##### `prechatCapture`

| Option             | Optional? | Default value | Description                                                                                                                                                            |
| ------------------ | --------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| avatarUrl          | Yes       | `undefined`   | Sets the URL of the avatar to use for the automatic reply to the prechat capture messages.                                                                             |
| enabled            | Yes       | `false`       | Enables the prechat capture experience.                                                                                                                                |
| enableEmailLinking | Yes       | `true`        | Automatically links the user's email to the app's Mailgun integration if it exists. If the property `fields` is defined, the first field of type `email` will be used. |
| fields             | Yes       | -             | Overrides the default Prechat Capture fields to define a custom form.                                                                                                  |

[beforeSend delegate](#beforesend) will apply to the user's submitted message.

[See below](#example) for an example.

`prechatCapture` uses the following [`customText`](#customText) options:

| Option                                   | Description                                                                   |
| ---------------------------------------- | ----------------------------------------------------------------------------- |
| prechatCaptureGreetingText               | Text for the initial greeting message.                                        |
| prechatCaptureNameLabel                  | Label displayed for the default form's first field.                           |
| prechatCaptureNamePlaceholder            | Placeholder for the default form's first field.                               |
| prechatCaptureEmailLabel                 | Label displayed for the default form's second field.                          |
| prechatCaptureEmailPlaceholder           | Placeholder for the default form's second field.                              |
| prechatCaptureConfirmationText           | Text for the confirmation message sent when the form is completed.            |
| prechatCaptureMailgunLinkingConfirmation | Text for the notification message when a user has linked their email address. |

##### Example

```javascript
var initPromise = BotfriendsWebchat.init({
    integrationId: '<integration-id>',
    // Leave unspecified for US region (default)
    region: 'eu-1',
    // For authenticated mode
    jwt: 'your_jwt',
    userId: 'user_id',
    locale: 'en-US',
    customColors: {
        brandColor: '65758e',
        conversationColor: '65758e',
        actionColor: '65758e',
    },
    menuItems: {
        imageUpload: true,
        fileUpload: true,
        shareLocation: true
    },
    fixedIntroPane: false,
    prechatCapture: {
        avatarUrl: 'http://domain.com/images/avatar.png',
        enabled: true,
        enableEmailLinking: true,
        fields: [
            {
                type: 'email',
                name: 'email',
                label: 'Email',
                placeholder: 'your@email.com'
            },
            {
                type: 'text',
                name: 'company-website',
                label: 'Company website',
                placeholder: 'mycompany.com'
            },
            {
                type: 'select',
                name: 'company-size',
                label: 'Company size',
                placeholder: 'Choose a number...',
                options: [
                    {
                        name: '1-10',
                        label: '1-10 employees'
                    },
                    {
                        name: '11-50',
                        label: '11-50 employees'
                    },
                    {
                        name: '51+',
                        label: '51+ employees'
                    }
                ]
            }
        ]
    },
    customText: {
        actionPaymentCompleted: 'Payment Completed',
        actionPaymentError: 'An error occurred while processing the card. <br> Please try again or use a different card.',
        actionPostbackError: 'An error occurred while processing your action. Please try again.',
        clickToRetry: 'Message not delivered. Click to retry.',
        clickToRetryForm: 'Form not submitted. Click anywhere on the form to retry.',
        connectNotificationText: 'Give us a way to reach out:',
        connectNotificationSingleText: 'Be notified when you get a reply.',
        connectNotificationSingleButtonText: 'Turn on <name> notifications',
        connectNotificationOthersText: 'others',
        conversationTimestampHeaderFormat: 'MMMM D YYYY, h:mm A',
        couldNotConnect: 'Couldn\'t connect. You can {retry}. '
        couldNotConnectInner: 'retry connecting now'
        couldNotConnectWithRetry: 'Couldn\'t connect. We\'ll keep retrying, or you can {retry}.',
        couldNotConnectWithRetryInner: 'try now',
        emailChangeAddress: 'Change my email',
        emailDescription: 'To be notified by email when you get a reply, enter your email address.',
        emailFieldLabel: 'Your email',
        emailFieldPlaceholder: 'Your email address',
        emailFormButton: 'Continue',
        fetchHistory: 'Load more',
        fetchingHistory: 'Retrieving history...',
        fileTooLargeError: 'Max file size limit exceeded ({size})',
        fileTypeError: 'Unsupported file type.',
        formErrorInvalidEmail: 'Email is invalid',
        formErrorNoLongerThan: 'Must contain no more than ({characters}) characters',
        formErrorNoShorterThan: 'Must contain at least ({characters}) characters',
        formErrorUnknown: 'This doesn\'t look quite right',
        formFieldSelectPlaceholderFallback: 'Choose one...',
        frontendEmailChannelDescription: 'To talk to us using email just send a message to our email address and we\'ll reply shortly:',
        headerText: 'How can we help?',
        imageClickToReload: 'Click to reload image.',
        imageClickToView: 'Click to view {size} image.',
        imagePreviewNotAvailable: 'Preview not available.',
        inputPlaceholder: 'Type a message...',
        inputPlaceholderBlocked: 'Complete the form above...',
        introAppText: 'Message us below or from your favorite app.',
        introductionText: 'We\'re here to talk, so ask us anything!',
        lineChannelDescription: 'To talk to us using LINE, scan this QR code using the LINE app and send us a message.',
        linkError: 'An error occurred when attempting to generate a link for this channel. Please try again.',
        locationNotSupported: 'Your browser does not support location services or it’s been disabled. Please type your location instead.',
        locationSecurityRestriction: 'This website cannot access your location. Please type your location instead.',
        locationSendingFailed: 'Could not send location',
        locationServicesDenied: 'This website cannot access your location. Allow access in your settings or type your location instead.',
        messageError: 'An error occured while sending your message. Please try again.',
        messageIndicatorTitlePlural: '({count}) New messages',
        messageIndicatorTitleSingular: '({count}) New message',
        messageRelativeTimeDay: '{value}d ago',
        messageRelativeTimeHour: '{value}h ago',
        messageRelativeTimeJustNow: 'Just now',
        messageRelativeTimeMinute: '{value}m ago',
        messageTimestampFormat: 'h:mm A',
        messageSending: 'Sending...',
        messageDelivered: 'Delivered',
        messengerChannelDescription: 'Connect your Facebook Messenger account to be notified when you get a reply and continue the conversation on Facebook Messenger.',
        notificationSettingsChannelsDescription: 'You can also talk to us from your favorite app or service.',
        notificationSettingsChannelsTitle: 'Other Channels',
        notificationSettingsConnected: 'Connected',
        notificationSettingsConnectedAs: 'Connected as {username}',
        prechatCaptureGreetingText: 'Hi there 👋\nTo start off, we\'d like to know a little bit more about you:',
        prechatCaptureNameLabel: 'Your name',
        prechatCaptureNamePlaceholder: 'Type your name...',
        prechatCaptureEmailLabel: 'Email',
        prechatCaptureEmailPlaceholder: 'name@company.com',
        prechatCaptureConfirmationText: 'Thanks for that! What can we help you with?',
        prechatCaptureMailgunLinkingConfirmation: 'You\'ll be notified here and by email at {email} once we reply.',
        sendButtonText: 'Send',
        settingsHeaderText: 'Settings',
        shareLocation: 'Share location',
        smsBadRequestError: 'We were unable to communicate with this number. Try again or use a different one.',
        smsCancel: 'Cancel',
        smsChangeNumber: 'Change my number',
        smsChannelDescription: 'Connect your SMS number to be notified when you get a reply and continue the conversation over SMS.',
        smsChannelPendingDescription: 'Check your messages at {number} to confirm your phone number.',
        smsContinue: 'Continue',
        smsInvalidNumberError: 'Your phone number isn\'t valid. Please try again.',
        smsLinkCancelled: 'Link to {appUserNumber} was cancelled.',
        smsLinkPending: 'Pending',
        smsPingChannelError: 'There was an error sending a message to your number.',
        smsSendText: 'Send me a text',
        smsStartTexting: 'Start Texting',
        smsTooManyRequestsError: 'A connection for that number was requested recently. Please try again in {minutes} minutes.',
        smsTooManyRequestsOneMinuteError: 'A connection for that number was requested recently. Please try again in 1 minute.',
        smsUnhandledError: 'Something went wrong. Please try again.',
        tapToRetry: 'Message not delivered. Tap to retry.',
        tapToRetryForm: 'Form not submitted. Tap anywhere on the form to retry.',
        telegramChannelDescription: 'Connect your Telegram account to be notified when you get a reply and continue the conversation on Telegram',
        uploadDocument: 'Upload document',
        uploadInvalidError: 'Invalid file',
        uploadPhoto: 'Upload photo',
        uploadVirusError: 'A virus was detected in your file and it has been rejected',
        unsupportedMessageType: 'Unsupported message type.',
        unsupportedActionType: 'Unsupported action type.',
        viberChannelDescription: 'Connect your Viber account to be notified when you get a reply and continue the conversation on Viber. To get started, scan the QR code using the Viber app.',
        viberChannelDescriptionMobile: 'Connect your Viber account to be notified when you get a reply and continue the conversation on Viber. To get started, install the Viber app and tap Connect.',
        viberQRCodeError: 'An error occurred while fetching your Viber QR code. Please try again.',
        wechatChannelDescription: 'Connect your WeChat account to be notified when you get a reply and continue the conversation on WeChat. To get started, scan this QR code using the WeChat app.',
        wechatChannelDescriptionMobile: 'Connect your WeChat account to be notified when you get a reply and continue the conversation on WeChat. To get started, save this QR code image and upload it in the <a href=\'weixin://dl/scan\'>QR code scanner</a>.',
        wechatQRCodeError: 'An error occurred while fetching your WeChat QR code. Please try again.',
        whatsappChannelDescriptionDesktop: 'Sync your account to WhatsApp by scanning the QR code or clicking the link below.\nThen, send the pre-populated message to validate the sync request. (Your code: {{code}}).',
        whatsappChannelDescriptionMobile: 'Sync your account to WhatsApp by clicking the link below.\nThen, send the pre-populated message to validate the sync request. (Your code: {{code}}).',
        whatsappLinkingError: 'An error occurred while fetching your WhatsApp linking information. Please try again.'
    }
}).then(function() {
    // Your code after init is complete
});
initPromise.then(function() {
    // do something
});
// pass it around...
initPromise.then(function() {
    //do something else
});
```

#### open()

Opens the conversation widget (noop when embedded)

```javascript
BotfriendsWebchat.open();
```

#### close()

Closes the conversation widget (noop when embedded)

```javascript
BotfriendsWebchat.close();
```

#### isOpened()

Tells if the widget is currently opened or closed.

```javascript
BotfriendsWebchat.isOpened();
```

#### login(userId , jwt)

Logs a user in the Web Messenger, retrieving the conversation the user already had on other browsers and/or devices. Note that you don't need to call this after `init` if you passed the user id and jwt already, it's done internally. This returns a promise that resolves when the Web Messenger is ready again.

```javascript
BotfriendsWebchat.login('some-id', 'some-jwt');
```

#### logout()

Logs out the current user and reinitialize the widget with an anonymous user. This returns a promise that resolves when the Web Messenger is ready again.

```javascript
BotfriendsWebchat.logout();
```

#### destroy()

Destroys the Web Messenger and makes it disappear. The Web Messenger has to be reinitialized with `init` to be working again because it also clears up the integration id from the Web Messenger. It will also unbind all listeners you might have with `BotfriendsWebchat.on`.

```javascript
BotfriendsWebchat.destroy();
```

#### sendMessage(message)

Sends a message on the user's behalf

```javascript
BotfriendsWebchat.sendMessage({
    type: 'text',
    text: 'hello'
});
// OR
BotfriendsWebchat.sendMessage('hello', '<conversation-id>');
```

#### startTyping()

Sends an event indicating the appUser has started typing. When called, a `stopTyping` timer will be created and will call `stopTyping()` after 10s, unless `stopTyping()` has been called manually. Everytime `startTyping()` is called the timer is reset. If `startTyping()` is called repeatedly, an event will only be sent if the time since the last sent event is greater then 10s.

If **conversationId** is not provided, the currently loaded conversation will be used.

```javascript
BotfriendsWebchat.startTyping();
```

#### stopTyping()

Sends an event indicating the appUser has stoped typing.

```javascript
BotfriendsWebchat.stopTyping();
```

#### triggerPostback(actionId, conversationId)

Trigger a `postback` action on the user's behalf.
The `actionId` is the `_id` property of the targeted action.

If you have the `_id` of the targetted `postback` action, you can pass it directly to `triggerPostback`.

If **conversationId** is not provided, the currently loaded conversation will be used.

```javascript
const actionId = '5a747faa065bbe4e7804f2a4';
BotfriendsWebchat.triggerPostback(actionId, '62565b5c2b4039adff80b7fd');
```

Otherwise, you can get the `_id` of an action by using `BotfriendsWebchat.getConversation()`, e.g.

```javascript
const conversation = BotfriendsWebchat.getConversation();
console.log(conversation.messages);
// [
//     {
//         "text": "Do you want to continue?",
//         "actions": [
//             {
//                 "payload": "text:continue",
//                 "text": "Continue conversation",
//                 "_id": "5a7c65211aaa9b61f69c95e3",
//                 "type": "postback"
//             }
//         ],
//         "type": "text",
//         "role": "appMaker",
//         "_id": "5a7c65211aaa9b61f69c95e2",
//         // ...
//     }
// ]
// Indicate that the user has clicked on the "Continue conversation" postback action.
BotfriendsWebchat.triggerPostback(conversation.messages[0].actions[0]._id);
```

#### updateUser(user)

Updates user information

```javascript
BotfriendsWebchat.updateUser({
    givenName: 'Updated',
    surname: 'Name',
    email: 'updated@email.com',
    properties: {
      'justGotUpdated': true
    }
});
```

#### getUser()

Returns the current user.

```javascript
var user = BotfriendsWebchat.getUser()
```

#### getConversation()

Returns the conversation if it exists

```javascript
var conversation = BotfriendsWebchat.getConversation();
// Data object
conversation = {
    _id: '<conversation-id>',
    unreadCount: 0,
    lastUpdatedAt: 1581010017.596,
    type: "personal",
    participants: [
        {
            _id: '<participant-id>',
            appUserId: '<appUser-id>',
            unreadCount: 0,
            lastRead: 1581010017.596
        }
    ],
    metadata: {},
    messages: [
        {
            role: 'appUser',
            authorId: '6ac41c8b32e704f3cdb9052d',
            name: 'Some user',
            _id: '5e6022c9cb55158bfd53f845',
            type: "text",
            received: 1583358665.139,
            text: 'Hello',
            source: {
                type: 'web',
                id: 'c38ae913af7c4ef3800b339ee529c579',
                integrationId: '5d8274d4aa780a5483f0ee56'
            }
        }
    ]
}
```

#### loadConversation(conversationId)

Loads a conversation into the current session

```javascript
BotfriendsWebchat.loadConversation('<conversation-id>');
```

#### startConversation()

Creates a user and conversation on the server, allowing the business to reach out proactively to the user via the public API.

It is strongly recommended to only call this method in the case where a message is likely to be sent.

This method is called automatically when starting a conversation via the `sendMessage` method, or when a user sends a message via the conversation view.

If a conversation already exists for the current user, this call is a no-op.

```javascript
BotfriendsWebchat.startConversation();
```

#### markAllAsRead()

Marks all unread messages as read.

```javascript
BotfriendsWebchat.markAllAsRead();
```

#### showNotificationChannelPrompt()

Displays a prompt to the user suggesting the linking of the current chat instance with other 3rd-party apps.

```javascript
BotfriendsWebchat.showNotificationChannelPrompt();
```

#### setPredefinedMessage(message)

Prefills the user's chat input with a predefined message.

```javascript
BotfriendsWebchat.setPredefinedMessage(message);
```

#### setDelegate(delegate)

Sets a delegate on the conversation. BotfriendsWebchat must be initialized before calling this method. See the [delegate](#delegate) section for more details.

```javascript
BotfriendsWebchat.setDelegate(delegate);
```

### Delegate

BotfriendsWebchat allows you to set a delegate to receive callbacks when important changes happen in the conversation.
To set a delegate, pass the `delegate` parameter in to [init options](#options), or use the [setDelegate](#setdelegatedelegate) method. The `delegate` object may optionally contain `beforeDisplay`, `beforeSend`, `beforePostbackSend` and `onInvalidAuth` delegate functions.

A `data` object is passed down with all the delegate events except `onInvalidAuth`. This is a read-only object containing a truncated version of the conversation associated with the event.

```javascript
const delegate = {
    beforeDisplay(message, data) {
        if (data.conversation._id === '<my-conversation-id>') {
            message.name = 'Acme Blank';
        }
        return message;
    },
    beforeSend(message, data) {
        return message;
    },
    beforePostbackSend(postback, data) {
        return postback;
    },
    onInvalidAuth() {
        return new Promise((resolve) =>
            resolve('<my-new-auth-token>')
        )
    }
}
// Passing delegate as an init parameter
BotfriendsWebchat.init({
    integrationId: '<integration-id>',
    delegate
});
// Using setDelegate
BotfriendsWebchat.init({ integrationId: '<integration-id>' }).then(() => {
    BotfriendsWebchat.setDelegate(delegate);
});
```

#### beforeDisplay

The `beforeDisplay` delegate allows a message to be hidden or modified before it is displayed in the conversation. This delegate should return a falsy value such as `null` to hide the message. It can also return a modified message object in order to change what the user will see rendered in their conversation history. Note that this change affects the client side rendering only; the server side copy of this message can not be modified by this delegate.

```javascript
BotfriendsWebchat.init({
    integrationId: '<integration-id>',
    delegate: {
        beforeDisplay(message, data) {
            if (data.conversation._id === '<conversation-id>' && message.metadata && message.metadata.isHidden) {
                return null;
            }
            return message;
        }
    }
});
```

#### beforeSend

The `beforeSend` delegate method allows you to modify properties of a message before sending it to BotfriendsWebchat.
The modified message must be returned for it to take effect.

Note that when a file or an image is uploaded, only the message `metadata` may be updated. Other message properties such as `type` or `text` won't be considered.

```javascript
BotfriendsWebchat.init({
    integrationId: '<integration-id>'
    delegate: {
        beforeSend(message, data) {
            if (data.conversation._id === '<conversation-id>') {
                message.metadata = {
                    any: 'info'
                };
            }
            return message;
        }
    }
});
```

If no language detection is enabled in BOTfriends X, the `beforeSend` delegate method can also be used to pass a language code to be used for intent detection. 
The language codes (e.g. en, de, fr, es) you can use can be found in your NLU integration in BOTfriends X. Make sure to use only language codes that exist in your BOTfriends X project.

```javascript
BotfriendsWebchat.init({
    integrationId: '<integration-id>'
    delegate: {
        beforeSend(message, data) {
            message.metadata = {
                languageCode: 'de'
            }
            return message;
        }
    }
});
```

#### beforePostbackSend

The `beforePostbackSend` delegate method allows you to modify properties of a postback before sending it to BotfriendsWebchat.
The modified postback must be returned for it to take effect.

```javascript
BotfriendsWebchat.init({
   integrationId: '<integration-id>',
   delegate: {
       beforePostbackSend(postback) {
           if (data.conversation._id === '<conversation-id>') {
               postback.metadata = {
                   any: 'info'
               };
           }
           return postback;
       }
   }
});
```

#### onInvalidAuth

The `onInvalidAuth` delegate notifies the delegate of a failed request due to invalid credentials and allows the implementer to set a new auth token in order to retry the request. The delegate must return a new JWT token as a `string` or `Promise<string>` that will resolve into the JWT.

```javascript
BotfriendsWebchat.init({
    integrationId: '<integration-id>',
    delegate: {
        onInvalidAuth() {
            return '<my-new-auth-token>';
        }
    }
});
```

### Events

If you want to make sure your events are triggered, try to bind them before calling `BotfriendsWebchat.init`.

To bind an event, use `BotfriendsWebchat.on(<event name>, <handler>);`. To unbind events, you can either call `BotfriendsWebchat.off(<event name>, handler)` to remove one specific handler, call `BotfriendsWebchat.off(<event name>)` to remove all handlers for an event, or call `BotfriendsWebchat.off()` to unbind all handlers.

#### ready

```javascript
// This event triggers when init completes successfully... Be sure to bind before calling init!
BotfriendsWebchat.on('ready', function(){
    console.log('the init has completed!');
});
BotfriendsWebchat.init(...).then(function() {
    // Your code after init is complete
});
```

#### destroy

```javascript
// This event triggers when the widget is destroyed.
BotfriendsWebchat.on('destroy', function(){
    console.log('the widget is destroyed!');
});
BotfriendsWebchat.destroy();
```

#### participant:added

```javascript
// This event triggers when a participant is added to a conversation
BotfriendsWebchat.on('participant:added', function(participant, data) {
    console.log(`A participant was added to conversation ${data.conversation._id}: `, participant);
});
```

#### participant:removed

```javascript
// This event triggers when a participant is removed from a conversation
BotfriendsWebchat.on('participant:removed', function(participant, data) {
    console.log(`A participant was removed from conversation ${data.conversation._id}: `, participant);
});
```

#### conversation:added

```javascript
// This event triggers when a conversation is added
BotfriendsWebchat.on('conversation:added', function(participants, data) {
    console.log(`Conversation ${data.conversation._id} was added with following participants: `, participants);
});
```

#### conversation:read

```javascript
// This event triggers when a participant in a sdkGroup chat reads a message
BotfriendsWebchat.on('conversation:read', function(payload, data) {
    if (data.role === 'appMaker') {
        console.log(`Conversation ${data.conversation._id} was read by appMaker`);
    } else if (data.role === 'appUser') {
        console.log(`Conversation ${data.conversation._id} was read by appUserId: ${payload.appUserId}`);
    }
});
// Data object, if the conversation was read by the appMaker, the appUser property will not exist
payload = {
    appUserId: '<appUser-id>',
    lastRead: 1581010017.596,
    role: 'appUser'
}
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

#### conversation:removed

```javascript
// This event triggers when a conversation is removed
BotfriendsWebchat.on('conversation:removed', function(data) {
    console.log(`Conversation ${data.conversation._id} was removed`);
});
// data object
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

#### message:received

```javascript
// This event triggers when the user receives a message
BotfriendsWebchat.on('message:received', function(message, data) {
    console.log(`The user received a message in conversation ${data.conversation._id}: `, message);
});
// data object
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

#### message:sent

```javascript
// This event triggers when the user sends a message
BotfriendsWebchat.on('message:sent', function(message, data) {
    console.log(`The user sent a message in conversation ${data.conversation._id}: `, message);
});
// data object
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

#### message

```javascript
// This event triggers when a message was added to the conversation
BotfriendsWebchat.on('message', function(message, data) {
    console.log(`A message was added in the conversation ${data.conversation._id}: `, message);
});
// data object
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

#### unreadCount

```javascript
// This event triggers when the number of unread messages changes
BotfriendsWebchat.on('unreadCount', function(unreadCount, data) {
    console.log(`the number of unread messages was updated for conversation ${data.conversation._id}:`, unreadCount);
});
// data object
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

#### widget:opened

```javascript
// This event triggers when the widget is opened
BotfriendsWebchat.on('widget:opened', function() {
    console.log('Widget is opened!');
});
```

#### widget:closed

```javascript
// This event triggers when the widget is closed
BotfriendsWebchat.on('widget:closed', function() {
    console.log('Widget is closed!');
});
```

#### connected

```javascript
// This event triggers when an active connection has been established for the first time,
// or when the connection has been re-established after a `disconnect` event.
BotfriendsWebchat.on('connected', function(e) {
    console.log('Connected');
});
```

#### disconnected

```javascript
// This event triggers when an active connection is lost
// While disconnected, the client will not be able to recieve messages
BotfriendsWebchat.on('disconnected', function(e) {
    console.log('Disonnected');
});
```

#### typing:start

```javascript
// This event triggers when appMaker starts typing. The associated conversation is passed in the argument.
BotfriendsWebchat.on('typing:start', function(data) {
    console.log(`${data.name} started typing!`, data.conversation._id);
});
// If the name and avatarUrl for the typing user is known it will be available as properties in the data object
// Data object
data = {
    conversation: {
        _id: '<conversation-id>',
    },
    avatarUrl: 'http://path.com/to/avatar-url-of-user',
    name: 'Name of Typing User',
}
```

#### typing:stop

```javascript
// This event triggers when appMaker stops typing. The associated conversation is passed in the argument.
BotfriendsWebchat.on('typing:stop', function(data) {
    console.log(`${data.name} stopped typing!`, data.conversation._id);
});
// data object
data = {
    conversation: {
        _id: '<conversation-id>'
    }
}
```

### Embedded mode

As describe above, to activate the embedded mode, you need to pass `embedded: true` when calling `BotfriendsWebchat.init`. By doing so, you are disabling the auto-rendering mechanism and you will need to call `BotfriendsWebchat.render` manually. This method accepts a DOM element which will be used as the container where the widget will be rendered.

```javascript
BotfriendsWebchat.init({
    integrationId: '<integration-id>',
    embedded: true
});
BotfriendsWebchat.render(document.getElementById('chat-container'));
```

The embedded widget will take full width and height of the container. You must give it a height, otherwise, the widget will collapse.

### Authenticating the user: init() vs login()

For authenticated user scenarios, a user's credentials can be passed in to either `init()` or `login()`. The question remains: when should you use which?

When `init()` is called without a `userId` and `jwt`, the UI will be initialized in an anonymous user context, and the user will be able to send messages as an anonymous user. After `init()` has completed, `login()` may be called to authenticate the user and resume any existing conversation. This is useful if you want your users to be able to send messages before they've logged in to your website. Once a user does login and the browser is issued a valid `jwt`, `login()` may be called so that the user may continue the conversation in an authenticated session.

If however your use case requires that all users be logged in before they may send messages, then you should consider passing `userId` and `jwt` directly to the `init()` call upfront.

### Automatic Welcome Message

Some use-cases require to send a message to the user directly upon opening the web chat widget. This behavior can be implemented by listening to the `'widget:opened'` event, and then calling the `startConversation()` method, if the user is new to the chat:

```javascript
BotfriendsWebchat.on('widget:opened', function() {
  var conversation = BotfriendsWebchat.getConversation()
  var isNewUser = !conversation?.messages?.length
  if (isNewUser) {
    BotfriendsWebchat.startConversation()
  }
})
```
#### v1 Projects
The connected bot needs to have a ‘WELCOME’ event added in the intent to be sent automatically. More information can be found in the [BOTfriends X docs](https://docs.botfriendsx.com/#/IM/Events?id=send-a-start-message-in-the-website-chat-welcome).

#### v2 Projects
`BotfriendsWebchat.startConversation()` will automatically trigger the **Welcome Node** in the **Welcome Dialog**. More information can be found in the [BOTfriends X Knowledge Hub](https://knowledgehub.botfriendsx.com/#/BotBuilder/WorkWithDialogs?id=welcome-node).

### Enable Markdown in Webchat

1. To enable display of Markdown Language in bot responses add the [markedjs](https://github.com/markedjs/marked) script to the html file:
```html
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
```
2. Add markdown parsing to the `delegate.beforeDisplay` method:

```js
BotfriendsWebchat.init({
  // ...
  delegate: {
    beforeDisplay(message, data) {
      // ...
      const parsed = marked.parse(message.text)
      const webMessenger = document.getElementById('web-messenger-container')
      const messageContainer = webMessenger.contentWindow.document.getElementsByClassName('messages-container')[0]
      const messageId = message?._id ?? ''
      const [element] = messageContainer.querySelectorAll(`[data-for=tooltip-${messageId}]`)
      if (element) {
        const textSpan = element.getElementsByClassName('message-item')[0]
        const isKnowledgeBaseSource = message?.metadata?.isKnowledgeBaseSource
        textSpan.innerHTML = isKnowledgeBaseSource ? `<i>${parsed}</i>` : parsed
        for (const child of textSpan.children) {
          child.style = `padding: 0px!important; margin: 0px!important`
          const hrefs = child.querySelectorAll('a')
          for (const href of hrefs) {
            href.setAttribute('target', '_blank')
          }
        }
        const lists = textSpan.querySelectorAll('ul, ol')
        for (const list of lists) {
          list.style = 'padding-left: 16px;'
        }
      }
      return message
    }
  }
})
```

3. Add Markdown to your bot responses. Use the [Cheat Sheet](https://www.markdownguide.org/cheat-sheet/) to find the tags that match your needs

## Content Security Policy

If your deployment requires [CSP compatibility](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP), add the following meta tag to your configuration.

```html
<meta
    http-equiv="Content-Security-Policy"
    content="
    connect-src
        wss://*.{{YOUR_DOMAIN_NAME}}
        https://*.{{YOUR_DOMAIN_NAME}};
    font-src
        https://*.{{YOUR_DOMAIN_NAME}};
    script-src
        https://*.{{YOUR_DOMAIN_NAME}};
    style-src
        https://*.{{YOUR_DOMAIN_NAME}};
    img-src
        blob:
        https://*.gravatar.com
        https://*.{{YOUR_DOMAIN_NAME}};"
/>
```

Note that an equivalent configuration can be done [server side](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy).

According to the channels you use, other domains may need to be added (these are used to display QR codes to link the Web Messenger conversation):

-   LINE: https://qr-official.line.me
-   WeChat: https://mp.weixin.qq.com
-   WhatsApp: https://wa.me

Note that your CSP configuration should also include any domains used to host images or files sent in messages.
If you require `blob:` to be excluded for `img-src`, you must disable the image upload feature via the [init settings](#initoptions).

## Acknowledgements

https://github.com/lipis/flag-icon-css
