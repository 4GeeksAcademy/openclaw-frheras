# TOOLS.md - Connected services and defaults

This file records the services currently enabled through Zapier, when to use them, and user-specific defaults. Do not assume access to other Google apps merely because they belong to the same Google account.

## Google Docs
- **Status:** Enabled through Zapier.
- **Use when:** Félix explicitly asks to create or retrieve a Google document.
- **Default Drive folder:** Not confirmed. Do not assume a folder or move documents between folders; use only the destination specified in the request, if folder selection is available.
- **Creation details:** Confirm the requested title and content. Report the resulting document link when the integration returns one.

## Google Calendar
- **Status:** Enabled through Zapier.
- **Use when:** Félix asks to look up or create calendar events.
- **Default calendar:** Not confirmed. Check the available calendars and use the intended calendar; if it is ambiguous, ask Félix before creating an event.
- **Time zone:** Resolve it from the selected calendar before creating events. Interpret requested local times accordingly and verify the created event's date and time.

## Google Drive
- **Status:** Not confirmed as an enabled Zapier service. Google Docs access does not by itself confirm general Drive browsing or file-management access.
- **Use when:** Do not assume Drive search, folder browsing, moving, or other file-management actions are available. Verify the available Zapier tools before offering or attempting them.
- **Default folder:** Not confirmed.

## Gmail
- **Status:** Not enabled through Zapier, based on the services Félix has reported as currently enabled.
- **Use when:** Do not use Zapier to draft or send Gmail messages unless Gmail is enabled and the requested action is explicitly authorized.
- **Default signature:** Not applicable / not confirmed. Do not invent or assume a signature.

## Defaults to confirm
- Preferred Google Calendar, if there is one.
- Google Drive folder for new documents, if folder selection is supported and a standard destination is wanted.
- Gmail signature, if Gmail is enabled in the future.
