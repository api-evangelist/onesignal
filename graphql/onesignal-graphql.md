# OneSignal GraphQL Schema

Conceptual GraphQL schema for the OneSignal multi-channel customer engagement platform, derived from the OneSignal REST API v1 (https://documentation.onesignal.com/reference).

OneSignal provides push notifications, in-app messaging, email, SMS, and Live Activities across iOS, Android, web, and other platforms. This schema models the full surface area of the API including notifications, players/devices, segments, subscriptions, apps, templates, journeys, webhooks, outcomes, and analytics.

## Schema Source

REST API reference: https://documentation.onesignal.com/reference
Schema file: onesignal-schema.graphql

## Type Overview

### Notification Types

- **Notification** - A multi-channel notification. Covers push, email, SMS, in-app, and Live Activity sends. Includes delivery counters, scheduling fields (sendAfter, ttl, priority), and outcome linkage.
- **NotificationDetails** - Extended view of a notification including targeting criteria, template reference, and external ID.
- **NotificationType** - Enum of PUSH, IN_APP, EMAIL, SMS, LIVE_ACTIVITY.
- **NotificationContents** - Localized body copy keyed by BCP-47 language tags.
- **NotificationHeadings** - Localized title copy keyed by BCP-47 language tags.
- **NotificationData** - Arbitrary JSON payload sent with a notification.
- **NotificationURL** - Launch URL with optional separate web and app URL variants.
- **NotificationTargeting** - Segment names, player IDs, external user IDs, alias maps, and filter arrays used to select recipients.
- **NotificationList** - Paginated wrapper around a list of Notification records.
- **DeliveryStatus** - Enum: PENDING, QUEUED, SENT, FAILED, ERRORED, CANCELED, COMPLETED.
- **PlatformDeliveryStats** - Per-platform delivered and error counts (iOS, Android, Chrome Web, Firefox, Safari, Email, SMS).

### Error Types

- **NotificationError** - A single code + message validation or delivery error.
- **Errors** - Top-level error envelope matching the REST API error response shape.

### Player / Device Types

- **Player** - A registered subscriber record (legacy Player API). Stores push token, tags, location, session counts, and spend data.
- **PlayerDetails** - Extended player record including purchase history.
- **PlayerType** - Enum of all supported platform types (IOS, ANDROID, CHROME_WEB, FIREFOX, SAFARI, EMAIL, SMS, AMAZON_FIRE, WINDOWS_PHONE, HUAWEI, CHROME_APP).
- **PlayerDevice** - Push token, app version, device vendor ID, ad ID, and root status.
- **PlayerTags** - Arbitrary JSON key-value tags for segmentation.
- **PlayerLanguage** - BCP-47 locale code.
- **PlayerLatLong** - GPS latitude and longitude.
- **PlayerIpAddress** - IPv4/IPv6 capture at registration.
- **PlayerDeviceModel** - Hardware model string (e.g. "iPhone14,3").
- **PlayerDeviceOS** - OS version string.
- **PlayerLocation** - Full location object including accuracy and background flag.
- **PlayerActiveSince** - First session timestamp.
- **PlayerList** - Paginated wrapper around a list of Player records.
- **Purchase** - An in-app purchase event with SKU, currency ISO code, and amount.

### Segment Types

- **Segment** - A dynamic or saved subscriber segment used for targeting.
- **SegmentDetails** - Extended segment view including filter tree and player count.
- **SegmentFilter** - A single filter clause with field, relation, value, and optional time window.
- **FilterField** - The attribute being evaluated (e.g. LANGUAGE, TAG, LAST_SESSION).
- **FilterValue** - Polymorphic comparison value (string, number, or boolean).
- **FilterOperator** - Enum of EQUALS, NOT_EQUALS, LESS_THAN, GREATER_THAN, EXISTS, NOT_EXISTS, CONTAINS, NOT_CONTAINS, TIME_ELAPSED_GT, TIME_ELAPSED_LT.
- **SegmentFilterField** - Enum of all filterable fields (AMOUNT_SPENT, APP_VERSION, COUNTRY, EMAIL, FIRST_SESSION, LANGUAGE, LAST_SESSION, LOCATION, SESSION_COUNT, SESSION_TIME, TAG, BOUGHT_SKU).
- **SegmentList** - Wrapper around a list of Segment records.

### Subscription Types

- **Subscription** - A OneSignal User API subscription (push token, email address, or phone number).
- **SubscriptionDetails** - Extended subscription record including device info and SDK version.
- **SubscriptionType** - Enum: PUSH, EMAIL, SMS.
- **SubscriptionToken** - The raw token, email, or phone number value.
- **SubscriptionList** - Paginated wrapper around subscriptions.

### App / Configuration Types

- **App** - A OneSignal app / project record including player count and platform keys.
- **AppDetails** - Full app detail including all platform configs.
- **AppPlatforms** - Boolean flags per platform (iOS, Android, Chrome Web, Firefox, Safari, Email, SMS, Huawei).
- **AppPlatformType** - Enum used when creating or updating apps.
- **APNsConfig** - Apple Push Notification service credentials: p12/p8 cert, key ID, team ID, bundle ID.
- **FCMConfig** - Firebase Cloud Messaging server key, sender ID, project ID, and app ID.
- **WebPushConfig** - VAPID public/private keys, origin, GCM sender ID, and subdomain.
- **EmailConfig** - Sender address, name, and reply-to for email channel.
- **SMSConfig** - Provider, from-number, account SID, and auth token for SMS.
- **ChromeConfig** - Chrome App extension API key.
- **SafariConfig** - Safari push certificate and website push ID.

### Outcome / Analytics Types

- **OutcomeResult** - Aggregated outcome value for a named outcome across a reporting window.
- **NotificationOutcome** - Outcome metrics scoped to a single notification send.
- **Outcome** - A single named outcome event with value, session type, and timestamp.
- **Influence** - Attribution record linking a session to an influencing notification (DIRECT, INDIRECT, UNATTRIBUTED).
- **OutcomeSessionType** / **InfluenceType** - Enums for attribution model.

### Session Types

- **Session** - A user session event with session count, playtime, and influence records.
- **SessionDetails** - Extended session with IP address, country, device OS, and SDK version.

### Email Message Types

- **Email** - An email notification send with subject, body, from details, and delivery status.
- **EmailDetails** - Extended email record including open, click, unsubscribe, and bounce counts.

### SMS Types

- **SMSNotification** - An SMS send with localized content, from-number, and delivery status.
- **SMSDetails** - Extended SMS record including delivery breakdown counters.

### Template Types

- **Template** - A reusable notification template (push, email, SMS, or in-app).
- **TemplateDetails** - Extended template with version number.
- **TemplateType** - Enum: PUSH, EMAIL, SMS, IN_APP.

### External User / Alias Types

- **ExternalId** - Mapping of an external ID to OneSignal subscription and player IDs.
- **ExternalUser** - A User API user record with aliases, subscriptions, and dynamic properties.
- **UserProperties** - Tags, language, timezone, location, spend, purchases, and session counts for a user.

### Journey Types

- **Journey** - A multi-step automated messaging journey with status tracking.
- **JourneyDetails** - Extended journey including step configuration, entry/exit filters, and conversion event.
- **JourneyStatus** - Enum: DRAFT, RUNNING, PAUSED, STOPPED, COMPLETED.

### API Key / Token Types

- **APIKey** - An app or org-level API key with label, expiry, and app scope.
- **Token** - An authentication token with type and expiry.

### Webhook Types

- **Webhook** - A registered webhook endpoint subscribed to one or more event types.
- **WebhookEvent** - A single incoming webhook event payload.
- **WebhookEventType** - Enum: NOTIFICATION_CLICKED, NOTIFICATION_DISMISSED, NOTIFICATION_WILL_DISPLAY, NOTIFICATION_PERMISSION_CHANGED, SUBSCRIPTION_CHANGED, IN_APP_MESSAGE_CLICKED.

### Pagination / List Wrappers

- **NotificationList** - total, offset, limit, notifications array.
- **PlayerList** - total, offset, limit, players array.
- **SegmentList** - total, segments array.
- **SubscriptionList** - total, offset, subscriptions array.

## Root Operations

### Query

| Field | Description |
|---|---|
| `notification(appId, id)` | Fetch a single notification |
| `notifications(appId, limit, offset, kind)` | Paginated notification list |
| `player(appId, id)` | Fetch a single player |
| `players(appId, limit, offset)` | Paginated player list |
| `segment(appId, id)` | Fetch a single segment |
| `segments(appId)` | List segments |
| `subscription(appId, subscriptionId)` | Fetch a subscription |
| `subscriptionsByUser(appId, externalId)` | Subscriptions for an external user |
| `app(id)` | Fetch a single app |
| `apps` | List all org apps |
| `email(appId, id)` | Fetch an email message |
| `smsNotification(appId, id)` | Fetch an SMS message |
| `template(appId, id)` | Fetch a template |
| `templates(appId)` | List templates |
| `user(appId, externalId)` | Fetch an external user |
| `outcomeResults(...)` | Fetch outcome aggregations |
| `webhooks(appId)` | List webhooks |
| `webhook(appId, id)` | Fetch a webhook |
| `journey(appId, id)` | Fetch a journey |
| `journeys(appId)` | List journeys |

### Mutation

| Field | Description |
|---|---|
| `sendNotification(input)` | Send a push/email/SMS/in-app notification |
| `cancelNotification(appId, id)` | Cancel a pending notification |
| `createPlayer(input)` | Register a new device |
| `updatePlayer(appId, id, input)` | Update device metadata or tags |
| `deletePlayer(appId, id)` | Remove a device |
| `createSegment(appId, input)` | Create a segment |
| `deleteSegment(appId, id)` | Delete a segment |
| `upsertUser(appId, externalId, ...)` | Create or update a User API user |
| `deleteUser(appId, externalId)` | Delete a user |
| `createApp(name, platforms)` | Create an app |
| `updateApp(id, input)` | Update app configuration |
| `createWebhook(input)` | Register a webhook |
| `updateWebhook(appId, id, input)` | Update a webhook |
| `deleteWebhook(appId, id)` | Remove a webhook |
| `createTemplate(input)` | Create a template |
| `updateTemplate(appId, id, input)` | Update a template |
| `deleteTemplate(appId, id)` | Delete a template |
| `exportPlayers(appId, ...)` | Trigger CSV export, returns job ID |

### Subscription (WebSocket)

| Field | Description |
|---|---|
| `notificationStatus(appId, notificationId)` | Stream delivery status changes |
| `webhookEvents(appId, events)` | Stream incoming webhook events |

## Input Types

- **SendNotificationInput** - Full send payload covering all targeting options and scheduling.
- **SegmentFilterInput** - Filter clause for segment creation.
- **CreateSegmentInput** - Name and filters for a new segment.
- **UpdatePlayerInput** - Fields that can be patched on a player record.
- **CreatePlayerInput** - Required and optional fields for device registration.
- **CreateWebhookInput** - URL and event list for webhook registration.
- **UpdateWebhookInput** - Partial update for an existing webhook.
- **CreateTemplateInput** - Fields for creating or replacing a template.
- **UpdateAppInput** - Patchable app-level configuration fields.
- **NotificationContentsInput** - Localized content map for send payloads.

## Named Types Count

This schema defines **70 named types** (types, enums, inputs, scalars, and root operation types).
