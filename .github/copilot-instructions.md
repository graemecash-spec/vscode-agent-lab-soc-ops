---
description: Workspace conventions for Social Bingo game (Soc Ops)
author: Harald Kirschner
---

# Soc Ops Workspace Instructions

React 19 + TypeScript social bingo game for in-person mixers with Tailwind CSS v4.

## Pre-Push Checklist

- [ ] `npm run lint` – ESLint passes (fix: `eslint . --fix`)
- [ ] `npm run build` – Production build succeeds
- [ ] `npm run test` – All tests pass

## Quick Commands

```bash
npm run dev      # Vite dev server (http://localhost:5173)
npm run build    # Production build
npm run test     # Vitest
npm run lint     # ESLint
```

## Architecture

**State**: `useBingoGame` hook centralizes state, localStorage persistence (version-validated), guards SSR via `typeof window`.

**Logic**: Pure functions in `bingoLogic.ts` — `generateBoard()`, `toggleSquare()`, `checkBingo()`, `getWinningSquareIds()`. Testable, deterministic, no side effects.

**Components**: Prop-based presentational components (`GameScreen`, `BingoBoard`, `BingoSquare`, `StartScreen`, `BingoModal`) receive props from `App.tsx` via hook, emit callbacks.

**Board**: 5×5 grid (25 squares, indices 0–24). Center (index 12) = free space, always marked. Winning lines: 5 rows + 5 columns + 2 diagonals.

## Patterns

**Game State Flow**:
```
App → StartScreen (gameState='start')
   → GameScreen (gameState='playing'|'bingo')
       → BingoBoard → BingoSquare (×25)
       → BingoModal
```

**Testing**: Vitest (`*.test.ts`). Game logic in `src/utils/bingoLogic.test.ts`. Pattern: Arrange → Act → Assert with `@testing-library/react`.

## Tailwind CSS v4

**Required Reading**: [`.github/instructions/tailwind-4.instructions.md`](.github/instructions/tailwind-4.instructions.md)

**Key Points**:
- Configure via `@theme` in CSS (`src/index.css`), not `tailwind.config.js`
- Auto-detects content in `src/**` (configured in `vite.config.ts`)
- Use native opacity syntax: `bg-black/50` (not `bg-opacity-50`)
- Container queries: `@container`, `@md:text-lg`
- Design tokens in `@theme`, regular vars in `:root`

## Development

**Add Questions**: Edit `src/data/questions.ts`. Keep concise (fit square UI). Shuffle at board generation.

**Modify Logic**: Update pure functions in `bingoLogic.ts`, add tests immediately, hook auto-uses updated logic.

**Style**: Use Tailwind v4 classes. Brand colors in `@theme` (see [tailwind-4.instructions.md](.github/instructions/tailwind-4.instructions.md)). Responsive: `md:`, `lg:` breakpoints.

## File Locator

```
src/
  App.tsx                    ← Entry, renders based on gameState
  index.css                  ← Tailwind @theme, global styles
  components/
    GameScreen.tsx, BingoBoard.tsx, BingoSquare.tsx
    StartScreen.tsx, BingoModal.tsx
  hooks/useBingoGame.ts      ← Central state + localStorage
  utils/bingoLogic.ts        ← Pure game functions (generate, toggle, check)
  utils/bingoLogic.test.ts   ← Game logic tests
  data/questions.ts          ← Question pool, FREE_SPACE constant
  types/index.ts             ← BingoSquareData, BingoLine, GameState
```

## Common Tasks

| Task | File | How |
|------|------|-----|
| Change winning condition | `bingoLogic.ts` | Modify `checkBingo()` |
| Add questions | `data/questions.ts` | Add strings to array |
| Style button | `components/*.tsx` | Use Tailwind classes |
| Fix state bug | `hooks/useBingoGame.ts` | Check localStorage validation |
| Optimize render | `components/*.tsx` | Use `useMemo` if needed |

## Known Constraints

- Single-device localStorage only, no cloud sync
- 5×5 board (fixed, 25 squares, 1 free space)
- Client-side React 19 only (no SSR)
