# Botfriends Agent Chat Widget

The Agent Chat Widget embeds a Botfriends X chat in your website. You add one
script tag to your page; everything else is optional.

This guide starts with the minimal setup and then adds one capability per step:

1. [Add the embed snippet](#step-1-add-the-embed-snippet) – the chat appears on your page.
2. [Customize the widget](#step-2-customize-the-widget) – colors, texts, and behavior.
3. [Control the widget from your page](#step-3-control-the-widget-from-your-page) –
   open the chat, send messages, react to events.
4. [Initialize the widget yourself](#step-4-initialize-the-widget-yourself) – decide
   when the widget starts.
5. [Embed the chat in a page element](#step-5-embed-the-chat-in-a-page-element) –
   inline or fullscreen instead of floating.
6. [Pass user data to the bot](#step-6-pass-user-data-to-the-bot) – metadata and
   signed metadata.
7. [Intercept messages with hooks](#step-7-intercept-messages-with-hooks) – run
   your own code before messages are shown or sent.

The [reference](#reference) at the end lists every option, method, and event.

## Step 1: Add the embed snippet

Copy the embed snippet from the Botfriends X project configuration and paste it
into the page where the chat should appear:

```html
<script
  defer
  src="https://webchat.botfriendsx.com/scripts/v1/widget.js"
  data-webchat-id="YOUR_WEBCHAT_ID"
  data-auto-init="true"
></script>
```

That is all you need. When the page loads, the chat launcher appears in the
corner of the page with the appearance and texts configured in Botfriends X.

What the snippet does:

- `src` loads the widget. The URL is environment-specific; use the exact URL from
  Botfriends X and do not copy a development or staging URL into production.
- `data-webchat-id` identifies your Botfriends X webchat.
- `data-auto-init="true"` starts the widget automatically.
- `defer` loads the script without blocking your page.

Add the snippet only once per page.

### The three global objects

The widget can be used without writing any JavaScript. If you do want to
configure or control it from JavaScript, the widget uses three global objects on
`window`. Each has its own job:

| Global object                    | What it is for                                                                                                                          | Who creates it                                                          |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `window.BotfriendsWidgetOptions` | Settings for the automatically started widget ([Step 2](#step-2-customize-the-widget)).                                                 | **You**, before the widget script runs. Optional.                       |
| `window.BotfriendsWidgetApi`     | Starts and removes the widget: `init(...)`, `destroy()` ([Step 4](#step-4-initialize-the-widget-yourself)).                             | The widget script, as soon as it has loaded.                            |
| `window.BotfriendsWidget`        | The running widget. Use it to open the chat, send messages, and listen to events ([Step 3](#step-3-control-the-widget-from-your-page)). | The widget, each time it starts (automatically or through `init(...)`). |

In short: `BotfriendsWidgetOptions` is what you **hand in**, `BotfriendsWidgetApi`
**starts** the widget, and `BotfriendsWidget` is the **running** widget you control.

There is only ever one widget per page. Starting it again replaces the previous
one, and `window.BotfriendsWidget` then refers to the new widget.

## Step 2: Customize the widget

By default, the widget uses the configuration from Botfriends X. To override
individual settings on a specific page, define `window.BotfriendsWidgetOptions`
**before** the embed snippet:

```html
<script>
  window.BotfriendsWidgetOptions = {
    appearance: {
      brandColor: '#2457a6',
      userMessageColor: '#2457a6',
    },
    localization: {
      defaultLanguage: 'en',
    },
  }
</script>
<script
  defer
  src="https://webchat.botfriendsx.com/scripts/v1/widget.js"
  data-webchat-id="YOUR_WEBCHAT_ID"
  data-auto-init="true"
></script>
```

You only need to list the settings you want to change. Everything else keeps
its Botfriends X value. Settings from your page take precedence over the
Botfriends X configuration, which takes precedence over the widget's defaults.
Invalid or unsupported settings are ignored and reported in the browser console.

All available settings are listed in the
[configuration reference](#configuration-options).

`window.BotfriendsWidgetOptions` is only read when the widget starts
automatically. If you start the widget yourself ([Step 4](#step-4-initialize-the-widget-yourself)),
pass the same object to `init(...)` instead.

## Step 3: Control the widget from your page

Once the widget has started, `window.BotfriendsWidget` gives you access to it.
With the embed snippet from Step 1, the widget has started by the time the
page's `DOMContentLoaded` event fires:

```html
<button id="help-button">Chat with us</button>
<script>
  window.addEventListener('DOMContentLoaded', () => {
    const widget = window.BotfriendsWidget

    document.getElementById('help-button').addEventListener('click', () => {
      widget.open({ focus: true })
    })
  })
</script>
```

Common actions:

```js
const widget = window.BotfriendsWidget

widget.open({ focus: true }) // open the chat panel and move focus into it
widget.close() // close the chat panel
widget.sendMessage('I need help with my order.')

widget.hide() // hide the whole widget, including the launcher
widget.show() // show it again
```

`open()`/`close()` control the chat panel. `show()`/`hide()` control whether the
widget is visible on the page at all.

To change settings while the widget is running, use `updateConfig(...)`. It
accepts the same settings as `window.BotfriendsWidgetOptions`:

```js
window.BotfriendsWidget.updateConfig({
  appearance: { brandColor: '#0a7d4f' },
})
```

To react to what happens in the chat, subscribe to an event. `on(...)` returns a
function that removes the listener again:

```js
const unsubscribe = window.BotfriendsWidget.on('message:received', event => {
  console.log('Received chat message:', event.detail)
})

// Later, when you no longer need the listener:
unsubscribe()
```

All methods and events are listed in the [JavaScript API reference](#javascript-api).

## Step 4: Initialize the widget yourself

Use this when your page needs to decide **when** the widget starts, for example
only after a cookie banner was accepted or only on certain routes.

Remove `data-webchat-id` and `data-auto-init` from the script tag. The script then
only loads the widget and provides `window.BotfriendsWidgetApi`. Start the widget
with `init(...)` when you are ready:

```html
<script defer src="https://webchat.botfriendsx.com/scripts/v1/widget.js"></script>
<script>
  window.addEventListener('DOMContentLoaded', () => {
    const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {
      appearance: {
        brandColor: '#2457a6',
      },
    })

    widget.open({ focus: true })
  })
</script>
```

`init(webchatId, options?, mountOptions?)`:

- `webchatId` (required): your Botfriends X webchat ID.
- `options` (optional): the same settings as `window.BotfriendsWidgetOptions` in Step 2.
- `mountOptions` (optional): where and how the widget is placed on the page, see
  [Step 5](#step-5-embed-the-chat-in-a-page-element).

`init(...)` returns the running widget. This is the same object that is also
available as `window.BotfriendsWidget`, so all methods from Step 3 work on it.

To remove the widget from the page, call `window.BotfriendsWidgetApi.destroy()`.
Call `init(...)` again to start a new widget.

## Step 5: Embed the chat in a page element

By default, the widget floats in a corner of the page. To show the chat inside
an element of your page instead, pass that element as `mountOptions.container`
to `init(...)`. Embedded mode fills the element's width and height, so give the
element a size:

```html
<div id="support-chat" class="support-chat"></div>
<script defer src="https://webchat.botfriendsx.com/scripts/v1/widget.js"></script>
<script>
  window.addEventListener('DOMContentLoaded', () => {
    const container = document.getElementById('support-chat')

    const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {}, { container })

    widget.open()
  })
</script>
```

```css
.support-chat {
  width: 100%;
  height: 600px;
}
```

For a fullscreen chat, make the container fill the viewport. There is no
separate fullscreen setting; your page's CSS controls the size:

```css
.support-chat {
  position: fixed;
  inset: 0;
  z-index: 1000;
  width: 100vw;
  height: 100dvh;
}
```

The container must be an `HTMLElement`. In embedded mode, the launcher button is
hidden by default; set `showLauncher: true` in `mountOptions` to display it.

## Step 6: Pass user data to the bot

You can send additional data about the visitor or the page along with each
message, for example a customer number. There are two ways, which can be
combined:

- **Metadata**: plain values, set in the browser. Simple, but not verified.
- **Signed metadata (JWT)**: values signed by your server. The gateway verifies
  them before passing them to the bot.

To read the metadata in your agent, see
[Accessing metadata](#accessing-metadata).

### Metadata

Pass JSON-compatible values under `integration.metadata`:

```js
const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {
  integration: {
    metadata: {
      customerId: '123',
    },
  },
})
```

This works the same way in `window.BotfriendsWidgetOptions` and
`updateConfig(...)`. The widget also adds the current full page URL, including
its query string and fragment, to outgoing messages.

Things to know:

- Metadata and the page URL are unsigned and visible to the receiving
  conversation. Do not include secrets, access tokens, or other sensitive values.
- The complete metadata object is limited to 16 KiB of UTF-8 JSON and eight
  nested object/array levels. If your values and the page URL together exceed the
  limit, your values are omitted for that message. If the URL alone exceeds the
  limit, the metadata field is omitted.
- Invalid metadata is ignored with a console warning instead of blocking a
  message. An invalid `updateConfig(...)` call keeps the previous valid settings.

### Signed metadata (JWT)

Use `integration.metadataJWT` when the bot must be able to trust the values. Your
server signs the values; the gateway verifies the signature and forwards only
verified values to the bot.

**1. Create the token on your server.** Sign it with the `HS256` algorithm using
the signing secret for your webchat. If you have not received a signing secret,
request it through your usual secure Botfriends X provisioning channel. Never
put the signing secret in browser JavaScript, the embed snippet, or a public
repository; only the signed token may be sent to the browser.

The token payload _must_ contain:

- `projectId`: the ID of the Botfriends X project that owns the webchat.
- `metadata`: a JSON object with the values to pass to the bot (same size limits
  as above).

Example using the `jsonwebtoken` package:

```js
const jwt = require('jsonwebtoken')

const metadataJWT = jwt.sign(
  {
    projectId: 'YOUR_PROJECT_ID',
    metadata: {
      customerId: '123',
      customerTier: 'gold',
    },
  },
  process.env.WEBCHAT_METADATA_JWT_SECRET,
  {
    algorithm: 'HS256',
    expiresIn: '5m',
  },
)
```

Standard `exp` and `nbf` claims are checked when present. The gateway does not
require an expiration or limit the token age, so use a short expiration.

**2. Pass the token to the widget:**

```js
const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {
  integration: {
    metadataJWT,
  },
})
```

The token is sent with each outgoing message. The gateway verifies it and
forwards only the verified `metadata` to the bot. The token itself is not added
to the message, stored with the conversation, or forwarded to the bot.

If the token is invalid, expired, signed with the wrong secret, or its
`projectId` does not match the webchat's project, no verified metadata is
forwarded. The message itself is still delivered.

For tokens that change per message, return a fresh token from the `beforeSend`
hook ([Step 7](#beforesend)).

Signed metadata is not a login: it does not identify or authenticate the
visitor, and its contents are still visible in the browser. The widget manages
its own connection to the chat gateway; you do not need to provide a token for
that.

### Accessing metadata

You can read these values in your agent via system collected data:

- Metadata: `{channelInformation.channelProperties.additionalInformation.metadata.<key>}`
- Signed metadata: `{channelInformation.channelProperties.additionalInformation.verifiedMetadata.<key>}`

## Step 7: Intercept messages with hooks

Hooks let you run your own code before a message is shown or sent. Register them
under `integration.hooks`. Each hook has a `handler` function, which may return a
value directly or a Promise, and an optional `timeoutMs`:

```js
const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {
  integration: {
    hooks: {
      beforeDisplay: {
        handler: (message, { element }) => {
          element.classList.add(`customer-message--${message.role}`)
          return true
        },
      },
      beforeSend: {
        handler: message => {
          return {
            message: {
              ...message,
              metadata: {
                ...message.metadata,
                custom: {
                  ...message.metadata?.custom,
                  source: 'website',
                },
              },
            },
            metadataJWT: getFreshMetadataJWT(), // your own function
          }
        },
        timeoutMs: 3000,
      },
    },
  },
})
```

The two supported hooks are `beforeDisplay` and `beforeSend`.

Events from Step 3 only notify you; hooks can change or stop a message.

### `beforeDisplay`

Runs for each user, agent, and system message before it becomes visible,
including earlier messages of the conversation and carousel cards. It runs again
each time the message is rendered, for example when the chat is closed and
reopened, so apply styling every time it runs.

The handler receives the message and a context object. `context.element` is the
rendered message element. Return `false` to hide the message; return `true` or
nothing to show it.

The widget renders inside a Shadow DOM, so `document.querySelector(...)` cannot
find its elements. Use `context.element`, or query the widget's `shadowRoot`:

```js
const root = document.querySelector('[data-botfriends-widget="root"]')?.shadowRoot
const element = root?.querySelector(`[data-message-id="${CSS.escape(message.id)}"]`)
```

### `beforeSend`

Runs once for each outgoing message before it is sent. Return nothing to send
the message unchanged, or an object with these optional fields:

- `message`: the changed message. Only `payload`, `metadata`, `displayText`, and
  `languageCode` are used; omitted fields keep their original values. The
  message stays a user message and keeps its `id`. User payloads must be valid
  text, event, or file payloads.
- `metadataJWT`: a signed metadata token for this message only. A string replaces
  the configured `integration.metadataJWT`, `null` sends no token, and leaving it
  out keeps the configured token. Put it next to `message`, not inside it.
- `send`: set to `false` to cancel sending. The message is removed from the
  conversation and its text is put back into the input field.

Invalid or oversized metadata, or an invalid payload, is ignored with a warning
and the original message is sent. If sending has to be retried, the widget
reuses the result and does not run the hook again.

### Timeouts and errors

The default timeout is three seconds; the maximum is 30 seconds. If a handler
throws, rejects, or times out, the widget continues as if there were no hook:
`beforeDisplay` shows the message and `beforeSend` sends the original message. A
timeout cannot interrupt a handler that runs synchronously.

### Changing hooks later

`updateConfig(...)` replaces individual hooks. Set a hook to `null` to remove it;
hooks not mentioned in the update stay registered. Unknown hook names and hooks
without a `handler` function are ignored with a console warning. A changed
`beforeDisplay` hook affects only messages rendered afterwards.

## Demo mode

Set `system.disableInteraction: true` when starting the widget to show a preview
without a real conversation, connection, or authentication. This setting cannot
be changed with `updateConfig(...)`; destroy the widget and start it again.

In demo mode, `addMessages(messages)` and `clearMessages()` manage the preview
content. Messages need a `role` and a matching `payload`; `id` and `createdAt`
are generated when omitted. Preview content is never sent to the gateway.

## Reference

### Configuration options

These settings can be used in `window.BotfriendsWidgetOptions`, in the `options`
argument of `init(...)`, and in `updateConfig(...)`. All of them are optional.
Nested settings are merged individually, so changing one field keeps its sibling
fields.

| Field                                                                            | Supported values                                                                  |
| -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `name`, `description`                                                            | Configuration name and description strings.                                       |
| `lastUpdated`                                                                    | Configuration update timestamp as a non-negative integer.                         |
| `appearance.useAvatar`, `appearance.avatarUrl`                                   | Whether to show an avatar and its image URL.                                      |
| `appearance.useInfoBanner`                                                       | Whether to show the information banner.                                           |
| `appearance.brandColor`                                                          | Main brand color as a 3- or 6-digit hex color, for example `#2457a6`.             |
| `appearance.agentMessageColor`, `appearance.userMessageColor`                    | Agent and user message colors as 3- or 6-digit hex colors.                        |
| `launcher.useIcon`, `launcher.iconUrl`                                           | Whether to use a custom launcher icon and its URL.                                |
| `launcher.size`                                                                  | `small`, `medium`, `large`, or a number (custom size in pixels).                  |
| `launcher.color`                                                                 | Launcher color.                                                                   |
| `conversation.fileUpload`                                                        | Enable or disable file upload.                                                    |
| `conversation.languageSelection`                                                 | Enable or disable conversation language selection.                                |
| `conversation.restartConversation`                                               | Enable or disable restarting a conversation.                                      |
| `conversation.downloadConversation`                                              | Enable or disable conversation download.                                          |
| `system.position`                                                                | Floating widget position: `bottom-right` or `bottom-left`.                        |
| `system.autoMount`, `system.autoOpen`                                            | Whether the widget appears and opens automatically after configuration loads.     |
| `system.autoWelcome`                                                             | Whether the widget sends its configured welcome event when a conversation starts. |
| `system.isActive`                                                                | Whether the widget is available to visitors.                                      |
| `system.disableInteraction`                                                      | Show a non-interactive preview ([Demo mode](#demo-mode)).                         |
| `system.forcePrivacyConsent`                                                     | Require the privacy-consent screen.                                               |
| `privacy.showPrivacyPolicy`                                                      | Show the project's privacy-consent flow when configured.                          |
| `showPoweredByBotfriends`                                                        | Show or hide the Botfriends attribution.                                          |
| `localization.defaultLanguage`                                                   | Default widget and conversation language.                                         |
| `localization.projectLanguages`                                                  | Languages enabled for the project.                                                |
| `localization.widgetLanguages`                                                   | Languages available in the widget.                                                |
| `localization.currentWidgetLanguage`, `localization.currentConversationLanguage` | Runtime language selections.                                                      |
| `localization.translations`                                                      | Per-language text overrides (see below).                                          |
| `integration.metadata`, `integration.metadataJWT`                                | User data for the bot ([Step 6](#step-6-pass-user-data-to-the-bot)).              |
| `integration.hooks`                                                              | Message hooks ([Step 7](#step-7-intercept-messages-with-hooks)).                  |

A more complete example:

```js
window.BotfriendsWidgetOptions = {
  appearance: {
    useAvatar: true,
    avatarUrl: 'https://cdn.example.com/support-avatar.png',
    useInfoBanner: true,
    brandColor: '#2457a6',
    agentMessageColor: '#f1f4f8',
    userMessageColor: '#2457a6',
  },
  launcher: {
    useIcon: false,
    size: 'medium',
    color: '#2457a6',
  },
  system: {
    position: 'bottom-right',
    autoMount: true,
    autoOpen: false,
  },
  localization: {
    defaultLanguage: 'en',
    projectLanguages: ['en', 'de'],
    widgetLanguages: ['en', 'de'],
    translations: {
      en: {
        'header.title': 'How can we help?',
        'header.subtitle': 'Chat with our team',
      },
      de: {
        'header.title': 'Wie können wir helfen?',
        'header.subtitle': 'Schreiben Sie unserem Team',
      },
    },
  },
}
```

**Texts.** Every text in the widget can be overridden through
`localization.translations`. To see all available text keys and their default
values, call `window.BotfriendsWidgetApi.getDefaultTranslations()` in the browser
console of a page that loads the widget.

**Styling.** Only the settings above are supported. There is no custom CSS API,
and because the widget renders in a Shadow DOM, your page's CSS cannot style the
widget's internals.

### JavaScript API

#### `window.BotfriendsWidgetApi`

Available as soon as the widget script has loaded.

| Member                                     | Description                                                                            |
| ------------------------------------------ | -------------------------------------------------------------------------------------- |
| `init(webchatId, options?, mountOptions?)` | Starts the widget and returns it. Replaces a widget that is already running.           |
| `destroy()`                                | Removes the running widget from the page.                                              |
| `version`                                  | The version of the loaded widget script.                                               |
| `getDefaultTranslations()`                 | Returns the widget's built-in texts for all languages, keyed by language and text key. |

`mountOptions` fields:

| Field          | Description                                                                                |
| -------------- | ------------------------------------------------------------------------------------------ |
| `container`    | An `HTMLElement` to show the chat in ([Step 5](#step-5-embed-the-chat-in-a-page-element)). |
| `showLauncher` | Show the launcher button in embedded mode. Default: `false`.                               |
| `openMenu`     | Show the menu initially. Intended for [demo mode](#demo-mode).                             |

#### `window.BotfriendsWidget`

The running widget. It is set each time the widget starts and is also the
return value of `init(...)`.

| Method                            | Description                                                                      |
| --------------------------------- | -------------------------------------------------------------------------------- |
| `open({ focus? }?)`               | Opens the chat panel. Set `focus: true` to move focus into the panel.            |
| `close()`                         | Closes the chat panel.                                                           |
| `toggle({ focus? }?)`             | Opens or closes the chat panel.                                                  |
| `show()`                          | Makes the widget visible.                                                        |
| `hide()`                          | Hides the widget, including the launcher.                                        |
| `sendMessage(text)`               | Sends a text message as the visitor.                                             |
| `updateConfig(options)`           | Changes settings of the running widget ([options](#configuration-options)).      |
| `setWidgetLanguage(languageCode)` | Changes the language of the widget's texts.                                      |
| `getWidgetLanguage()`             | Returns the current language of the widget's texts.                              |
| `setConversationLanguage(code)`   | Changes the language of the conversation.                                        |
| `getConversationLanguage()`       | Returns the current conversation language.                                       |
| `on(eventName, listener)`         | Subscribes to an [event](#events). Returns a function that removes the listener. |
| `destroy()`                       | Removes this widget from the page.                                               |
| `addMessages(messages)`           | Adds preview messages. [Demo mode](#demo-mode) only.                             |
| `clearMessages()`                 | Removes all preview messages. [Demo mode](#demo-mode) only.                      |

`BotfriendsWidgetApi.destroy()` and `BotfriendsWidget.destroy()` both remove the
running widget. The difference only matters if you keep a reference to an old
widget: its `destroy()` does nothing once that widget has already been removed
or replaced by a new `init(...)` call.

After the widget is removed, `window.BotfriendsWidget` still points to the
removed widget and its methods have no effect. Start a new widget with
`init(...)` before using it again.

#### Events

Subscribe with `window.BotfriendsWidget.on(eventName, listener)`. The listener
receives an event whose `detail` contains the data:

| Event                         | `event.detail`                                      |
| ----------------------------- | --------------------------------------------------- |
| `connection:error`            | `null`                                              |
| `message:sent`                | The submitted chat message.                         |
| `message:received`            | The received chat message.                          |
| `message:typing`              | Typing state information.                           |
| `conversation:new`            | `null` when a new conversation starts.              |
| `conversation:terminated`     | The message that could not be delivered, or `null`. |
| `conversation:changeLanguage` | An object containing `languageCode`.                |

## Privacy, consent, and data storage

### Privacy information and consent prompt

The project configuration controls the widget's privacy screen:

- `privacy.showPrivacyPolicy: true` shows the configured privacy information
  before a visitor starts chatting, unless a recent acceptance is stored in
  this browser for this webchat.
- The displayed content and button labels are configured through the
  localization keys `privacy.title`, `privacy.information`, `privacy.accept`,
  and `privacy.decline`.
- When a visitor accepts, the widget allows the chat to open and stores the
  acceptance locally. When a visitor declines, the widget closes the chat and
  does not store a decline record; the prompt is shown again the next time it
  is opened.
- With `privacy.showPrivacyPolicy: false`, the privacy prompt is not shown.
  This does not clear an acceptance previously stored in the browser.

The acceptance record is stored in the embedding site's browser `localStorage`
under `bfx_widget_privacy_consent:<webchatId>`. It contains the choice
(`accepted`) and the acceptance timestamp. It is scoped to the webchat and is
considered valid for 48 hours; expired or invalid records are removed. If
`localStorage` is unavailable or blocked, the widget can use the acceptance for
the current page session but cannot remember it after reload.

`system.forcePrivacyConsent` is a separate forced-gate setting. When enabled,
the privacy screen remains required and the widget does not start a gateway
connection, even if an acceptance is stored. The accept and decline actions do
not change the state while this setting is enabled. Use
`privacy.showPrivacyPolicy` for the regular accept/decline flow.

This widget behavior displays configured privacy information and remembers a
local acceptance state. It does not create a server-side consent record, provide
an audit trail, or determine whether the configured text or consent flow meets
your legal requirements. Configure the privacy information and obtain any
required legal review for your use case.

### Browser storage

The widget uses the embedding page's `localStorage` for:

| Key                                            | Stored value and purpose                                             | Lifetime                                                                                                                               |
| ---------------------------------------------- | -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `bfx_resume_token`                             | The gateway-issued JWT used to resume the browser's webchat session. | Persists until replaced or removed by browser/site storage management. The token's validity is controlled by its server-issued expiry. |
| `bfx_widget_privacy_consent:<webchatId>`       | The visitor's accepted privacy-screen state and timestamp.           | 48 hours from acceptance.                                                                                                              |
| `bfx_widget_info_banner_dismissed:<webchatId>` | The timestamp when the visitor dismissed the information banner.     | 48 hours from dismissal.                                                                                                               |

The session token is available to JavaScript running on the embedding page's
origin; it is not stored in `sessionStorage` or an `httpOnly` cookie. Protect
the host page against script injection and do not treat browser storage as a
secure secret store. Destroying the widget removes its mounted UI but does not
clear these `localStorage` entries. Unlike the consent and banner keys, the
`bfx_resume_token` key has no webchat ID suffix, so there is one resume-token
slot per embedding origin.

### Conversation and message retention

Conversation messages are retained by the gateway while the conversation is
active. When a conversation is terminated, the gateway applies the retention
period configured for the webchat/project. The default retention period is 30
days. Conversations can be terminated by the configured inactivity timeout
(default eight hours) or by an authoritative conversation-end event. The
inactivity timeout and message retention period are separate settings.

After the retention deadline, stored messages become eligible for deletion;
physical deletion is asynchronous and may occur later. The local browser
consent setting does not control server-side message retention. Contact your
Botfriends X administrator to confirm the retention settings for your project
and follow your organization's process for deletion requests.

## Content security policy and bot-provided URLs

The widget runs in the embedding page's document and renders inside a Shadow DOM.
The Shadow DOM isolates styles, but it is not an iframe, origin boundary, or
security sandbox. The embedding page's Content Security Policy (CSP) therefore
continues to apply to the widget and its resources. Security headers returned by
the chat gateway do not set or verify the CSP configured on your website.

If your website uses a CSP, it must allow the widget and its connections to load.
Usually, this means allowing the script host shown in your Botfriends X embed
snippet, the chat gateway used by your project, and the hosts of images shown in
chat messages. Botfriends X provides the relevant hosts for your deployment; you
do not need to choose them yourself. If something is blocked, your browser's
console will show a CSP error. If your website does not use a CSP, no CSP changes
are needed.

Bot-provided image messages and card artwork are accepted only when their URLs are
absolute `http://` or `https://` URLs. The widget checks image URLs again before
using them; invalid or unsupported image URLs are not displayed.

Card buttons always display their configured title. When clicked:

- If the postback is absent or exactly empty, the title is sent as a chat message.
- A non-empty absolute HTTP(S), `mailto:`, or `tel:` URL opens as a destination.
  A valid bare email address is opened as an email address.
- Any other non-empty value is sent as chat text rather than opened as a URL. The
  conversation bubble displays the button title, not that postback value.
- Quick-reply postbacks are sent as chat data and are not treated as navigation
  destinations.

Destinations opened by the widget use a new browsing context with `noopener` and
`noreferrer`. These behaviors apply to bot-provided content; host applications
should still configure CSP for the origins they intend to permit.

## Mobile applications

The widget is a JavaScript embed for websites, including mobile websites. There
are no native Android or iOS SDKs.
