# Botfriends Agent Chat Widget

The Agent Chat Widget is a JavaScript widget for embedding Botfriends X chat in a
website. It is delivered as a browser script and exposes a JavaScript API for
initialization, configuration, and runtime control.

The widget renders in a Shadow DOM to isolate its styles from the host page. The
Shadow DOM is a styling boundary, not a security sandbox.

## Add the copy script to a website

Copy the embed snippet from the Botfriends X project configuration and add it to
the page where the chat should appear. The script URL is environment-specific; use
the exact URL supplied by Botfriends X.

```html
<script
        defer
        src="https://webchat.botfriendsx.com/scripts/v1/widget.js"
        data-webchat-id="YOUR_WEBCHAT_ID"
        data-auto-init="true"
></script>
```

The snippet:

- Loads the widget bundle from the configured widget host.
- Identifies the Botfriends X webchat with `data-webchat-id`.
- Enables automatic initialization when `data-auto-init` is the string `true`.
- Lets the browser download the deferred script without blocking HTML parsing.

The bundle registers `window.BotfriendsWidgetApi`; with auto-initialization enabled,
it also mounts the widget and exposes the mounted handle as
`window.BotfriendsWidget`. Add the snippet once per page. Do not copy a development
or staging script URL into production.

To pass JavaScript options with the copy script, define them before the widget
bundle runs:

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

`BotfriendsWidgetOptions` applies only when the script auto-initializes. If you
initialize through the JavaScript API, pass the options to `init` instead.

## Initialize through JavaScript

Use the API when the host page needs to control when or where the widget mounts:

```html
<script src="https://webchat.botfriendsx.com/scripts/v1/widget.js" defer></script>
<script>
  window.addEventListener('DOMContentLoaded', () => {
    const webchatId = 'YOUR_WEBCHAT_ID'

    const widget = window.BotfriendsWidgetApi.init(webchatId, {
      appearance: {
        brandColor: '#2457a6',
      },
    })

    widget.open({ focus: true })
  })
</script>
```

`init(webchatId, options?, mountOptions?)` returns a handle for the mounted widget.
The `webchatId` is required. `options` is a partial widget configuration;
`mountOptions` controls where and how the widget is mounted.

The widget manages its own gateway session authentication. See the JWT section
below for the distinction between its session token and the optional signed
metadata token.

### Passing user data: JWT and metadata

#### JWT

The widget obtains and refreshes its own gateway session JWT. This session token
authorizes the widget's connection; it is separate from the optional
`integration.metadataJWT` described here. The widget API does not accept a
host-supplied identity JWT to log a user in or resume another user session.

`integration.metadataJWT` is an optional signed assertion accompanying an
outgoing message. The gateway verifies it with the signing secret configured for
the webchat, using the `HS256` algorithm. The JWT payload must contain:

- `projectId`: the ID of the Botfriends X project that owns the webchat.
- `metadata`: a JSON object containing the verified metadata to pass to the bot.

The `projectId` must match the authenticated project. The metadata object is
limited to 16 KiB of UTF-8 JSON and eight nested object/array levels. Standard
JWT `exp` and `nbf` claims are checked when present. The gateway does not require
an expiration claim or impose a maximum token age; use a short expiration for
tokens created by your application.

Create the JWT on your server using the signing secret provided for the webchat
through your Botfriends X deployment's secure setup. If your integration has not
been given a signing secret, request it through your usual secure project
provisioning channel. Never expose the signing secret in browser JavaScript, the
embed snippet, or a public repository. Only the resulting signed token should be
sent to the browser. For example, server-side code using the `jsonwebtoken`
package can sign a short-lived token like this:

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

Pass the resulting token to the widget as `integration.metadataJWT`:

```js
const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {
  integration: {
    metadataJWT,
  },
})
```

For per-message or rotating tokens, `beforeSend` can supply or replace the token
by returning `metadataJWT` at the top level of its result, alongside `message`
and `send`—not inside `message`. Return `null` to omit the token for that
message, or leave the field undefined to use the configured
`integration.metadataJWT`.

The token accompanies each outgoing message separately from its message payload.
The gateway verifies it and forwards only the verified `metadata` claim to the
bot backend. The token itself is not added to the message, stored with the
conversation, or forwarded to the bot backend.

If the token is invalid, expired, signed with the wrong secret, or has a
`projectId` that does not match the webchat's project, no verified metadata is
forwarded. The message itself is still processed without that verified metadata.

Do not confuse this metadata assertion with user authentication: it does not
identify or log in the website visitor. JWT contents and any transmitted
metadata should still be treated as visible to the browser and the receiving
conversation.

#### Metadata

Pass optional JSON-compatible metadata under `integration.metadata` when
initializing the widget:

```js
const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', {
  integration: {
    metadata: {
      customerId: '123',
    },
  },
})
```

The same integration configuration can be supplied or changed with
`widget.updateConfig(...)`. The widget also adds the current full page URL,
including its query string and fragment, to outgoing messages. The complete
metadata object is limited to 16 KiB of UTF-8 JSON and eight nested object/array
levels.

Metadata and the page URL are unsigned and visible to the receiving conversation.
Do not include secrets, access tokens, or other sensitive values. Invalid
metadata or integration settings are ignored with a console warning instead of
blocking a message; an invalid configuration update keeps the previous valid
settings. If custom values and the page URL exceed the size limit together, the
custom values are omitted for that message. If the URL alone exceeds the limit,
the metadata field is omitted.

Use this method for non-sensitive values that do not need cryptographic
verification. For signed, server-verified metadata, use `integration.metadataJWT`
instead. The two methods serve different purposes and can be supplied together.

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

## Configuration and precedence

The script accepts partial values from `BotfriendsWidgetOptions` or the `options`
argument to `BotfriendsWidgetApi.init`. These values override the project
configuration for the mounted widget. A runtime `widget.updateConfig(...)` call
uses the same override layer.

Configuration precedence, from highest to lowest, is:

1. JavaScript options provided by the host page or `updateConfig(...)`.
2. The Botfriends X project configuration.
3. The widget's default values.

Configuration is merged by property, so overriding one nested field does not
replace its sibling fields. Invalid or unsupported fields are discarded and
reported in the browser console.

The supported configuration fields are:

| Field                                                                            | Supported values                                                                  |
| -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `name`, `description`                                                            | Configuration name and description strings.                                       |
| `lastUpdated`                                                                    | Configuration update timestamp as a non-negative integer.                         |
| `appearance.useAvatar`, `appearance.avatarUrl`                                   | Whether to show an avatar and its image URL.                                      |
| `appearance.useInfoBanner`                                                       | Whether to show the information banner.                                           |
| `appearance.brandColor`                                                          | Main brand color as a 3- or 6-digit hex color, for example `#2457a6`.             |
| `appearance.agentMessageColor`, `appearance.userMessageColor`                    | Agent and user message colors as 3- or 6-digit hex colors.                        |
| `launcher.useIcon`, `launcher.iconUrl`                                           | Whether to use a custom launcher icon and its URL.                                |
| `launcher.size`                                                                  | `small`, `medium`, `large`, or `number` ( custom px value )                       |
| `launcher.color`                                                                 | Launcher color.                                                                   |
| `conversation.fileUpload`                                                        | Enable or disable file upload.                                                    |
| `conversation.languageSelection`                                                 | Enable or disable conversation language selection.                                |
| `conversation.restartConversation`                                               | Enable or disable restarting a conversation.                                      |
| `conversation.downloadConversation`                                              | Enable or disable conversation download.                                          |
| `system.position`                                                                | Floating widget position: `bottom-right` or `bottom-left`.                        |
| `system.autoMount`, `system.autoOpen`                                            | Whether the widget appears and opens automatically after configuration loads.     |
| `system.autoWelcome`                                                             | Whether the widget sends its configured welcome event when a conversation starts. |
| `system.isActive`                                                                | Whether the widget is available to visitors.                                      |
| `system.disableInteraction`                                                      | Render a non-interactive demo preview instead of starting a chat session.         |
| `system.forcePrivacyConsent`                                                     | Require the privacy-consent screen.                                               |
| `privacy.showPrivacyPolicy`                                                      | Show the project's privacy-consent flow when configured.                          |
| `showPoweredByBotfriends`                                                        | Show or hide the Botfriends attribution.                                          |
| `localization.defaultLanguage`                                                   | Default widget and conversation language.                                         |
| `localization.projectLanguages`                                                  | Languages enabled for the project.                                                |
| `localization.widgetLanguages`                                                   | Languages available in the widget.                                                |
| `localization.currentWidgetLanguage`, `localization.currentConversationLanguage` | Runtime language selections.                                                      |
| `localization.translations`                                                      | Per-language translation overrides.                                               |

For example:

```js
const widgetOptions = {
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

const widget = window.BotfriendsWidgetApi.init('YOUR_WEBCHAT_ID', widgetOptions)
```

The configurable translation keys are all from `getDefaultTranslations()`.
`getDefaultTranslations()` returns the widget's built-in translation catalogs.

Supported settings are limited to the configuration fields above. The widget does
not provide a public custom CSS or arbitrary DOM styling API. Its Shadow DOM
prevents host-page CSS from styling widget internals.

## Embedded mode and fullscreen layouts

Pass an existing HTML element as `mountOptions.container` to embed the widget
inside that element instead of mounting it as a floating widget on `document.body`.
The container must be an `HTMLElement`. Embedded mode fills the container's width
and height, so set its dimensions in the host page.

```html
<div id="support-chat" class="support-chat"></div>
<script src="https://webchat.botfriendsx.com/scripts/v1/widget.js" defer></script>
<script>
  window.addEventListener('DOMContentLoaded', () => {
    const container = document.getElementById('support-chat')

    const widget = window.BotfriendsWidgetApi.init(
      'YOUR_WEBCHAT_ID',
      {},
      {
        container,
        showLauncher: false,
      },
    )

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

For a fullscreen embedded chat, size and position the host container to fill the
viewport. Fullscreen is controlled by the host page's container CSS; there is no
separate fullscreen configuration flag.

```css
.support-chat {
  position: fixed;
  inset: 0;
  z-index: 1000;
  width: 100vw;
  height: 100dvh;
}
```

In embedded mode, the launcher is hidden by default. Set `showLauncher: true` in
`mountOptions` to display it. `openMenu` is another optional mount option for
showing the menu initially in demo mode.

## JavaScript API

The bundle exposes `window.BotfriendsWidgetApi`:

| API                                        | Description                                               |
| ------------------------------------------ | --------------------------------------------------------- |
| `init(webchatId, options?, mountOptions?)` | Mounts the widget and returns a `BotfriendsWidgetHandle`. |
| `destroy()`                                | Destroys the currently mounted widget.                    |
| `version`                                  | The loaded widget bundle's version string.                |
| `getDefaultTranslations()`                 | Returns the default translation catalogs.                 |

The returned handle is also available as `window.BotfriendsWidget` after mounting:

| Handle method             | Description                                                               |
| ------------------------- | ------------------------------------------------------------------------- |
| `open({ focus? }?)`       | Opens the chat panel. Set `focus: true` to move focus into the panel.     |
| `close()`                 | Closes the chat panel.                                                    |
| `toggle({ focus? }?)`     | Opens or closes the chat panel.                                           |
| `show()`                  | Makes the widget visible.                                                 |
| `hide()`                  | Hides the widget.                                                         |
| `sendMessage(message)`    | Submits a text message through the widget.                                |
| `updateConfig(config)`    | Applies partial JavaScript configuration overrides to the mounted widget. |
| `destroy()`               | Unmounts the widget and removes its host element.                         |
| `on(eventName, listener)` | Subscribes to a supported widget event. Returns an unsubscribe function.  |
| `addMessages(messages)`   | Adds preview messages in demo mode only.                                  |
| `clearMessages()`         | Clears preview messages in demo mode only.                                |

`show()`/`hide()` control widget visibility. `open()`/`close()` control the
conversation panel. For example:

```js
const widget = window.BotfriendsWidget

widget.open({ focus: true })
widget.sendMessage('I need help with my order.')
widget.close()

// Remove an event listener when it is no longer needed.
const unsubscribe = widget.on('message:received', event => {
  console.log('Received chat message:', event.detail)
})
unsubscribe()
```

### Available events

`on` supports these widget events:

| Event                         | `event.detail`                                      |
| ----------------------------- | --------------------------------------------------- |
| `connection:error`            | `null`                                              |
| `message:sent`                | The submitted chat message.                         |
| `message:received`            | The received chat message.                          |
| `message:typing`              | Typing state information.                           |
| `conversation:new`            | `null` when a new conversation starts.              |
| `conversation:terminated`     | The message that could not be delivered, or `null`. |
| `conversation:changeLanguage` | An object containing `languageCode`.                |

These are notifications, not message-interception hooks. The integration hooks
below allow code to run before a message is displayed or sent.

## Hooks

Register hooks under `integration.hooks`. Each hook definition has a `handler`
function, which may return synchronously or return a Promise, and an optional
`timeoutMs`.

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
          const tokenForThisMessage = getFreshMetadataJWT()

          return {
            message: {
              ...message,
              metadata: {
                ...message.metadata,
                custom: { source: 'website' },
              },
            },
            metadataJWT: tokenForThisMessage,
          }
        },
        timeoutMs: 3000,
      },
    },
  },
})
```

The widget renders inside a Shadow DOM. `beforeDisplay` receives the message
element as `context.element`, which is the simplest way to style it. If you need
to find an element yourself, query the widget's `shadowRoot` as shown below;
`document.querySelector(...)` cannot select elements inside that root.

```js
const root = document.querySelector('[data-botfriends-widget="root"]')?.shadowRoot
const element = root?.querySelector(`[data-message-id="${CSS.escape(message.id)}"]`)
```

### `beforeDisplay`

`beforeDisplay` runs for each user, agent, and system message before it becomes
visible. This includes historical messages and carousel cards as they arrive. It
runs each time the message is rendered, such as when the chat is closed and
reopened. The hook receives a context object with the rendered message element
as `context.element`; it can also be found by its `data-message-id` attribute
within the widget's shadow root.

Return `false` to suppress the message; return `true` or `undefined` to display
it. For example, the registration snippet above adds a host-specific class to
the message element. Because rendering can happen again, apply styling each time
the hook runs.

### `beforeSend`

`beforeSend` runs once for each outgoing message before its first transport
attempt. It may return `{ message?, send? }`; returning `undefined` sends the
original message. Set `send: false` to cancel sending, remove the optimistic
message from the conversation, and restore its text to the composer.

The result may contain `message`, `metadataJWT`, and `send` as top-level fields.
The final message remains a user message and keeps its original `id` and
idempotency key. Only `payload`, `metadata`, `displayText`, and `languageCode`
are read from the returned `message`; omitted fields keep their original values
and other keys are dropped. The top-level `metadataJWT` is not part of the
message payload: a string overrides the configured token for this message,
`null` omits it, and `undefined` keeps the configured token. User payloads must
be valid text, event, or file payloads. Invalid or oversized metadata, or an
invalid payload, is ignored with a warning and the original message is sent.
Retries reuse the finalized message and selected token without running the hook
again.

The example in the hook registration snippet adds custom metadata to an outgoing
message.

### Timeouts and errors

The default hook timeout is three seconds and the maximum is 30 seconds. A
thrown error, rejected Promise, or timeout fails open: `beforeDisplay` allows
the message to display, and `beforeSend` sends the original message. A timeout
cannot interrupt a synchronously running handler; it only bounds asynchronous
waiting.

### Updating hooks

`updateConfig(...)` replaces individual hook definitions. Set a hook to `null`
to remove it; hooks omitted from the update remain registered. Unknown hook
names and definitions without a `handler` function are ignored with a console
warning. A `beforeDisplay` update affects messages rendered after the update,
not messages already rendered.

### Other hooks

The currently supported integration hooks are `beforeDisplay` and `beforeSend`,
described above. No other integration hooks are exposed by this widget API.

### Demo mode

Set `system.disableInteraction: true` when initializing to render a preview without
authentication, a WebSocket connection, or a real conversation. The mode cannot be
changed with `updateConfig`; destroy and remount the widget to change it.

In demo mode, `addMessages(messages)` and `clearMessages()` manage preview content.
Messages require a `role` and a compatible `payload`; optional `id` and `createdAt`
values are generated locally when omitted. Preview content is visual only and is
not sent to the gateway.

## Mobile applications

The product provides a JavaScript embed for websites, including mobile websites.
It does not provide native Android or iOS SDKs.

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
