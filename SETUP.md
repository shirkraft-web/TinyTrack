# TinyTrack — Google backup setup (optional)

TinyTrack works fully offline with no setup — every entry is saved to your browser's local storage automatically. This guide is only for turning on the **optional** Google Drive backup, so your log survives clearing browser data or switching devices.

You don't need to do any of this to use the app. Skip it if local storage is enough for you.

## Why this is needed

Google's sign-in system won't work when you open `index.html` directly as a file (`file://...`). It only works over `http://` or `https://`. So enabling backup means: (1) create your own free Google Cloud OAuth credential, and (2) serve the app from a real address instead of double-clicking the file.

## Steps

1. **Create a Google Cloud project**
   Go to [console.cloud.google.com](https://console.cloud.google.com), create a new project (any name, e.g. "TinyTrack").

2. **Configure the OAuth consent screen**
   In the project, go to *APIs & Services → OAuth consent screen*. Choose **External**, fill in the required app name/email fields, and leave it in **Testing** mode (fine for personal use — add your own Google account as a test user).

3. **Create an OAuth 2.0 Client ID**
   Go to *APIs & Services → Credentials → Create Credentials → OAuth client ID*. Application type: **Web application**. Under **Authorized JavaScript origins**, add the exact address you'll open the app from, for example:
   - `http://localhost:5500` (a local static server)
   - `https://yourname.github.io` (if hosted on GitHub Pages)

   Save it, then copy the generated **Client ID** (looks like `xxxxx.apps.googleusercontent.com`).

4. **Enable the Google Drive API**
   Go to *APIs & Services → Library*, search for "Google Drive API", and enable it for the project.

5. **Paste the Client ID into the app**
   Open `index.html`, find this line near the top of the `<script>` block:
   ```js
   const GOOGLE_CLIENT_ID = 'YOUR_GOOGLE_CLIENT_ID.apps.googleusercontent.com';
   ```
   Replace it with the Client ID you copied.

6. **Serve the app over http instead of opening the file directly**
   From the `TinyTrack` folder, run one of:
   ```bash
   npx serve .
   # or
   python -m http.server 5500
   ```
   Then open the printed `http://localhost:...` address in your browser — it must match an origin you added in step 3.

   For real cross-device sync (e.g. checking the log from your phone too), host the folder somewhere reachable from both devices, such as GitHub Pages, and add that URL as an Authorized JavaScript origin in step 3 instead of (or in addition to) localhost.

## Using it

Once configured, open the History panel and tap **Sign in with Google**. Your existing local entries are backed up to a private file in your own Google Drive (not visible in your regular Drive file list — it's stored in Drive's hidden app-data area). From then on, new entries sync automatically a couple seconds after you log them. Signing out never deletes anything on this device — it only stops syncing.

# Siri Shortcuts (optional, iPhone)

You can log entries by voice ("Hey Siri, Log Pee") using the iOS Shortcuts app. This works because the page reads a `?log=...` URL parameter on load and logs the matching entry automatically — no Google setup required for this part, just the app being reachable at a real URL (already true at `https://shirkraft-web.github.io/TinyTrack/`).

**Heads up:** triggering a Shortcut briefly opens Safari to run the page — iOS has no way to run a Home Screen web app's code silently in the background without a native app. The entry still gets saved to the same storage the Home Screen app reads (same site, same origin), so it shows up correctly in History either way. It's a couple seconds on screen, not a hidden background action.

## Setup

For each action you want, create a Shortcut in the Shortcuts app:

1. Open **Shortcuts** → tap **+** → **Add Action** → search for **Open URLs**.
2. Enter one of these URLs:
   - Log Pee: `https://shirkraft-web.github.io/TinyTrack/?log=pee`
   - Log Poop: `https://shirkraft-web.github.io/TinyTrack/?log=poop`
   - Log Diaper (pee + poop together): `https://shirkraft-web.github.io/TinyTrack/?log=both`
   - Feeding (start/finish toggle — first run starts the timer, next run finishes it): `https://shirkraft-web.github.io/TinyTrack/?log=feed`
3. Name the Shortcut something clear, e.g. "Log Pee" — that alone is usually enough for **"Hey Siri, Log Pee"** to work. For a custom phrase, open the Shortcut's **⋯** menu → **Add to Siri** and record one.

Repeat for whichever of the four you want available by voice.
