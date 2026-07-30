# NextSound

NextSound is a responsive music-discovery application built with React, TypeScript, and the Spotify Web API. It includes a demo mode, music search, a playback queue, and an Express proxy that keeps Spotify authentication on the server.

## Features

- Browse popular, recent, classic, chill, and throwback music
- Search tracks with the command palette
- Run without Spotify credentials using built-in demo data
- Play available Spotify audio previews
- Simulate playback when a preview is unavailable
- Queue complete music sections from any selected track
- Add and remove individual queue items
- Next, previous, shuffle, repeat-one, and repeat-all controls
- Seek, mute, and adjustable volume controls
- Responsive light and dark interfaces
- Offline API caching and request retries

## Important playback limitation

NextSound uses the preview URLs returned by Spotify. Spotify does not provide previews for every track, so some tracks use simulated progress without audio.

Playing complete Spotify songs would require Spotify OAuth, the Spotify Web Playback SDK, and a Spotify Premium account.

## Technology

- React 18
- TypeScript
- Vite
- Tailwind CSS
- Redux Toolkit and RTK Query
- React Router
- Framer Motion
- Express
- Spotify Web API

## Requirements

- Node.js 22 LTS
- npm
- Optional Spotify developer credentials for live catalog data

## Run locally

Clone the repository and install its dependencies:

```bash
git clone https://github.com/itsnextwork/nextsound.git
cd nextsound
npm install
```

Start the frontend in demo mode:

```bash
npm run dev
```

Open [http://localhost:5173](http://localhost:5173).

### PowerShell note

If PowerShell reports that `npm.ps1` cannot run because scripts are disabled, use the command wrapper:

```powershell
npm.cmd install
npm.cmd run dev
```

## Spotify API setup

Demo mode works without credentials. To use live Spotify data:

1. Create an application in the [Spotify Developer Dashboard](https://developer.spotify.com/dashboard).
2. Copy `.env.example` to `.env`.
3. Uncomment and fill in the Spotify credentials:

```env
VITE_SPOTIFY_CLIENT_ID=your_client_id
VITE_SPOTIFY_CLIENT_SECRET=your_client_secret

VITE_USE_BACKEND_PROXY=true
VITE_PROXY_SERVER_URL=http://localhost:3001
VITE_ENABLE_OFFLINE_CACHE=true
VITE_API_RETRY_ENABLED=true

PORT=3001
FRONTEND_URL=http://localhost:5173
```

4. Start the frontend and backend together:

```bash
npm run dev:full
```

The frontend runs on port `5173` and the proxy server runs on port `3001`.

Never commit the `.env` file or expose the Spotify client secret in browser code. The Express proxy reads the credentials and handles Spotify access tokens.

## Available commands

```bash
npm run dev          # Start the Vite frontend
npm run dev:full     # Start the frontend and development proxy
npm run server       # Start only the proxy server
npm run server:dev   # Start the proxy with automatic restart
npm run build        # Type-check and create a production build
npm run preview      # Preview the production frontend
npm run start        # Start the proxy and production preview
```

## Queue behavior

- Clicking a track replaces the queue with that section and begins at the selected track.
- The plus button appends a track and opens the queue panel.
- The queue panel can select, remove, or clear upcoming tracks.
- At the end of the queue, playback stops unless repeat-all is enabled.
- Tracks without audio previews advance after simulated playback completes.

## Project structure

```text
nextsound/
├── server/                 Spotify API proxy
├── src/
│   ├── common/             Shared layout and sections
│   ├── components/ui/      Cards, player, queue, and controls
│   ├── context/            Global application contexts
│   ├── data/               Demo music catalog
│   ├── hooks/              Audio player and application hooks
│   ├── pages/              Route pages
│   ├── services/           Spotify and music data services
│   ├── store/              Redux Toolkit configuration
│   └── utils/              Configuration, caching, and helpers
├── .env.example
└── package.json
```

## Troubleshooting

### No sound

The selected track may not have a Spotify preview. The player will continue using simulated progress. Use a track with a valid preview URL or add licensed local audio samples for reliable demo playback.

### Spotify requests fail

- Confirm `.env` contains valid credentials.
- Start both services with `npm run dev:full`.
- Check the proxy health endpoint at [http://localhost:3001/health](http://localhost:3001/health).
- Confirm ports `5173` and `3001` are available.

### PowerShell blocks npm

Run npm through `npm.cmd`, or allow trusted local scripts for your user:

```powershell
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
```

### Build reports missing test type definitions

The TypeScript configuration references the optional Vitest and Testing Library types. Install them before building if they are not already present:

```bash
npm install --save-dev vitest @testing-library/jest-dom
```

