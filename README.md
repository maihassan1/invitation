# Wedding Invitation Page

A single-file, mobile-first wedding invitation — same idea as
https://nada5ashraf.github.io/mazenwedding/ but with all the content pulled into
one editable block.

## 1. Customise

Open `index.html`, scroll to the bottom, and edit the `INVITE = { ... }` object
(around line 300). That is the only part you need to touch:

| Field | What it does |
|---|---|
| `groom`, `bride`, `monogram` | Names shown in the hero, gate and footer |
| `date` | Drives the countdown, the calendar file and every printed date |
| `timeLabel`, `endHour` | Displayed start time / calendar end time |
| `venue`, `area`, `city` | Venue block |
| `mapsQuery` | A search phrase **or** a full Google Maps URL |
| `heroImage` | Illustration at the bottom of the opening block (`images/hero.jpg`) |
| `venueImage` | Background photo URL (or a local file like `venue.jpg`) |
| `arabicLine` | Optional Arabic line — set to `""` to hide |
| `rsvpPhone` | WhatsApp number, country code first, no `+` or spaces |
| `rsvpBy` | RSVP deadline text |

Colours and fonts live in the `:root { ... }` block at the very top of the file.

## 2. Swapping the hero illustration

`images/hero.jpg` is the artwork under the opening block; `images/hero-original.jpg`
is the untouched upload. The page blends it with `mix-blend-mode: multiply`, which
makes a **white background disappear** into the cream page — so any replacement
should be artwork on plain white (or a PNG with transparency). If the new file has
empty space above the artwork, trim it first:

```bash
python3 - <<'EOF'
from PIL import Image, ImageChops
im = Image.open('images/new-art.jpg').convert('RGB')
bg = Image.new('RGB', im.size, (255,255,255))
box = ImageChops.difference(im, bg).convert('L').point(lambda p: 255 if p > 12 else 0).getbbox()
im.crop(box).save('images/hero.jpg', quality=88, optimize=True)
EOF
```

## 3. Add music (optional)

Drop an MP3 named `music.mp3` next to `index.html`. If the file isn't there,
the music section hides itself automatically. Keep it under ~3 MB — guests open
this on mobile data. Use music you have the right to use.

## 4. Preview locally

```bash
cd ~/invitation
python3 -m http.server 8000
```

Then open http://localhost:8000 — also try it in your phone's browser, since
that's how nearly every guest will see it.

## 5. Publish free on GitHub Pages

```bash
cd ~/invitation
git init -b main
git add .
git commit -m "Wedding invitation"
gh repo create wedding --public --source=. --push     # or create the repo on github.com
```

Then: repo → **Settings → Pages → Source: Deploy from a branch → main / (root)** →
Save. Two minutes later it's live at:

```
https://<your-username>.github.io/wedding/
```

Share that link on WhatsApp. Anything you push to `main` afterwards updates the
live page.

### Alternatives to GitHub Pages
- **Netlify Drop** (netlify.com/drop) — drag the folder into the browser, instant URL, no git.
- **Cloudflare Pages** / **Vercel** — same idea, connect the repo.
- Custom domain (e.g. `mazenandmai.com`) can be pointed at any of them.

## Notes
- The opening curtain has an **Open Invitation** button because browsers block
  audio that starts on its own — that tap is what unlocks the music.
- `prefers-reduced-motion` is respected: animations switch off for guests who
  ask their phone for less motion.
- Everything runs client-side, so there's no backend and nothing to pay for.
  RSVPs come to you as WhatsApp messages; if you'd rather collect them in a
  spreadsheet, swap the two RSVP links for a Google Form link.
