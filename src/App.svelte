<script>
  import { onMount } from 'svelte';
  import Grid from './Grid.svelte';
  import './AppStyles.css'; 

  let words = [];
  let currentWord = '';
  let currentGuess = '';
  let guesses = [];
  let gridKey = 0; // key to force re-render of Grid
  const maxGuesses = 6;
  const wordLength = 5;

  onMount(async () => {
    const res = await fetch('/words.txt');
    const text = await res.text();
    words = text.split('\n').map(word => word.trim().replace('\r', '')).filter(Boolean);
    currentWord = selectRandomWord();
    console.log("Chosen word:", currentWord);
  });

  function selectRandomWord() {
    return words[Math.floor(Math.random() * words.length)];
  }

  const keyboardRows = [
    ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
    ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L'],
    ['Enter', 'Z', 'X', 'C', 'V', 'B', 'N', 'M', 'Backspace']
  ];

  function handleKeyClick(key) {
    if (key === 'Enter') {
      submitGuess();
    } else if (key === 'Backspace') {
      currentGuess = currentGuess.slice(0, -1);
    } else if (currentGuess.length < wordLength && key.length === 1) {
      currentGuess += key;
    }
  }

  function submitGuess() {
    if (guesses.length >= maxGuesses || currentGuess.length !== wordLength) return;

    const guess = currentGuess.toLowerCase();
    if (!words.includes(guess)) {
      alert('Not a valid word');
      return;
    }

    guesses = [...guesses, currentGuess.toUpperCase()];
    currentGuess = '';
    gridKey++; // Increment key to force re-render

    // Check for win/loss
    if (currentWord.toLowerCase() === guess) {
      setTimeout(() => alert('Congratulations, you won!'), 500);
    } else if (guesses.length === maxGuesses) {
      setTimeout(() => alert(`Sorry, you lost. The word was ${currentWord}`), 500);
    }
  }

  function getCellColor(cellId) {
    const row = Math.floor(cellId / wordLength);
    const col = cellId % wordLength;
    const letter = guesses[row]?.[col];

    if (letter === undefined) return '';

    if (letter === currentWord[col]) {
      return 'green';
    }

    if (currentWord.includes(letter)) {
      let letterCountInWord = currentWord.split('').filter(l => l === letter).length;
      let letterCountInGuess = guesses[row].slice(0, col + 1).filter(l => l === letter).length;

      if (letterCountInGuess <= letterCountInWord) {
        return 'yellow';
      }
    }

    return 'lightgrey';
  }





</script>


<div class="app-container">
  <header>
    <h1>Wordly</h1>
    <hr />
  </header>
  <p>
    {guesses.length < maxGuesses 
      ? `Guess ${guesses.length + 1} of ${maxGuesses}` 
      : 'Game Over'}
  </p>

  <Grid {guesses} {currentGuess} {getCellColor} />
  
  <div class="keyboard">
    {#each keyboardRows as row}
      <div class="keyboard-row">
        {#each row as key}
          <button class="key" on:click={() => handleKeyClick(key)}>
            {key}
          </button>
        {/each}
      </div>
    {/each}
  </div>
</div>

  