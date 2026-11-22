# Play Store Search API — demo site

A basic search page built on the [Play Store Search API](https://github.com/FAK-LABS/playstore-search-api). It is a showcase: this is what you can build with the API. One static file, no framework, no build step.

**Live demo:** https://play-api.faizankhichi.me/demo

## What it shows

- Search box + filters (free / paid, sort by relevance / rating / installs / recently updated) calling the live API.
- App cards: icon, name, developer, score + ratings, installs, price, genre.
- A **Join Beta** button on every card — it opens that app's Google Play beta page (`play.google.com/apps/testing/<package>`). Beta availability is not verifiable without a Google sign-in, so the button never claims a beta exists; the user signs in on Google's page and sees whether the program is open, full, or unavailable.
- Click a card for full details: description, screenshots, rating histogram, developer contact, recent changes.

## Run it anywhere

Just open `index.html` in a browser, or serve the folder with any static server:

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

It calls `https://play-api.faizankhichi.me` directly — no API key, no sign-in, CORS is open. Point it at your own host by changing the `API` constant at the top of the `<script>`.

## Files

- `index.html` — the whole demo: markup, CSS, and JavaScript in one file.

API docs: https://github.com/FAK-LABS/playstore-search-api
