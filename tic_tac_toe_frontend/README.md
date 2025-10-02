# Tic Tac Toe — Ocean Professional (SvelteKit)

A modern, minimalist two-player Tic Tac Toe web app built with SvelteKit. The UI follows the Ocean Professional style guide with blue and amber accents, subtle gradients, modern shadows, rounded corners, and smooth transitions.

## Features

- Two-player local Tic Tac Toe
- Alternating turns with clear status
- Board highlights last move and winning line
- Announces winner or tie
- Restart round and Reset all (scores + board)
- Accessible controls, keyboard focus, responsive layout

## Getting started

Install dependencies:
```bash
npm install
```

Run the dev server:
```bash
npm run dev
```

Build for production:
```bash
npm run build
```

Preview the production build:
```bash
npm run preview
```

## Project structure

- `src/lib/theme.css` — Theme variables, base styles, and utilities.
- `src/routes/+page.svelte` — Main game UI and logic.
- `src/app.css` — Global stylesheet importing the theme.
- `src/app.html` — Base HTML template.

## Accessibility

- Proper ARIA roles (`application`, `grid`, `gridcell`, `status`)
- Live regions for status updates and scores
- Focus-visible styles on interactive elements

## License

MIT
