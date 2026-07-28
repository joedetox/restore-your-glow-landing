# Restore Your Glow — Deploy Notes

## Source of truth
- **Page:** `Restore Your Glow.dc.html` — authored in the design tool. This is the
  editable master. All page/copy/questionnaire/payload changes happen there.
- **GHL integration spec:** `GHL-INTEGRATION.md`.

## What gets deployed
The page is a Design Component (`.dc.html`) that depends on `support.js` at runtime.
To host it as a normal static page, deploy the **bundled standalone** version:
all CSS/JS inlined, works offline, no runtime dependency.

The design tool produces this standalone bundle on request. Deploy the bundled
HTML as `index.html`.

## Deploy chain
```
Design tool (edit page)  ->  standalone index.html  ->  GitHub repo  ->  Vercel (auto-deploy)
```
- **GitHub -> Vercel:** automatic. Once the repo is linked in the Vercel dashboard,
  every push auto-deploys.
- **Design tool -> GitHub:** manual. A fresh standalone file must be pulled from the
  design tool and committed whenever the page changes.

## Update procedure
1. Request the page change in the design tool.
2. Get a fresh standalone bundle / link from the design tool.
3. Commit it as `index.html` in the repo (overwrite).
4. Push -> Vercel deploys -> live in ~1 min.

## Do not
- Do not hand-edit `index.html` in the repo — it will be overwritten by the next
  bundle and your edits will be lost. Change the page in the design tool instead.
- Do not rename payload keys on the GHL side; keep them in sync with
  `GHL-INTEGRATION.md`.
