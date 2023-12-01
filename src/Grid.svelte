<script>
    export let guesses;
    export let currentGuess;
    export let getCellColor;
  
    const wordLength = 5;
    const maxGuesses = 6;
</script>

<div class="grid">
    {#each Array(maxGuesses) as _, row (row)}
        <div class="row">
            {#each Array(wordLength) as _, col (col)}
                <div class={`cell ${row < guesses.length ? getCellColor(row * wordLength + col) : ''}`}>
                    {row < guesses.length ? guesses[row][col] : ''}
                    {row === guesses.length && col < currentGuess.length ? currentGuess[col] : ''}
                </div>
            {/each}
        </div>
    {/each}
</div>

<style>
    .grid {
        display: flex;
        flex-direction: column;
    }
    .row {
        display: flex;
    }
    .cell {
        width: 50px; /* Example width */
        height: 50px; /* Example height */
        border: 1px solid #ccc; /* Example border */
        display: flex;
        justify-content: center;
        align-items: center;
    }
    /* Define your color classes */
    .green {
        background-color: green;
    }
    .yellow {
        background-color: yellow;
    }
    .lightgrey {
        background-color: lightgrey;
    }
</style>
