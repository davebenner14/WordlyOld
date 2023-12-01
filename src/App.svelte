<script>
  import { onMount } from 'svelte';
  import Grid from './Grid.svelte';
  import './AppStyles.css'; 

  let words = [];
  let currentWord = '';
  let currentGuess = '';
  let guesses = [];
  const maxGuesses = 6;
  const wordLength = 5;

  // Fetch words and select a random word on component mount
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
    console.log("Clicked key:", key);

    if (key === 'Enter') {
      submitGuess();
    } else if (key === 'Backspace') {
      currentGuess = currentGuess.slice(0, -1);
    } else if (currentGuess.length < wordLength && key.length === 1) {
      currentGuess += key;
    }

    console.log("Current guess:", currentGuess);
  }

  function submitGuess() {
    if (guesses.length >= maxGuesses || currentGuess.length !== wordLength) return;

    const guess = currentGuess.toLowerCase();
    console.log("Submitting guess:", guess);

    if (!words.includes(guess)) {
      alert('Not a valid word');
      return;
    }

    // Analyzing and logging each letter in the guess
    guess.split('').forEach((letter, index) => {
      if (letter === currentWord[index]) {
        console.log(`Letter '${letter}' is in the correct position`);
      } else if (currentWord.includes(letter)) {
        console.log(`Letter '${letter}' is in the word but in the wrong position`);
      } else {
        console.log(`Letter '${letter}' is not in the word`);
      }
    });

    guesses = [...guesses, currentGuess.toUpperCase()];
    currentGuess = '';

    // Check for winning or losing condition
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

  if (!letter) return '';

  // Letter is in the correct position
  if (letter === currentWord[col]) {
    return 'green';
  } 
  // Letter is in the word but in the wrong position
  else if (currentWord.includes(letter)) {
    // Check if the letter appears in a correct position for a different instance
    for (let i = 0; i < wordLength; i++) {
      if (currentWord[i] === letter) {
        // If this instance of the letter is already correctly guessed, continue checking other instances
        if (guesses[row]?.[i] === letter) {
          continue;
        }
        // If a different instance of the letter is not correctly guessed, color it yellow
        return 'yellow';
      }
    }
    // If all instances of the letter are correctly guessed, color it light grey
    return 'lightgrey';
  } 
  // Letter is not in the word
  else {
    return 'lightgrey';
  }
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

  