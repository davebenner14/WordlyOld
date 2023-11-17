<script>
  import { onMount } from 'svelte';

  let words = [];
  let currentWord = '';
  let currentGuess = '';
  let guesses = [];
  const maxGuesses = 6;
  const wordLength = 5;

  // Fetch words and select a random one on component mount
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
  console.log("Clicked key:", key); // Log the clicked key

  if (key === 'Enter') {
    submitGuess();
  } else if (key === 'Backspace') {
    currentGuess = currentGuess.slice(0, -1);
  } else if (currentGuess.length < wordLength && key.length === 1) {
    currentGuess += key;
  }

  console.log("Current guess:", currentGuess); // Log the current guess
}



function submitGuess() {
  if (guesses.length >= maxGuesses || currentGuess.length !== wordLength) return;

  console.log("Submitting guess:", currentGuess.toLowerCase()); // Debugging line

  if (!words.includes(currentGuess.toLowerCase())) {
  alert('Not a valid word');
  return;
}


  guesses = [...guesses, currentGuess.toUpperCase()];
  currentGuess = '';

  // Check for winning or losing condition
  if (currentWord.toLowerCase() === guesses[guesses.length - 1].toLowerCase()) {
    setTimeout(() => alert('Congratulations, you won!'), 500);
  } else if (guesses.length === maxGuesses) {
    setTimeout(() => alert(`Sorry, you lost. The word was ${currentWord}`), 500);
  }
}

function getCellColor(row, index) {
  const letter = guesses[row]?.[index];
  if (!letter) return '';

  // Letter is in the correct position
  if (letter === currentWord[index]) {
    console.log(`Letter: ${letter}, Position: ${index}, Color: green`);
    return 'green';
  }

  // Letter is in the word but in the wrong position
  if (currentWord.includes(letter)) {
    const letterCountInWord = currentWord.split('').filter(l => l === letter).length;
    const letterCountInGuess = guesses[row].slice(0, index + 1).filter(l => l === letter).length;
    const color = letterCountInGuess <= letterCountInWord ? 'yellow' : 'grey';
    console.log(`Letter: ${letter}, Position: ${index}, Color: ${color}`);
    return color;
  }

  // Letter is not in the word at all
  console.log(`Letter: ${letter}, Position: ${index}, Color: grey`);
  return 'grey';
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
  
	  <div class="grid">
		{#each Array(maxGuesses) as _, row}
		  <div class="row">
			{#each Array(wordLength) as _, col}
			  <div class="cell {getCellColor(row, col)}">
				{row === guesses.length && currentGuess.length > col ? currentGuess[col] : guesses[row]?.[col] || ''}
			  </div>
			{/each}
		  </div>
		{/each}
	  </div>
	  
	  

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

  <style>
  .app-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: flex-start; 
    padding-top: 20px; 
    padding-bottom: 20px; 
  }
  
	header {
	  text-align: center;
	  width: 100%;
	  margin-bottom: 40px;
	}
  
	h1 {
	  margin: 0;
	  padding-bottom: 0.5em;
	}
  
	hr {
	  border: none;
	  height: 2px;
	  background-color: #333;
	  margin: 0;
	}
  
	.grid {
	  display: flex;
	  flex-direction: column;
	  align-items: center;
	}
	.row {
	  display: flex;
	}
	.cell {
	  width: 50px;
	  height: 50px;
	  border: 2px solid #333;    
	  display: flex;
	  justify-content: center;
	  align-items: center;
	  font-size: 2em;
	  margin: 5px;
	  color: black;
	}
	.green {
	  background-color: green;
	}
	.yellow {
	  background-color: yellow;
	}
	.grey {
	  background-color: grey;
	}
	.keyboard {
    margin-top: 20px;
  }
  .keyboard-row {
    display: flex;
    justify-content: center;
    margin-bottom: 5px;
  }
  .key {
    background-color: lightgray;
    border: none;
    border-radius: 4px;
    margin: 5px;
    padding: 10px 15px;
    font-size: 1em;
    color: black;
    cursor: pointer;
  }
  .key:active {
    background-color: darkgray;
  }
  </style>
  