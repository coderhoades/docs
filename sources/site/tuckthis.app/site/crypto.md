# Source: https://tuckthis.app/site/crypto

Crypto

# Why a password, a passphrase, and a recovery kit

They look like three passwords. They are three jobs. Mixing them would either let the server read your mind or lock you out with no backup.

| Secret | Job | Who can reset it |
| --- | --- | --- |
| Account password | Signs you into tuckthis.app. Session cookie. Syncs ciphertext between devices. | Website login only. Never unwraps thoughts. |
| Vault passphrase | Daily unlock on this device (and others after they sync). Stretched with PBKDF2 on the device. Not sent to AWS. | Nobody. Forgot it? Use the kit. |
| Recovery kit | Second wrap of the same random data-encryption key. Print it, or generate a new one later from Account → Vault. A sealed copy can unwrap in an unlocked browser after sync. | You can mint a replacement. Lose both passphrase and kit and the ciphertext is gone. |

## What 1Password does — and what we do not

1Password mixes your Master Password with a Secret Key so a stolen server dump plus a phished password is still not enough. Tuck started local-first: ciphertext lived only in IndexedDB. Asking for the recovery kit every morning would be a worse daily life for a threat we did not have yet.

So Tuck uses **dual wrap** of one random AES-256-GCM key (the DEK):

1. The device generates a 256-bit DEK.
2. Titles, bodies, tag names, attachment bytes, todo titles, calendar/mail secrets, and people names, emails, notes, and files are sealed with that DEK.
3. The DEK is encrypted twice: passphrase (PBKDF2, 600,000 iterations) and recovery kit (10,000 iterations — the kit is already high entropy).
4. Daily unlock = passphrase. Disaster unlock = kit. Refresh does not re-prompt until you lock or sign out.

## Sync does not change this

A signed-in device uploads wraps + sealed notes to identity. A new laptop: account password → download blob → passphrase (or kit). AWS still cannot read thoughts. An operator with the account password still cannot unwrap the DEK.

Brain names, dates, and reminder stubs stay in the clear so calendar, kanban, and the ingest worker can function without unlock. Sharing a tuck is opt-in: you publish a snapshot so a link can work without the vault. Trial accounts omit attachments on that copy.

## What we will not do

- Derive the vault from the account password. A password reset would become a vault breach.
- Email a “forgot passphrase” link. That would mean we could reconstruct the DEK.
- Store the recovery kit in Secrets Manager. Then we would hold the second wrap’s key.

Using the same string for account password and passphrase collapses two jobs. We do not forbid it; the UI should make it feel like a mistake.

Algorithms and the 1Password comparison in full: `docs/crypto.md` in the repo. How boxes connect: [architecture](https://tuckthis.app/site/architecture).