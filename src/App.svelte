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
    words = text.split('\n').filter(Boolean);
    currentWord = selectRandomWord();
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
  } else if (currentGuess.length < wordLength) {
    currentGuess += key;
  }
}


  function handleInput(event) {
    if (currentGuess.length < wordLength && /^[A-Za-z]$/.test(event.key)) {
      currentGuess += event.key.toUpperCase();
    }
    if (event.key === 'Enter' && currentGuess.length === wordLength) {
      submitGuess();
    }
    if (event.key === 'Backspace') {
      currentGuess = currentGuess.slice(0, -1);
    }
  }

  function submitGuess() {
    guesses.push(currentGuess);
    currentGuess = '';
  }

  function getCellColor(row, index) {
    const letter = guesses[row]?.[index];
    if (!letter) return '';
    if (letter === currentWord[index]) return 'green';
    if (currentWord.includes(letter)) return 'yellow';
    return 'grey';
  }
</script>

<svelte:window on:keydown={handleInput} />

<div class="app-container">
	<header>
	  <h1>Wordly</h1>
	  <hr />
	</header>
  
	<div class="grid">
	  {#each Array(maxGuesses) as _, row}
		<div class="row">
		  {#each Array(wordLength) as _, col}
			<div class="cell {getCellColor(row, col)}">
			  {guesses[row]?.[col] || ''}
			</div>
		  {/each}
		</div>
	  {/each}
	</div>
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
  