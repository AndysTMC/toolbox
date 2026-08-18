# Example launchers

Snapshot of the GNOME set on this machine after create + the icon pipeline below. Chrome profiles (`~/.config/chrome-<slug>/`) are **not** included — those hold login cookies.

`.desktop` files still point at `/home/andys-tmc/...`. Treat them as reference, not drop-in installs. Recreate with `../webapp-launcher create --icon icons/<slug>.png ...`.

## Icon pipeline (lossless)

No scaling. ImageMagick crop + extent only:

1. Trim fully transparent / empty margins (`-fuzz 0% -trim +repage`).
2. Square canvas at `max(w,h) × 1.10` — **5% padding on every side**.
3. PNG32 so alpha stays 8-bit.

`create` and `square-icon` both run this. `square-icon` is idempotent: it trims the pad back off and puts the same 5% on again.

## Catalog

| Name | Slug | URL | Icon | Trimmed content → canvas | StartupWMClass |
|------|------|-----|------|--------------------------|----------------|
| Amazon | amazon | https://www.amazon.in/ | [icons/amazon.png](icons/amazon.png) | 6110×2047 → 6721×6721 | `chrome-www.amazon.in__-Default` |
| Apple Music | apple-music | https://music.apple.com/ | [icons/apple-music.png](icons/apple-music.png) | 360×360 → 396×396 | `chrome-music.apple.com__-Default` |
| CodeChef | codechef | https://www.codechef.com/ | [icons/codechef.png](icons/codechef.png) | 512×512 → 563×563 | `chrome-www.codechef.com__-Default` |
| Codeforces | codeforces | https://codeforces.com/ | [icons/codeforces.png](icons/codeforces.png) | 512×412 → 563×563 | `chrome-codeforces.com__-Default` |
| Flipkart | flipkart | https://www.flipkart.com/ | [icons/flipkart.png](icons/flipkart.png) | 512×512 → 563×563 | `chrome-www.flipkart.com__-Default` |
| Gemini | gemini | https://gemini.google.com/ | [icons/gemini.png](icons/gemini.png) | 470×470 → 517×517 | `chrome-gemini.google.com__-Default` |
| Gemini Notebook | gemini-notebook | https://notebook.google.com/ | [icons/gemini-notebook.png](icons/gemini-notebook.png) | 470×470 → 517×517 | `chrome-notebook.google.com__-Default` |
| Gmail | gmail | https://mail.google.com/ | [icons/gmail.png](icons/gmail.png) | 470×374 → 517×517 | `chrome-mail.google.com__-Default` |
| Google Calendar | google-calendar | https://calendar.google.com/ | [icons/google-calendar.png](icons/google-calendar.png) | 408×438 → 482×482 | `chrome-calendar.google.com__-Default` |
| Google Finance | google-finance | https://www.google.com/finance/ | [icons/google-finance.png](icons/google-finance.png) | 301×286 → 331×331 | `chrome-www.google.com__finance_-Default` |
| Google Photos | google-photos | https://photos.google.com/ | [icons/google-photos.png](icons/google-photos.png) | 470×470 → 517×517 | `chrome-photos.google.com__-Default` |
| Google Tasks | google-tasks | https://tasks.google.com/ | [icons/google-tasks.png](icons/google-tasks.png) | 460×449 → 506×506 | `chrome-tasks.google.com__-Default` |
| Instagram | instagram | https://www.instagram.com/ | [icons/instagram.png](icons/instagram.png) | 5000×5000 → 5500×5500 | `chrome-www.instagram.com__-Default` |
| JioHotstar | jiohotstar | https://www.hotstar.com/ | [icons/jiohotstar.png](icons/jiohotstar.png) | 424×377 → 466×466 | `chrome-www.hotstar.com__-Default` |
| LeetCode | leetcode | https://leetcode.com/ | [icons/leetcode.png](icons/leetcode.png) | 359×427 → 470×470 | `chrome-leetcode.com__-Default` |
| LinkedIn | linkedin | https://www.linkedin.com/ | [icons/linkedin.png](icons/linkedin.png) | 635×540 → 699×699 | `chrome-www.linkedin.com__-Default` |
| Netflix | netflix | https://www.netflix.com/ | [icons/netflix.png](icons/netflix.png) | 496×900 → 990×990 | `chrome-www.netflix.com__-Default` |
| Prime Video | prime-video | https://www.primevideo.com/ | [icons/prime-video.png](icons/prime-video.png) | 1041×311 → 1145×1145 | `chrome-www.primevideo.com__-Default` |
| WhatsApp | whatsapp | https://web.whatsapp.com/ | [icons/whatsapp.png](icons/whatsapp.png) | 2880×2880 → 3168×3168 | `chrome-web.whatsapp.com__-Default` |
| X | x | https://x.com | [icons/x.png](icons/x.png) | 2400×2453 → 2698×2698 | `chrome-x.com__-Default` |
| YouTube | youtube | https://www.youtube.com/ | [icons/youtube.png](icons/youtube.png) | 827×579 → 910×910 | `chrome-www.youtube.com__-Default` |

`desktop/<slug>.desktop` is the matching launcher.

Chrome builds `StartupWMClass` from **host + path**, not host alone. A site at `/finance/` is `chrome-www.google.com__finance_-Default`, not `chrome-www.google.com__-Default`. The script guesses that; `fix-wmclass` still wins if the live window differs.
