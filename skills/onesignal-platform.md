---
name: Onesignal
description: Use when building multi-channel messaging campaigns, setting up push notifications, email, SMS, or in-app messages, creating automated customer journeys, managing user segments and targeting, or integrating messaging APIs into applications. Agents should reach for this skill when implementing notification systems, personalizing messages, tracking user engagement, or orchestrating lifecycle marketing workflows.
metadata:
    mintlify-proj: onesignal
    version: "1.0"
---

# OneSignal Skill

## Product summary

OneSignal is a customer engagement platform for sending push notifications, email, SMS, in-app messages, and Live Activities across web, mobile, and desktop. Use the REST API (`https://api.onesignal.com`) to send messages programmatically, manage users and subscriptions, create segments, and track analytics. Key files and concepts: **App ID** and **REST API Key** (found in Settings > Keys & IDs), **Users** (identified by External ID), **Subscriptions** (devices, emails, phone numbers), **Segments** (dynamic audience groups), and **Journeys** (automated multi-step workflows). Primary documentation: https://documentation.onesignal.com

---

## When to use

Reach for this skill when:
- **Sending messages programmatically** — Use the Create Message API to send push, email, SMS, or Live Activities to users or segments.
- **Building automated workflows** — Create Journeys to trigger multi-channel messages based on user behavior, time delays, or profile attributes (onboarding, abandoned carts, re-engagement).
- **Managing user data** — Set External IDs to unify users across devices and channels, add tags for personalization, track custom events.
- **Targeting audiences** — Build segments with filters on tags, behavior, location, or message interactions; use filters inline in API requests.
- **Integrating SDKs** — Set up mobile (iOS, Android, React Native, Flutter) or web (JavaScript, React, Vue, Angular) SDKs to collect subscriptions.
- **Tracking engagement** — Export analytics, view message performance, measure conversions, or stream events to data warehouses.
- **Personalizing content** — Use Liquid syntax to inject user tags, custom data, or dynamic content into messages.

---

## Quick reference

### Essential API endpoints

| Task | Endpoint | Method |
|------|----------|--------|
| Send a message | `/notifications` | POST |
| Create a user | `/users` | POST |
| Update a user | `/users/{id}` | PATCH |
| Create a segment | `/segments` | POST |
| View a message | `/notifications/{id}` | GET |
| Export user data | `/csv_exports` | POST |
| Create a template | `/templates` | POST |
| Create custom event | `/events` | POST |

### Authentication

All API requests require:
- **Header:** `Authorization: Key YOUR_REST_API_KEY`
- **Header:** `Content-Type: application/json`
- **Body:** `"app_id": "YOUR_APP_ID"`

### Key identifiers

| Identifier | Purpose | Where to find |
|-----------|---------|---------------|
| **App ID** | Identifies your OneSignal app | Dashboard > Settings > Keys & IDs |
| **REST API Key** | Authenticates API requests | Dashboard > Settings > Keys & IDs |
| **External ID** | Your user ID (e.g., user_123) | Set via SDK or API; unifies subscriptions |
| **OneSignal ID** | OneSignal's internal user UUID | Auto-generated; returned in API responses |
| **Subscription ID** | Unique ID for a device/email/phone | Returned when subscription is created |

### Message targeting methods (choose one per request)

| Method | Use case | Example |
|--------|----------|---------|
| **Segments** | Predefined audience groups | `"included_segments": ["Active Users"]` |
| **Filters** | Ad-hoc rules with AND/OR logic | `"filters": [{"field": "tag", "key": "level", "relation": "=", "value": "10"}]` |
| **Aliases** | Specific users by ID/email/phone | `"include_aliases": {"external_id": ["user1", "user2"]}` |
| **Subscription IDs** | Target specific devices | `"include_subscription_ids": ["sub-uuid"]` |

### Liquid syntax for personalization

```liquid
Hi {{ first_name | default: 'there' }}
You have {{ points | default: 0 }} points.
{% if premium == "true" %}Premium member{% endif %}
```

---

## Decision guidance

### When to use Journeys vs. single-message campaigns

| Scenario | Use Journeys | Use single message |
|----------|-------------|-------------------|
| Multi-step automation (onboarding, abandoned cart) | ✓ | |
| Triggered by user behavior or time | ✓ | |
| One-off announcement or promotion | | ✓ |
| Branching logic (if/then paths) | ✓ | |
| Scheduled broadcast to a segment | | ✓ |
| Requires wait steps or delays | ✓ | |

### When to use tags vs. custom events

| Aspect | Tags | Custom Events |
|--------|------|---------------|
| **Data type** | Key-value pairs (strings/numbers) | JSON objects with properties |
| **Lifetime** | Stored indefinitely | 30+ days (lifetime storage available) |
| **Use case** | User properties (level, status, location) | User actions (purchase, level_complete) |
| **Segmentation** | Yes | Yes (coming soon) |
| **Journey triggers** | No | Yes (via Wait Until) |
| **Personalization** | Yes | Yes |

### When to use API vs. dashboard

| Task | API | Dashboard |
|------|-----|-----------|
| Send to specific users programmatically | ✓ | |
| Build and test campaigns visually | | ✓ |
| Create Journeys with branching | | ✓ |
| Transactional messages (OTPs, receipts) | ✓ | |
| Bulk user imports | ✓ | ✓ |
| View analytics and reports | | ✓ |
| Manage templates | ✓ | ✓ |

---

## Workflow

### Typical task: Send a personalized push notification to a segment

1. **Identify your audience**
   - Decide: segment, filters, or specific users?
   - If using a segment, verify it exists in the dashboard or create it via API.
   - If using filters, construct the filter logic (e.g., tag-based, location-based).

2. **Prepare your message content**
   - Decide on channel: push, email, SMS, or in-app.
   - Write the message body and title.
   - Add Liquid placeholders for personalization (e.g., `{{ first_name }}`).
   - Optionally create a template in the dashboard for reuse.

3. **Construct the API request**
   - Choose targeting method (segments, filters, or aliases).
   - Set `app_id`, `contents` (or `email_subject`/`email_body` for email).
   - Add optional fields: `headings`, `buttons`, `data`, `url`, `send_after`.
   - Include `Authorization: Key YOUR_REST_API_KEY` header.

4. **Send and verify**
   - POST to `https://api.onesignal.com/notifications`.
   - Check response: `200` with `id` = success; `200` without `id` = no valid subscriptions.
   - Save the message `id` for tracking.

5. **Monitor performance**
   - View message stats in the dashboard or via GET `/notifications/{id}`.
   - Check delivery, open rate, click-through rate.
   - Refine targeting or content for next send.

### Typical task: Set up a user and assign an External ID

1. **Create or identify the user**
   - Collect user data: email, phone, device token (from SDK).
   - Assign a stable External ID from your system (user_123, user@example.com).

2. **Call OneSignal.login (SDK) or Create User API**
   - **SDK:** `OneSignal.login(external_id)` — unifies all subscriptions under one user.
   - **API:** POST `/users` with `identity: { external_id: "user_123" }`.

3. **Add subscriptions (if not auto-collected by SDK)**
   - **Mobile/Web SDK:** Automatically collects push subscriptions.
   - **Email/SMS:** Call `OneSignal.User.addEmail()` or `addSms()` (SDK) or POST `/subscriptions` (API).

4. **Add tags for personalization and segmentation**
   - Call `OneSignal.User.addTag("level", "10")` (SDK) or PATCH `/users/{id}` (API).
   - Use tags in segments and Liquid templates.

5. **Verify in dashboard**
   - Go to Audience > Users, search by External ID.
   - Confirm subscriptions are listed and tags are applied.

### Typical task: Create and launch a Journey

1. **Define the entry trigger**
   - Choose: custom event, segment membership, or manual entry.
   - If custom event, ensure it's tracked in your app/website.

2. **Design the flow**
   - Add message steps (push, email, SMS, in-app).
   - Add action steps: wait delays, branching (if/then), split paths.
   - Use Liquid for personalization.

3. **Configure settings**
   - Set re-entry rules (allow users to re-enter after X days?).
   - Set exit conditions (unsubscribe, segment exit).
   - Choose scheduling (immediate, scheduled, recurring).

4. **Test before launch**
   - Add yourself as a test user.
   - Send a test Journey to verify message rendering and flow.

5. **Launch and monitor**
   - Publish the Journey.
   - Monitor analytics: completion rate, drop-offs, conversions.
   - Pause or edit if needed.

---

## Common gotchas

- **Anonymous users without External ID:** Each subscription (email, device, SMS) is treated as a separate user. This causes duplicate messages and broken attribution. Always set an External ID early.

- **Mixing targeting methods:** You cannot combine `include_aliases`, `included_segments`, and `filters` in one request. Choose one per message.

- **Calling addEmail/addSms before login:** Email and SMS subscriptions added before `OneSignal.login` are tied to the anonymous user. Call `login` first, then add email/SMS.

- **Restricted External IDs:** OneSignal rejects placeholder values like `"null"`, `"NA"`, `"undefined"`, `"0"`, `"-1"`. Use stable identifiers from your auth system.

- **Liquid syntax in templates:** Curly braces must be escaped in JSON. Use `{{ field_name }}` in templates; OneSignal renders at send time. Invalid syntax renders as empty string but message still sends.

- **Rate limits:** API has rate limits (see `/reference/rate-limits`). Use `idempotency_key` for retries to avoid duplicate messages.

- **Subscription status vs. opt-out:** A subscription can be "opted out" but still count toward MAU billing. Unsubscribe only removes the subscription entirely.

- **Segment filters are slow:** Negation filters (`!=`, `not_exists`) on tags are slower, especially for users with many tags. Use positive filters when possible.

- **SDK initialization timing:** Call `OneSignal.login` and `addEmail`/`addSms` early in the app lifecycle, before sending messages. Late calls break attribution.

- **Email/SMS setup in dashboard only:** Email and SMS channels cannot be configured via API; use the dashboard to set up DNS, senders, and registration.

---

## Verification checklist

Before submitting work with OneSignal:

- [ ] **External ID is set** — User has a stable External ID from your system (not anonymous).
- [ ] **Subscriptions are active** — User has at least one subscription for the target channel (push, email, SMS).
- [ ] **Targeting is correct** — Message targets the right segment, filter, or user list (not all users by accident).
- [ ] **Personalization renders** — Liquid syntax is valid and tag keys exist (test with a test user).
- [ ] **Message content is complete** — Title, body, and CTA are present; no placeholder text remains.
- [ ] **Scheduling is correct** — Send time is in the future if scheduled; timezone is correct if using `delayed_option`.
- [ ] **API response is valid** — Request returned `200` with a message `id` (not `200` without `id`).
- [ ] **Test send succeeds** — Message appears on test device/email with correct rendering and links.
- [ ] **Analytics are tracked** — Message shows in dashboard with delivery, open, and click metrics.
- [ ] **No duplicate messages** — User received message once, not multiple times (check for External ID conflicts).

---

## Resources

**Comprehensive navigation:** https://documentation.onesignal.com/llms.txt

**Critical documentation pages:**
- [REST API Overview](https://documentation.onesignal.com/reference/rest-api-overview) — Core API concepts, authentication, rate limits.
- [Create Message API](https://documentation.onesignal.com/reference/create-message) — Send push, email, SMS with targeting and personalization.
- [Users and External IDs](https://documentation.onesignal.com/docs/en/users) — User model, subscription unification, lifecycle.
- [Journeys Overview](https://documentation.onesignal.com/docs/en/journeys-overview) — Automated multi-channel workflows.
- [Segmentation](https://documentation.onesignal.com/docs/en/segmentation) — Build dynamic audience groups.
- [Message Personalization](https://documentation.onesignal.com/docs/en/message-personalization) — Liquid syntax, tags, dynamic content.

---

> For additional documentation and navigation, see: https://documentation.onesignal.com/llms.txt