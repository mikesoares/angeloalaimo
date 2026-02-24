# angeloalaimo

Static landing page for Angelo Alaimo. A centered rounded square divided into 4 colored quadrant buttons — currently the LinkedIn quadrant (top-left, blue) is active.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Markup | HTML5 |
| Styling | CSS3 (inline) |
| JavaScript | Vanilla ES5 (inline) |
| Build | None (static files) |

## Requirements

Any modern web browser. No server, no build tools, no dependencies.

## Local Development

```bash
# Open in default browser (macOS)
open index.html

# Or serve locally with Python (optional, for testing)
python3 -m http.server 8321
```

No install, build, lint, or test commands.

## Building & Deployment

No build step — static files served as-is.

```bash
# Deploy to production
rsync -avz -e "ssh -p 4947" index.html .htaccess favicon.svg favicon.ico apple-touch-icon.png angeloal_deploy@ispeakofcake.com:/web/
```

## Project Structure

```
angeloalaimo/
├── index.html          # Single-page landing (HTML + inline CSS + inline JS)
├── .htaccess           # Apache config: disable directory listings, force HTTPS, www→non-www redirect
├── favicon.svg         # Vector favicon (modern browsers)
├── favicon.ico         # Multi-size ICO (16/32/48px, legacy browsers)
├── apple-touch-icon.png # 180x180 PNG (iOS home screen)
├── .gitignore
├── CLAUDE.md
├── README.md
├── TODOS.md
└── tmp/                # User-provided staging files (gitignored)
```

## License

Private — all rights reserved.
