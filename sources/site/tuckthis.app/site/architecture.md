# Source: https://tuckthis.app/site/architecture

Architecture

# Local-first mind, hosted chores

Tuck keeps the mind encrypted in the browser. Hosted pieces exist so accounts, inbound mail, and reminders still work when no tab is open — and so a second laptop can download the same ciphertext and unlock it with your passphrase.

How requests travel

you → tuckthis.app → your vault in the browser

sign-in cookie → account service (ciphertext sync, never plaintext)

share link → a snapshot of one tuck (only what you published)

mail → you@sendto.tuckthis.app → your mailbox

## Your browser

Titles, bodies, tag names, and todo titles stay encrypted on this device. Your passphrase unwraps them. Lock clears the key from memory.

## iPhone and iPad

The native app uses the same vault wraps as the browser. Face ID unlocks a Secure Enclave key that unwraps the DEK. Widgets and the share sheet write pending captures; they never hold the raw DEK. Subscriptions stay on the website.

## tuckthis.app

The website you sign into. It holds the vault UI, marketing pages, and an HttpOnly session cookie. It never sees your vault passphrase.

## Accounts

Email, password, and your send-to address. Encrypted vault snapshots can live here so a second laptop can download them. Hosted software cannot read thoughts.

## Share links

You can publish a snapshot of one tuck as a link. That copy is opt-in so a recipient can read it without the vault. Trial accounts omit attachments on the published copy.

## Inbound mail

you@sendto.tuckthis.app arrives as ordinary email, then becomes a capture in your mailbox. That path is not the vault.

## Reminders & plan

Trials, paid access, and reminder delivery keep working when no tab is open. They do not store note bodies.

## What we never host

Your vault passphrase and the plaintext recovery kit. A stolen account password still cannot unwrap thoughts.

The trust split in more detail: [account password vs vault passphrase vs recovery kit](https://tuckthis.app/site/crypto).