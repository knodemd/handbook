# 8 - Service registrations

The live connectors of [decision 0013](decisions/0013-live-connectors.md) sign in to Google,
Slack and Telegram as Knode. Each service needs Knode registered once, by an owner, with an
account the project controls: [knode#302](https://github.com/knodemd/knode/issues/302) (Google),
[knode#303](https://github.com/knodemd/knode/issues/303) (Slack) and
[knode#301](https://github.com/knodemd/knode/issues/301) (Telegram). This chapter is the
click-by-click for each one, the texts to paste, and where the values that come back go.

The values never go in an issue, a pull request, a chat or a log
([chapter 4](04-building-in-public.md)). They go in GitHub Actions secrets for release builds and
in a local ignored file for developer builds (see [Where the values go](#where-the-values-go)).

Consoles change their menus often. The steps below were written in November 2026; if a label
moved, the field it names is still the one to fill.

## Before you start

- [ ] **The privacy policy and terms are live** at `https://knode.md/privacy` and
      `https://knode.md/terms` ([knodemd/web](https://github.com/knodemd/web), reviewed by the
      owners with legal advice first). Google and Slack both check them.
- [ ] **A project address** for the consoles, such as `oss@knode.md`, that more than one owner
      reads. Use it as the support and developer contact everywhere below.
- [ ] **The logo**: the app icon from knodemd/brand (`07 App Icon`), as a square PNG of 120x120
      for Google and 512x512 to 2000x2000 for Slack, no transparency at the edges.
- [ ] **Access to the DNS of knode.md**, to add a TXT record for Google.
- [ ] **Admin access to knodemd/knode**, to set the secrets.

Order and time, roughly: Telegram (15 minutes), Google project and client (45 minutes, then
2-3 working days for brand verification), Slack app (30 minutes), the secrets (10 minutes).
Then the long ones: Google's restricted-scope verification (weeks, see below) and the Slack
Marketplace review (weeks, and it needs installs first).

## Telegram

1. On a phone with Telegram, use the account that will own the app (an owner's own account; it
   cannot be moved later). Turn off any VPN: my.telegram.org often fails behind one.
2. Open [my.telegram.org](https://my.telegram.org), enter the phone number in international
   form, and type the code Telegram sends **in the Telegram app** (not by SMS).
3. Open **API development tools**. Fill in the form:

   | Field | Value |
   |---|---|
   | App title | `Knode` |
   | Short name | `knode` (5-32 letters and digits) |
   | URL | `https://knode.md` |
   | Platform | Desktop |
   | Description | the short description from [Texts to paste](#texts-to-paste) |

   The title and short name cannot contain "Telegram". If the form answers only "ERROR", wait a
   day, try from another network, and make sure the account is not brand new.
4. Click **Create application**. The page shows **App api_id** (a number) and **App api_hash**
   (32 hexadecimal characters). These are `KNODE_TELEGRAM_API_ID` and `KNODE_TELEGRAM_API_HASH`.
   Ignore the MTProto server addresses on the same page; the library knows them.

The [Telegram API terms](https://core.telegram.org/api/terms) bind the project from here:

- the client is open source and does nothing an official client would not (no bulk messaging,
  no scraping of chats the person is not in, no flooding);
- it is not called Telegram and does not use Telegram's logo as its own;
- the person is told the app uses the Telegram API and signs in with their own account;
- the `api_id` is Knode's: forks building their own release should register their own;
- Telegram watches accounts on new clients for abuse, and can revoke the `api_id` if the terms
  are broken. Keep the connector polite: it already backs off on `FLOOD_WAIT`.

## Google

Everything happens in the [Google Cloud console](https://console.cloud.google.com) with the
account that will own the project. Add a second owner as soon as the project exists.

### 1. The project and the Gmail API

1. Top bar, project picker > **New project**. Name `Knode`; organisation and location: none
   unless the project has a Google Workspace organisation. **Create**, then select it.
2. **APIs & Services > Library**, search **Gmail API**, open it, **Enable**.
3. **IAM & Admin > IAM > Grant access**: the other owners, role **Owner**.

### 2. The domain in Search Console

Google only accepts a home page, privacy policy and authorised domain it has seen you own.

1. Open [Search Console](https://search.google.com/search-console) with an account that is an
   Owner of the Cloud project.
2. **Add property > Domain**, enter `knode.md`, **Continue**.
3. Copy the `google-site-verification=...` TXT record, add it at the DNS provider of knode.md
   (name `@` or empty, type TXT), wait a few minutes and click **Verify**. Keep the record: removing
   it removes the verification.

### 3. The consent screen (Google Auth Platform)

**APIs & Services > OAuth consent screen** opens the Google Auth Platform. Click **Get started**.

1. **App information**: App name `Knode`; User support email: the project address.
2. **Audience**: **External**.
3. **Contact information**: the project address. Agree to the Google API Services User Data
   Policy, **Create**.
4. **Branding**: upload the 120x120 logo; Application home page `https://knode.md`; Application
   privacy policy link `https://knode.md/privacy`; Application terms of service link
   `https://knode.md/terms`; Authorised domains: **Add domain** `knode.md`; Developer contact:
   the project address. **Save**. A logo makes Google verify the brand (usually 2-3 working days);
   until then the consent screen shows no logo.
5. **Data access > Add or remove scopes**: paste
   `https://www.googleapis.com/auth/gmail.readonly` under **Manually add scopes**, **Add to
   table**, tick it, **Update**, **Save**. It is listed under "Your restricted scopes". This is the
   only scope the connector asks for (`SCOPE` in
   `core/crates/knode-service/src/sources/gmail/oauth.rs`); do not add others, each one has to be
   justified.
6. **Audience**: the publishing status is **Testing**. Under **Test users > Add users**, add the
   Google accounts of the owners and testers, one address per line.

Testing mode, what it means:

- Only the listed test users can sign in, up to **100** for the life of the project (removing a
  test user does not give the place back).
- They see a "Google hasn't verified this app" screen and click **Continue**.
- Their sign-in lasts **7 days**: in Testing, Google expires the refresh token of a restricted
  scope after a week, so Knode asks them to sign in with Google again. This goes away once the
  app is verified and in production.

Do not press **Publish app** before the verification below is ready: in production an unverified
restricted-scope app is still capped at 100 users and shows the same warning.

### 4. The desktop client

1. **Clients > Create client**. Application type **Desktop app**, name `Knode desktop`.
   **Create**.
2. The dialog shows the **Client ID** (ending in `.apps.googleusercontent.com`) and the **Client
   secret** (starting `GOCSPX-`). Download the JSON if you like, then store both as
   `KNODE_GOOGLE_CLIENT_ID` and `KNODE_GOOGLE_CLIENT_SECRET`. Newer consoles show the secret only
   once: if it is lost, add a new secret on the client and delete the old one.
3. There are no redirect URIs to enter: desktop clients accept any loopback port, and Knode
   redirects to `http://127.0.0.1:<port>` with PKCE.

The "secret" of a desktop client is not confidential: Google's
[installed-app guide](https://developers.google.com/identity/protocols/oauth2/native-app) says it
is embedded in the app and cannot be kept secret, and RFC 8252 says the same. PKCE protects the
exchange. It goes in a secret anyway, so it is not copied around, but anyone can read it from a
release binary; that is expected.

### 5. Verification of the restricted scope

`gmail.readonly` is a **restricted scope**. Past the 100 test users, and to drop the warning and
the 7-day sign-in, Google must verify the app. Start as soon as the client works, since it takes
weeks; the beta runs on test users meanwhile.

What Google asks, from its
[restricted scope verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/restricted-scope-verification)
pages:

- **A verified domain and a real home page.** knode.md must describe Knode and what it does with
  Gmail, and link to the privacy policy. The home page should name the Gmail connector, or link
  to a page that does.
- **A privacy policy** on knode.md, linked from the home page and the consent screen, that says
  what Google data is read, how it is used, stored and shared, how to delete it, and contains the
  **Limited Use disclosure** of the
  [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy)
  (text below). The draft at knode.md/privacy has it.
- **A justification for the scope**: why the narrowest scope that works is `gmail.readonly`
  (text below).
- **A demo video**, unlisted on YouTube, in English (script below).
- **Answers to a questionnaire** about the data: where it is stored, whether it reaches a server,
  whether people see it, whether it trains AI models.
- **Possibly a security assessment (CASA)** by an assessor Google approves, renewed every year,
  paid by the developer.

To submit: **Verification Center > Prepare for verification** (or the **Submit for verification**
button once the branding, scopes and links are filled), paste the texts, give the video link,
answer the questions, submit. Google answers by mail to the project address; reply in the same
thread.

What is known and what is not:

- **Known:** verification is required for more than 100 users; it is renewed every year; Google
  needs the items above; the Limited Use disclosure must appear in the privacy policy or on the
  home page; reviewers ask follow-up questions and the first answer is often a request for
  changes.
- **Known, from Google's policy:** the security assessment is required when restricted-scope data
  is stored on or passes through a server. Google's guidance has said that local client apps,
  whose data is processed and stored only on the person's device, do not need it. Knode is one:
  mail goes from Google's API straight to the device, and no Knode server exists. **Say so
  plainly in the justification and the questionnaire.**
- **Uncertain:** whether the reviewer accepts the local-only exemption for Gmail in practice
  (developers report being asked for CASA anyway), and how long the review takes (decision 0013
  estimated about six weeks; it can be longer).
- **Uncertain:** how the reviewer sees the person's own AI agents reading their memory through
  Knode's MCP server. The privacy policy treats it as a transfer the person makes; Limited Use
  allows transfers needed for user-facing features the person chooses. Expect a question on it,
  and answer with how the person connects each agent and chooses its spaces.
- If CASA is asked for: it is a Tier 2 assessment for most apps, done with an approved lab
  (listed on the App Defense Alliance site); budget for a fee and a few weeks.

## Slack

### 1. Create the app from a manifest

1. Sign in at [api.slack.com/apps](https://api.slack.com/apps) with the account of a workspace the
   project controls (create a free workspace for Knode if needed; it becomes the app's home).
2. **Create New App > From a manifest**, pick that workspace, choose **YAML**, paste the manifest
   below, **Next**, **Create**.

```yaml
display_information:
  name: Knode
  description: Brings the Slack conversations you are in into your Knode memory, on your device.
  long_description: >-
    Knode is a local-first memory for you and your AI agents. The Knode Slack app lets Knode, on
    your own computer, read the channels, private channels, direct messages and group messages
    you are already in, and keep them as notes in your Knode space, so you and the agents you
    connect can find what was said. It only reads: it cannot post, edit or delete anything. Your
    messages go straight from Slack to your device; no Knode server receives them. You choose
    which conversations to exclude, and can disconnect at any time.
  background_color: "#111111"
oauth_config:
  redirect_urls:
    - http://localhost:47821/slack/callback
    - http://localhost:47822/slack/callback
    - http://localhost:47823/slack/callback
  scopes:
    user:
      - channels:history
      - groups:history
      - im:history
      - mpim:history
      - channels:read
      - groups:read
      - im:read
      - mpim:read
      - users:read
settings:
  org_deploy_enabled: false
  socket_mode_enabled: false
  token_rotation_enabled: true
```

The redirect URLs and scopes come from the connector:
`REDIRECT_PORTS` / `REDIRECT_PATH` and `USER_SCOPES` in
`core/crates/knode-ingest/src/chats/slack_live/oauth.rs`. If the code changes them, change the
app too. There is no bot user and no bot scope: the app works with a user token only, and Socket
Mode stays off (Marketplace apps cannot use it). Pick the background colour from the brand.

### 2. OAuth & Permissions

1. **OAuth & Permissions**: check the three redirect URLs and the nine **User Token Scopes**.
2. **Turn on PKCE** in the same page (the PKCE section). Without it Slack treats a `localhost`
   redirect as a server redirect and the connector's exchange, which sends no client secret,
   fails. Turning it on marks the app as a public client and cannot be undone without Slack
   support; that is what we want.
3. **Token rotation** is on from the manifest: tokens last 12 hours and the connector refreshes
   them; refresh tokens of PKCE apps last 30 days.

### 3. Values and distribution

1. **Basic Information > App Credentials**: copy the **Client ID** (digits, a dot, digits): it is
   `KNODE_SLACK_CLIENT_ID`. The client secret and signing secret are **not** needed: the
   connector uses PKCE and receives no events. Do not put them anywhere.
2. **Basic Information > Display Information**: upload the 512-2000 px icon, check the name and
   descriptions.
3. **Manage Distribution**: tick the checklist (it asks for the redirect URLs and that no
   credentials are hard-coded), confirm **Remove Hard Coded Information**, and
   **Activate Public Distribution**. Other workspaces can now install Knode; until the
   Marketplace listing is approved, the connector runs at the lower limits (one history call a
   minute, 15 messages a call).
4. **Collaborators**: add the other owners.

### 4. Marketplace submission

From **Manage Distribution > Submit to Slack Marketplace** (checked against Slack's
[guidelines](https://docs.slack.dev/slack-marketplace/slack-marketplace-app-guidelines-and-requirements/)
in November 2026):

- [ ] **Usage first:** Slack treats apps with fewer than 10 active workspaces and fewer than 20
      monthly active users as unsuitable. Get the beta into real workspaces before submitting.
- [ ] Short description (10 words or fewer) and long description (texts below).
- [ ] Icon, high resolution.
- [ ] Screenshots, 1600x1000 (8:5), JPG or PNG under 2 MB, showing the app in Slack (the
      install screen) and the result.
- [ ] Video, recommended: 30-90 seconds, public on YouTube, captions on, ads off.
- [ ] Pricing: Free.
- [ ] Landing page on knode.md for the Slack app: public, built for it, with how to install it,
      what happens after installing, and a link to the privacy policy.
- [ ] Support page: public, with an email address or form, no account needed; GitHub only in
      addition. Slack expects answers within 2 business days.
- [ ] Privacy policy: what is collected, how it is used, how long it is kept, how to ask for
      access or deletion, a contact email.
- [ ] A reason for each scope that says how Knode uses it (texts below).
- [ ] At least one collaborator.
- [ ] A test workspace and steps for the reviewer (texts below).

Risks to know before submitting:

- **History scopes:** Slack says broad `*:history` scopes are unlikely to be approved without a
  clear use case, and gives search and MCP as examples. Knode is both: argue it in the scope
  reasons. A refusal would leave the connector at the lower limits.
- **The install path:** Slack's guidelines describe an install link on the landing page that
  redirects to `slack.com/oauth/v2/authorize`. Knode's install starts in the app (it has to open
  the loopback port and make the PKCE challenge). The landing page should say "Install from
  Knode: Settings > Sources > Slack > Connect"; whether the reviewer accepts it is uncertain.
- The review has no published duration; plan for weeks and a round of questions.

When the listing is approved, set the repository variable `KNODE_SLACK_LIMITS` to `marketplace`
(below) so the next release calls at the Marketplace rates.

## Where the values go

| Name | Service | Kind | Notes |
|---|---|---|---|
| `KNODE_GOOGLE_CLIENT_ID` | Google | secret | ends in `.apps.googleusercontent.com` |
| `KNODE_GOOGLE_CLIENT_SECRET` | Google | secret | starts `GOCSPX-`; not confidential, see above |
| `KNODE_TELEGRAM_API_ID` | Telegram | secret | a number |
| `KNODE_TELEGRAM_API_HASH` | Telegram | secret | 32 hexadecimal characters |
| `KNODE_SLACK_CLIENT_ID` | Slack | secret | digits, a dot, digits |
| `KNODE_SLACK_LIMITS` | Slack | variable | `marketplace` once listed; unset until then |

**Release builds:** repository secrets of knodemd/knode (**Settings > Secrets and variables >
Actions > New repository secret**), or from a terminal, which prompts for the value so it never
lands in the shell history:

```sh
gh secret set KNODE_GOOGLE_CLIENT_ID --repo knodemd/knode
gh secret set KNODE_GOOGLE_CLIENT_SECRET --repo knodemd/knode
gh secret set KNODE_TELEGRAM_API_ID --repo knodemd/knode
gh secret set KNODE_TELEGRAM_API_HASH --repo knodemd/knode
gh secret set KNODE_SLACK_CLIENT_ID --repo knodemd/knode
gh variable set KNODE_SLACK_LIMITS --repo knodemd/knode --body marketplace   # only once listed
```

The release workflow passes them to `cargo build` and `xcodebuild`, so the connectors embed them
(`option_env!`), and checks them with `scripts/check-service-clients.sh`: for a stable version a
missing or malformed value, or a binary that does not embed it, fails the build; for a
pre-release it is a warning. See [chapter 7](07-releasing.md#service-clients).

**Developer builds:** copy `core/.env.services.example` to `core/.env.services` in the knode
checkout and fill in what you have. The file is ignored by git. The Mac build scripts load it;
for plain cargo, `. ../scripts/load-service-clients.sh && cargo build` from `core/`. A value in the
environment wins over the file.

**At run time**, the same variables set when Knode Service starts override the built-in values:
that is how a fork or a tester uses their own Google, Slack or Telegram app.

If a value leaks or must change: create a new one at the service (a new Google client secret, a
new Slack app is rarely needed, a Telegram `api_hash` cannot be rotated without contacting
Telegram), replace the secret, cut a release, then delete the old value at the service.

## Texts to paste

### Short description (Telegram, Slack)

> Knode reads your conversations into a local memory for you and your AI agents.

### App description (Google verification, Slack long description)

> Knode is an open-source, local-first memory for a person and the AI agents they use. It runs on
> the person's own computer (macOS and Linux) and keeps everything as files on that device. Its
> connectors bring in what the person chooses to connect, so they and their agents can search it
> later. The Gmail connector uses Sign in with Google to read the person's mail with the
> gmail.readonly scope and writes each thread as a note in the person's Knode space on their
> device. Mail goes directly from the Gmail API to the device. There is no Knode account and no
> Knode server: Knode's developers never receive, store or see any mail. The connector cannot
> send, modify or delete mail. The person can exclude labels, pause or disconnect at any time,
> and disconnecting removes the token from the system keychain.

### Scope justification: `https://www.googleapis.com/auth/gmail.readonly`

> Knode's Gmail connector keeps a searchable copy of the person's mail in their local Knode
> memory, on their own device, and keeps it up to date as new mail arrives. To do that it lists
> messages and labels, reads each message's headers and body, and follows the mailbox history
> API (users.history.list) to fetch only what is new since the last run. Every label is read
> except Spam, Trash and the labels the person excludes. This needs read access to message
> content and the history API, which gmail.metadata does not give (no bodies) and which
> gmail.labels or gmail.send do not cover. gmail.readonly is the narrowest scope that allows it;
> Knode does not ask for gmail.modify or full mail access because it never changes, sends or
> deletes mail. The data is processed and stored only on the person's device: there is no Knode
> server, and no Gmail data is transmitted to Knode or to any third party by Knode. It is never
> used for advertising, sold, read by people, or used to train AI or machine-learning models.

### Limited Use statement (privacy policy, verification form)

> Knode's use and transfer to any other app of information received from Google APIs will adhere
> to the Google API Services User Data Policy, including the Limited Use requirements.

Followed, in the privacy policy, by what it means for Knode: used only for the feature the
person turned on; not transferred except by the person's own action (an AI agent they connect, a
space they share or sync); never for ads, never sold, never used to train AI models; nobody at
Knode reads it.

### Demo video script (Google)

Record the screen in English, 3-5 minutes, with the system language in English, and upload it to
YouTube as **unlisted**. Use a test account with a few harmless mails.

1. Show knode.md: the home page, then the privacy policy, scrolling to the Google section and
   the Limited Use statement.
2. Open Knode (a release build with the production client ID). Go to **Settings > Sources**.
3. Click **Connect** on Gmail and choose **Sign in with Google**.
4. The browser opens Google's consent screen. **Zoom on the address bar** so the `client_id`
   in the URL is readable and matches the project, and show the app name "Knode" and the
   permission "Read your email messages and settings". Click **Allow**.
5. Show the browser's "You can close this window" page and Knode reporting the connection.
6. Show the history choice (From today / Everything it has) and the label exclusions; exclude a
   label.
7. Show the mail arriving as notes in the Knode space, open one thread, and search for a word
   from it.
8. Send a new mail to the test account and show it arriving.
9. Show that nothing leaves the device: say it, and show the space as files on disk.
10. **Disconnect** the source in Knode, then show the access removed at
    myaccount.google.com/connections.

Say what each step shows as you go, or add captions.

### Slack scope reasons

| Scope | How Knode uses it |
|---|---|
| `channels:history` | Reads new messages and thread replies in the public channels the person is in, to keep them in their local memory and search |
| `groups:history` | The same for the private channels the person is in |
| `im:history` | The same for the person's direct messages |
| `mpim:history` | The same for the person's group direct messages |
| `channels:read` | Lists the public channels the person is in, with names, so they can exclude channels and so notes carry the channel name |
| `groups:read` | The same for private channels |
| `im:read` | Lists the person's direct-message conversations, to read them and name them by person |
| `mpim:read` | Lists group direct messages, to read them and name them |
| `users:read` | Turns user ids into names, so messages and mentions show who wrote them |

General reason, for the history scopes:

> Knode is a local memory and search for the person and the AI agents they connect through
> Knode's MCP server. The person installs the app with their own user token to bring the
> conversations they already take part in to their own computer, where they can search them and
> let their agents recall them. The app only reads; it has no bot, posts nothing and receives no
> events. Messages go straight from Slack to the person's device; Knode has no server that
> receives them. The person excludes any conversation, and an excluded one is never read again.

### Reviewer notes (Slack)

> Install Knode from https://knode.md/install on macOS or Linux. Open Settings > Sources > Slack >
> Connect (or run `knode sources connect slack`). The browser opens Slack's authorization page;
> approve it for the test workspace. Knode brings in the conversations the user is in; search
> for a message with `knode search "<words>"` or in the app. Exclude a channel in Settings >
> Sources > Slack. Disconnect removes the token from the system keychain.
