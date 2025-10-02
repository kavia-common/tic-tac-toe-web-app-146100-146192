<script lang="ts">
  // PUBLIC_INTERFACE
  /** Returns a fresh 3x3 board. */
  function createBoard(): (null | 'X' | 'O')[] {
    return Array(9).fill(null);
  }

  // Game state
  let board: (null | 'X' | 'O')[] = createBoard();
  let current: 'X' | 'O' = 'X';
  let winner: null | 'X' | 'O' = null;
  let isTie = false;
  let lastMoveIndex: number | null = null;

  // Scoreboard
  let scoreX = 0;
  let scoreO = 0;
  let ties = 0;

  // Derived helpers
  const lines = [
    [0,1,2],[3,4,5],[6,7,8], // rows
    [0,3,6],[1,4,7],[2,5,8], // cols
    [0,4,8],[2,4,6]          // diags
  ];
  let winningLine: number[] | null = null;

  // PUBLIC_INTERFACE
  /** Handles a cell click: place mark, check status, toggle turn. */
  function handleCellClick(i: number) {
    if (winner || isTie || board[i] !== null) return;

    board = board.slice();
    board[i] = current;
    lastMoveIndex = i;

    const res = evaluateBoard(board);
    if (res.winner) {
      winner = res.winner;
      winningLine = res.line;
      if (winner === 'X') scoreX++; else scoreO++;
    } else if (res.tie) {
      isTie = true;
      ties++;
    } else {
      current = current === 'X' ? 'O' : 'X';
    }
  }

  // PUBLIC_INTERFACE
  /** Evaluates the current board for winner or tie. */
  function evaluateBoard(b: (null|'X'|'O')[]) {
    for (const [a, c, d] of lines) {
      if (b[a] && b[a] === b[c] && b[c] === b[d]) {
        return { winner: b[a] as 'X'|'O', line: [a,c,d], tie: false };
      }
    }
    const hasEmpty = b.some((v) => v === null);
    return { winner: null, line: null, tie: !hasEmpty };
  }

  // PUBLIC_INTERFACE
  /** Resets the current round but keeps scores. */
  function resetBoard() {
    board = createBoard();
    current = (winner || isTie) ? (winner === 'O' ? 'X' : 'O') : current;
    winner = null;
    winningLine = null;
    isTie = false;
    lastMoveIndex = null;
  }

  // PUBLIC_INTERFACE
  /** Resets everything including scores. */
  function resetAll() {
    resetBoard();
    scoreX = 0;
    scoreO = 0;
    ties = 0;
  }

  // a11y helpers
  function cellAriaLabel(i: number) {
    const v = board[i];
    return v ? `Cell ${i + 1}, ${v}` : `Cell ${i + 1}, empty`;
  }


</script>

<svelte:head>
  <title>Tic Tac Toe — Ocean Professional</title>
  <meta name="description" content="Play a modern, minimalist Tic Tac Toe game with blue & amber accents." />
</svelte:head>

<div class="container">
  <div class="game card" role="application" aria-label="Tic Tac Toe Game">
    <div class="header">
      <div>
        <div class="title">Tic Tac Toe</div>
        <div class="subtitle">Modern, minimalist — Ocean Professional</div>
      </div>

      <div class="scoreboard" aria-label="Scoreboard">
        <span class="badge badge-info" aria-live="polite" aria-atomic="true" title="Player X score">
          X&nbsp;<strong>{scoreX}</strong>
        </span>
        <span class="badge badge-success" aria-live="polite" aria-atomic="true" title="Ties">
          Tie&nbsp;<strong>{ties}</strong>
        </span>
        <span class="badge badge-info" aria-live="polite" aria-atomic="true" title="Player O score">
          O&nbsp;<strong>{scoreO}</strong>
        </span>
      </div>
    </div>

    <div class="status" role="status" aria-live="polite" aria-atomic="true">
      {#if winner}
        <div class="status-bubble win">
          <span class="dot"></span>
          Player {winner} wins!
        </div>
      {:else if isTie}
        <div class="status-bubble tie">
          <span class="dot"></span>
          It’s a tie.
        </div>
      {:else}
        <div class="status-bubble turn {current === 'X' ? 'x' : 'o'}">
          <span class="dot"></span>
          Player {current}'s turn
        </div>
      {/if}
    </div>

    <div class="board" role="grid" aria-label="3 by 3 Tic Tac Toe board">
      {#each board as cell, i (i + ':' + String(cell))}
        {@const isWinning = winningLine ? winningLine.includes(i) : false}
        {@const isLast = lastMoveIndex === i}
        <button
          class="cell {cell ? 'filled' : ''} {isWinning ? 'win' : ''} {isLast ? 'last' : ''}"
          role="gridcell"
          aria-label={cellAriaLabel(i)}
          aria-selected={cell !== null}
          on:click={() => handleCellClick(i)}
          disabled={!!winner || isTie || cell !== null}
        >
          <span class="mark {cell === 'X' ? 'x' : cell === 'O' ? 'o' : ''}">
            {cell}
          </span>
        </button>
      {/each}
    </div>

    <div class="actions">
      <button class="btn btn-primary" on:click={resetBoard} aria-label="Restart round">
        Restart Round
      </button>
      <button class="btn btn-outline" on:click={resetAll} aria-label="Reset scores and board">
        Reset All
      </button>
      <span class="hint">Tip: Use the <span class="kbd">Tab</span> key to navigate.</span>
    </div>
  </div>

  <footer class="footnote">
    Built with Svelte • Ocean Professional theme
  </footer>
</div>

<style>
  .game {
    padding: 1.25rem 1.25rem 1.5rem;
    backdrop-filter: saturate(1.2);
  }

  .scoreboard {
    display: flex;
    gap: 0.5rem;
    align-items: center;
  }

  .status {
    margin: 1rem 0 1.2rem;
  }

  .status-bubble {
    display: inline-flex;
    align-items: center;
    gap: 0.55rem;
    padding: 0.65rem 0.9rem;
    border-radius: 999px;
    font-weight: 600;
    letter-spacing: 0.01em;
    box-shadow: var(--shadow-sm);
    border: 1px solid transparent;
    transition: transform var(--transition), box-shadow var(--transition);
  }

  .status-bubble .dot {
    width: 10px;
    height: 10px;
    border-radius: 999px;
    display: inline-block;
  }

  .status-bubble.turn.x {
    background: rgba(37,99,235,0.10);
    color: #1e40af;
    border-color: rgba(37,99,235,0.25);
  }
  .status-bubble.turn.x .dot { background: var(--color-primary); }

  .status-bubble.turn.o {
    background: rgba(245,158,11,0.12);
    color: #8a5100;
    border-color: rgba(245,158,11,0.3);
  }
  .status-bubble.turn.o .dot { background: var(--color-secondary); }

  .status-bubble.win {
    background: linear-gradient(180deg, rgba(37,99,235,0.12), rgba(245,158,11,0.10));
    color: #0b2b70;
    border-color: rgba(37,99,235,0.25);
  }
  .status-bubble.win .dot {
    background: linear-gradient(180deg, var(--color-primary), var(--color-secondary));
  }

  .status-bubble.tie {
    background: rgba(17,24,39,0.06);
    color: rgba(17,24,39,0.85);
    border-color: rgba(17,24,39,0.12);
  }
  .status-bubble:hover {
    transform: translateY(-1px);
    box-shadow: var(--shadow-md);
  }

  .board {
    position: relative;
    width: 100%;
    max-width: 420px;
    aspect-ratio: 1 / 1;
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    margin: 0.25rem auto 1.1rem;
    padding: 12px;
    border-radius: var(--radius-lg);
    background: linear-gradient(180deg, rgba(255,255,255,0.65), rgba(255,255,255,0.9));
    box-shadow: inset 0 1px 0 rgba(255,255,255,0.7), var(--shadow-md);
    border: 1px solid rgba(17,24,39,0.06);
  }

  .cell {
    position: relative;
    border-radius: 14px;
    background: linear-gradient(180deg, rgba(255,255,255,0.9), rgba(249,250,251,1));
    border: 1px solid rgba(17,24,39,0.08);
    box-shadow: var(--shadow-sm);
    transition: transform var(--transition-fast), box-shadow var(--transition), border-color var(--transition), background var(--transition);
    display: grid;
    place-items: center;
    font-size: clamp(2.5rem, 8vw, 3.6rem);
    font-weight: 800;
    color: var(--color-text);
  }

  .cell:not(.filled):hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-lg);
    border-color: rgba(37,99,235,0.35);
    background: linear-gradient(180deg, rgba(237,242,255,0.6), rgba(249,250,251,1));
  }

  .cell:disabled {
    cursor: not-allowed;
    opacity: 0.95;
  }

  .cell.filled .mark.x {
    color: var(--color-primary);
    text-shadow: 0 8px 24px rgba(37,99,235,0.25);
  }

  .cell.filled .mark.o {
    color: var(--color-secondary);
    text-shadow: 0 8px 24px rgba(245,158,11,0.2);
  }

  .cell.win {
    outline: 3px solid rgba(37,99,235,0.45);
    box-shadow: 0 0 0 4px rgba(37,99,235,0.12), var(--shadow-lg);
    transform: translateY(-2px);
  }

  .cell.last:not(.win) {
    box-shadow: 0 0 0 3px rgba(245,158,11,0.28), var(--shadow-md);
  }

  .mark {
    transform: translateY(1px);
    transition: transform var(--transition-fast);
  }

  .cell.filled .mark {
    transform: translateY(0);
  }

  .actions {
    display: flex;
    align-items: center;
    gap: 0.7rem;
    justify-content: center;
    margin-top: 0.25rem;
  }

  .hint {
    color: rgba(17,24,39,0.6);
    font-size: 0.9rem;
  }

  .footnote {
    text-align: center;
    color: rgba(17,24,39,0.6);
    font-size: 0.9rem;
    margin-top: 1rem;
  }

  @media (max-width: 480px) {
    .game {
      padding: 1rem;
    }
    .board {
      gap: 8px;
      padding: 10px;
    }
    .actions {
      flex-wrap: wrap;
    }
  }
</style>
