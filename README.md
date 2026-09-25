# Lönetracker

A small web app for tracking work hours, salary, and parking costs at ÖB (a Swedish clothing retailer), built to work out Swedish OB (obekväm arbetstid, i.e. inconvenient-hours) surcharges automatically instead of doing it by hand every pay period.

## Why this exists

Retail shift schedules in Sweden usually come with OB surcharges that kick in on evenings, weekends, and Sundays, and the rate depends on the day and time. Working that out by hand for every shift, every week, gets old fast. This app takes a weekly schedule export (CSV or Excel) and turns it into gross pay, net pay after tax, and what's actually left over once other expenses and parking are accounted for.

## What it does

- Upload a schedule (`.csv` or `.xlsx`) with `Datum`, `Start`, and `Slut` columns (a break/`Rast` column is optional)
- Splits each shift into base hours and OB 50% / 70% / 100% hours per Detaljhandelsavtalet §8.1 (weekdays 18.15–20.00 / after 20.00, Saturdays after 12.00, Sundays and Swedish public holidays all day, jul-/nyårs-/midsommarafton as Saturdays), walking overnight shifts day by day
- Places the unpaid rast in the middle of the shift and takes it off whichever OB tiers it lands on, which is how the employer's payroll comes out
- Reads both the "Aktivt schema" and "Närvaro" blocks and pays the overlap (later start, earlier end), so clocked days follow what was actually worked
- Withholds tax from Skatteverket's skattetabell for your kommun (`skattetabell.js`, tax year 2026), or from a percentage you type or derive from a payslip
- Handles semesterlön per day, semesterersättning 13 % and sjuklön (§15.4, karens from your agreed weekly hours) when the export marks those days
- Shows one month at a time, since one lönebesked covers one month
- Tracks other expenses and shows what's left over after they're deducted from net pay
- Tracks other expenses and shows what's left over after they're deducted from net pay
- Tracks parking cost per shift, based on a configurable free-hours allowance and an hourly rate
- Installable as a PWA with its own icon — everything is saved to the browser's local storage, nothing is sent to a server

## Tech

Plain HTML, CSS, and JavaScript. No framework, no build step, no npm dependencies. The Excel parser is hand-rolled — it reads the `.xlsx` file's zipped XML directly using the browser's built-in `DecompressionStream`, so there's no external library for that either. State is persisted with `localStorage`.

## Running it locally

Nothing to install or build. Clone the repo and open `index.html` directly in a browser, or serve the folder with any static file server:

```bash
npx serve .
```

## Files

- `index.html` — the entire app: markup, styles, and logic
- `support.js` — runtime script loaded by the app
- `skattetabell.js` — Skatteverket's monthly tax tables and the kommun → table mapping, converted from the public app's generated `lib/skattetabell.ts`
- `manifest.json` — PWA manifest (name, icons, theme colors)
- `icon-192.png` / `icon-512.png` — app icons

## Calculation

The pay engine is a line-for-line port of the public Lönetracker app (`abboud-said/lonetracker`, `lib/rules.ts`, `holidays.ts`, `calc.ts`, `parse.ts`, `semester.ts`), so the two apps produce the same figures. Checked against real payslips: June 2026 (28 245,81 kr gross, 5 149 kr tax) and July 2026 (26 167,79 kr gross, 4 630 kr tax) both land within 1,30 kr on gross and exactly on tax, the same as the public app.

## Note

The UI is in Swedish, since it was built for a specific real use case: calculating pay and parking costs at an ÖB store.
