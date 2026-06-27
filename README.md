# Housewarming RSVP — backend

A small Express server with a real SQLite database, so RSVPs reliably save
no matter what browser or device a guest uses. Includes the invite page
you already saw, now talking to this backend instead of browser storage.

## What's in here

```
server.js        the API (Express)
db.js            initializes data/rsvps.json (a small JSON-file database) on first run
mailer.js        optional email notification when someone RSVPs
public/index.html   the invite + RSVP form + host dashboard page
.env.example     all the settings you can configure
```

## Run it locally

```bash
npm install
cp .env.example .env
```

Open `.env` and fill in:
- `HOST_PASSCODE` — whatever you want to type in to see the RSVP list
- `HOST_NAME`, `EVENT_DATE`, `EVENT_TIME`, `EVENT_ADDRESS` — your event details
- (optional) the `SMTP_*` and `NOTIFY_EMAIL` fields, if you want an email every
  time someone RSVPs. Leave them blank and RSVPs still save fine, you just
  won't get emailed — Gmail works well here with an "App Password" from
  https://myaccount.google.com/apppasswords

Then:

```bash
npm start
```

Open **http://localhost:3000** — that's the invite page. The data file
`data/rsvps.json` is created automatically the first time the server runs.
Nothing to install or set up for the database itself — no native modules,
no separate database server.

## Checking RSVPs

Click "Hosting? View RSVPs" at the bottom of the page (or visit
`http://localhost:3000/?host=1`), enter your passcode, and you'll see live
counts plus the full list, newest first.

## Putting it online so guests can actually use it

Right now this only works on your own computer. To get a real link to send
out, you need to host the server somewhere it stays running. Easiest free
options:

- **Render.com** or **Railway.app** — connect a GitHub repo (or upload this
  folder), set the same environment variables from `.env` in their dashboard,
  and they give you a public URL.
- Either works with no code changes — just set the environment variables
  through their dashboard instead of a local `.env` file.

One thing to know: most free hosting tiers don't guarantee the filesystem is
permanent — if the service restarts the container, `data/rsvps.json` could
reset. For a single party's RSVP list this is usually fine, but if you want
the list to be bulletproof, say so and I can switch storage to a hosted
Postgres or SQLite-on-a-persistent-disk instead (Render and Railway both
offer free options) — that survives restarts.

## API reference

| Method | Path             | Purpose                                  |
|--------|------------------|-------------------------------------------|
| GET    | `/api/event-info`| Returns host name/date/time/address       |
| POST   | `/api/rsvp`      | Body: `{ name, attending, guests }`       |
| GET    | `/api/rsvps`     | Header `x-host-passcode: <passcode>`      |
