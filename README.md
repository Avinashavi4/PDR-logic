# Hail Damage Report — PDR Logic challenge (Part 1)

A single-file, no-backend damage report form a shop tech can fill out on a phone
in the field. Open `index.html` in any browser — there's no build step and no
dependencies.

## What it does

- **Vehicle:** Year, Make, Model, Color, and an optional VIN.
- **Panels:** tap them on a top-down diagram of the car instead of hunting
  through a dropdown — that's closer to how a tech actually looks at a vehicle.
  Minimum of 3 panels.
- **Per panel:** number of dents (1–100, with big +/− buttons for gloves and a
  typed field for high counts), average dent size, and severity.
- **Submit:** validates, shows a summary table, and produces the report JSON in
  the shape the brief asked for:

  ```json
  { "vehicle": { "year", "make", "model", "color" },
    "panels": [ { "name", "dentCount", "dentSize", "severity" } ] }
  ```

  (VIN is added to `vehicle` only when it's filled in.)

## Decisions worth calling out

- **One source of truth.** Panel data lives in a single `state` object keyed by
  panel name. The diagram, the data-entry cards, and the JSON all read from it,
  so nothing can drift out of sync, and a panel keeps its dents if you toggle it
  off and back on.
- **Nothing is lost on reload.** The form autosaves a draft to `localStorage` as
  you type and restores it if the page is reloaded or the browser is killed —
  the field is exactly where you can't afford to lose 10 minutes of work.
- **Offline-aware submit.** If there's no connection when you hit submit, the
  report is saved to a local outbox and the summary says so, instead of
  pretending it sent. VIN validation follows the real rule (17 chars, no I/O/Q).

## Known limitation (honest)

There's no server here, so the outbox stores queued reports on the device but
doesn't actually POST them on reconnect — that flush is stubbed. The full
offline-sync strategy (persistence, retry, and how you avoid duplicates or lost
reports) is what Part 2 is about.

## Run it

Just open `index.html`. To test the offline path, open dev tools → Network →
"Offline", then submit.
