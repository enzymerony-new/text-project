# Text Project

Node.js project intended to run as a **TypeScript** + **Next.js** app. Setup is in progress: the repo currently has npm (`package-lock.json`), a `dotenv` dependency, and a Cursor project guide in [`cursor.md`](./cursor.md).

## Requirements

- Node.js 20 or newer (LTS)
- npm (this repo uses `package-lock.json`)

## Setup

```bash
git clone <repo-url>
cd Text-Project
npm install
```

Copy environment variables from an example file when one exists:

```bash
copy .env.example .env.local
```

On macOS/Linux use `cp .env.example .env.local`. Do not commit `.env` or `.env.local`.

## Scripts

| Command | Description |
| --- | --- |
| `npm install` | Install dependencies |
| `npm test` | Placeholder — no tests yet |

Next.js scripts (`dev`, `build`, `start`, `lint`) will be added when the app is initialized.

## Project layout

```text
Text-Project/
├── README.md          # This file
├── cursor.md          # Cursor / AI project guide
├── package.json
└── package-lock.json
```

Application source (`src/app`, components, API routes) is not in the repo yet.

## Stack (planned)

| Layer | Choice |
| --- | --- |
| Runtime | Node.js |
| Language | TypeScript (strict) |
| Framework | Next.js (App Router) |
| UI | React (version required by Next.js) |
| Package manager | npm |

Details, conventions, and env rules live in [`cursor.md`](./cursor.md). Update that file when the stack or folder structure changes.

## License

ISC — see `package.json`.
