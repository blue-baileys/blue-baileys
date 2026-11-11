# BLUE/baileys

**BLUE Baileys** — WhatsApp Multi-Device library (Baileys fork)


## Features 

- `sock.rvo()` — Reveal View Once
- Android browser support for view-once media
- Group Status / Interactive messages / Album support
- All core Baileys multi-device APIs


> [!NOTE]
> **🚀 New View-Once Architecture**
> You must use new logic to bypass WhatsApp's restrictions. This is the modern standard for handling ephemeral media.

### 👁️ How View-Once Downloading Works Now

WhatsApp implements strict restrictions on View-Once messages. Desktop/Web clients are not sent the media payload, and quoting a View-Once strips the `directPath` and `url`, making traditional vv downloads fail with `Invalid Media URL`.

To combat this, we use a **3-step workaround**:

*   **1. Android Device Spoofing:** 
    We initialize the socket using `Browsers.android('14')` or similar. WhatsApp trusts mobile devices more than web clients. By spoofing an Android device, WhatsApp sends the **complete, unredacted `viewOnceMessage` payload** (including encryption keys and media URLs) directly to the bot.
*   **2. Immediate Download (Zero-Wait):** 
    The moment a View-Once hits the `messages.upsert` event, BOTS should intercept it and instantly decrypt/download the media via `downloadContentFromMessage`.do not wait for a command. *(This is the only way, as WhatsApp hides the media from desktop users and URLs expire quickly).*
*   **3. Local Indexing & Storage:** 
    Once downloaded, implement saving of the media local disk , and the metadata is stored in a local database. track everything by the unique **Message ID** and **Stanza ID** for easy lookup

### 🛠️ The `.vv` Command Workaround
Because WhatsApp strips the media URL when you quote a message, the `.vv` command no longer reaches out to WhatsApp's servers. 
Instead, act as a **Local Indexer Search**:
*   eg When you reply with `.vv`, the bot must take the `stanzaId` of the quoted message.
*   It queries the local database for that ID.
*   It reads the file directly from the local disk and resends it instantly. 

### 💾 Disk Usage & Management
To prevent your VPS from running out of storage,  implement an automatic cleanup cycle:

*    A background worker that  runs intervals  to automatically delete View-Once media older than intended time ensuring your disk space is never bloated is recommended.
## Install

```bash
npm install github: blue-baileys/blue-baileys
```

Or alias as official name in your bot `package.json`:

```json
{
  "dependencies": {
    "@whiskeysockets/baileys": "github: blue-baileys/blue-baileys"
  }
}
```

```js
import makeWASocket from '@whiskeysockets/baileys'
```

## Credits

- Base: [WhiskeySockets/Baileys](https://github.com/WhiskeySockets/Baileys)
- Upstream fork features: [LORDMEGA-MD/baileys](https://github.com/LORDMEGA-MD/baileys) / `@outlaw1/baileys`
- Modification (View Channel / branding removal): **MR ADMIN BLUE 𓃵**

## License

MIT
