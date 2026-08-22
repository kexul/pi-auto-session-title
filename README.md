# Pi Auto Session Title

A [Pi](https://pi.dev) extension that automatically assigns a concise title to an unnamed session after its first completed user/assistant exchange.

## Features

- Uses the currently selected Pi model to generate a title in the conversation's primary language.
- Uses the first user prompt and the corresponding assistant response as title context.
- Leaves explicitly named sessions untouched, including when `/name` is used while the background request is running.
- Keeps the title-generation request out of the visible conversation history.
- Treats title generation as best-effort: a failure is logged and never interrupts the session.

## Install

```bash
pi install git:github.com/kexul/pi-auto-session-title
```

Restart Pi, or run `/reload` in an active session, after installation.

To try the extension for a single run without installing it:

```bash
pi -e git:github.com/kexul/pi-auto-session-title
```

To uninstall it later:

```bash
pi remove git:github.com/kexul/pi-auto-session-title
```

## How it works

When Pi reaches `agent_settled` after the first completed exchange, the extension:

1. Skips the session if it already has a name or no active model is available.
2. Extracts the first textual user message and all textual assistant output before the next user message.
3. Makes a small, isolated completion request with the active model (`maxTokens: 64`).
4. Normalizes the first returned line and saves it as the session name if no manual name was set in the meantime.

Titles are limited to 40 characters. The prompt asks for approximately 8–20 Chinese characters or 4–10 words and preserves important technical terms and file names.

## Privacy and cost

Generating a title makes one additional request to your currently selected model provider. That request includes up to 2,400 characters from the first user message and up to 2,400 characters from the first assistant response. It can therefore incur provider usage and should only be enabled when sending that content to the selected provider is acceptable.

The request uses `cacheRetention: "none"` and is not appended to the Pi session conversation.

## Development

The extension entry point is [`auto-session-title.ts`](./auto-session-title.ts). Pi loads TypeScript extensions directly; no build step is required.

## License

[MIT](./LICENSE)
