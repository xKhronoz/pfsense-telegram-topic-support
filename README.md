# pfSense Telegram Topic Support Patch

This repo contains a custom pfSense patch that adds Telegram forum topic support to `notices.inc`.

## Included Patch

- `pfsense-telegram-topic-support.patch`

The patch updates the Telegram notification code so the existing Chat ID field can also carry a Telegram topic/thread ID.

## Chat ID Format

Normal chat IDs still work unchanged:

- Private chat: `123456789`
- Group chat: `-987654321`
- Channel or supergroup: `-1001234567890`
- Username-based destination: `@mygroup`

To target a Telegram forum topic, use this format:

```text
<chat_id>-topic-<topic_id>
```

Examples:

- `-1001234567890-topic-42`
- `@mygroup-topic-42`

Notes:

- The `-100` prefix is only part of the actual Telegram chat ID for channels and supergroups.
- The patch does not assume every chat ID starts with `-100`.
- The `-topic-<topic_id>` suffix is used only when sending into a Telegram forum topic/thread.

## How To Apply The Custom Patch

### pfSense GUI Method

This is the easiest way if you want to apply the patch directly from the pfSense web interface.

1. In pfSense, install the `System Patches` package if it is not already installed.
2. Go to `System > Patches`.
3. Click `Add New Patch`.
4. Enter a description such as `Telegram topic support`.
5. Use one of these approaches:

   Paste method:
   Paste the contents of `pfsense-telegram-topic-support.patch` into the patch contents/body field.

   URL method:
   Use a GitHub commit patch URL in this format:

   ```text
   https://github.com/xKhronoz/pfsense-telegram-topic-support/commit/<commit_sha>.patch
   ```

   The referenced commit must contain only the `pfsense-telegram-topic-support.patch` file change you want to import.

6. Save the patch.
7. Click `Fetch` if the page offers it, then click `Apply`.
8. Confirm the patch applied successfully.

Do not append `/pfsense-telegram-topic-support.patch` to the commit URL. GitHub will return HTML for that URL, not the patch text expected by `System Patches`.

Do not use a commit that also includes unrelated files such as `README.md` or `.gitignore`. The `.patch` URL returns the whole commit patch, so the commit should be patch-file-only if you want the URL method to work cleanly.

After applying it, go to the Telegram notification settings and use either a normal chat ID or a topic-aware value such as:

```text
-1001234567890-topic-42
```

### CLI Method

These steps assume you have a pfSense source checkout.

1. Copy the patch into your pfSense source tree, or keep it anywhere accessible on the system.
2. Change into the pfSense source root.
3. Apply the patch with:

```sh
patch -p1 < /path/to/pfsense-telegram-topic-support.patch
```

If your tree already contains local edits, dry-run it first:

```sh
patch -p1 --dry-run < /path/to/pfsense-telegram-topic-support.patch
```

## Files Patched

- `src/etc/inc/notices.inc`

## What The Patch Changes

- Adds parsing for `chat_id-topic-topic_id`
- Sends the base value as Telegram `chat_id`
- Sends the suffix value as Telegram `message_thread_id`
- Leaves normal non-topic chat IDs unchanged

## Usage After Patching

In the pfSense Telegram notification settings, enter either:

- A normal Telegram chat ID or username
- A topic-aware value in the form `chat_id-topic-topic_id`

Example:

```text
-1001234567890-topic-42
```
