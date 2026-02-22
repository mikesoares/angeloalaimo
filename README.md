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

No build step — the project is a single static HTML file.

```bash
# Deploy to production
rsync -avz -e "ssh -p 4947" index.html angeloal_deploy@ispeakofcake.com:/web/
```

## Project Structure

```
angeloalaimo/
├── index.html    # Single-page landing (HTML + inline CSS + inline JS)
├── .gitignore
├── CLAUDE.md
├── README.md
├── TODOS.md
└── tmp/          # User-provided staging files (gitignored)
```

## License

MIT
