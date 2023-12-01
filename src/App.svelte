<script>
  import { onMount } from 'svelte';

  let words = [];
  let currentWord = '';
  let currentGuess = '';
  let guesses = [];
  const maxGuesses = 6;
  const wordLength = 5;
  let testColor = '';


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

  const guess = currentGuess.toLowerCase();
  console.log("Submitting guess:", guess);

  if (!words.includes(guess)) {
    alert('Not a valid word');
    return;
  }

  // Analyzing and logging each letter in the guess
  const analysis = guess.split('').map((letter, index) => {
    if (letter === currentWord[index]) {
      console.log(`Letter '${letter}' is in the correct position`);
      return { letter, status: 'correct position' };
    } else if (currentWord.includes(letter)) {
      console.log(`Letter '${letter}' is in the word but in the wrong position`);
      return { letter, status: 'wrong position' };
    } else {
      console.log(`Letter '${letter}' is not in the word`);
      return { letter, status: 'not in word' };
    }
  });

  console.log("Guess analysis:", analysis);

  guesses = [...guesses, currentGuess.toUpperCase()];
  currentGuess = '';

  // Check for winning or losing condition
  if (currentWord.toLowerCase() === guess) {
    setTimeout(() => alert('Congratulations, you won!'), 500);
  } else if (guesses.length === maxGuesses) {
    setTimeout(() => alert(`Sorry, you lost. The word was ${currentWord}`), 500);
  }
}



function getCellColor(row, index) {
  const letter = guesses[row]?.[index];
  if (!letter) return '';

  let color = '';

  // Correct position
  if (letter === currentWord[index]) {
    color = 'green';
    console.log(`Letter '${letter}' is correct and in the correct position`);
  } else if (currentWord.includes(letter)) {
    // Check if any of the same letters in the guess are in the correct position
    let isAnyInCorrectPosition = false;
    for (let i = 0; i < currentWord.length; i++) {
      if (currentWord[i] === letter && guesses[row][i] === letter) {
        isAnyInCorrectPosition = true;
        break;
      }
    }

    if (!isAnyInCorrectPosition) {
      color = 'yellow';
      console.log(`Letter '${letter}' is in the word but in the wrong position`);
    } else {
      color = 'lightgrey';
    }
  } else {
    color = 'lightgrey';
    console.log(`Letter '${letter}' is not in the word`);
  }

  console.log(`Row: ${row}, Index: ${index}, Letter: '${letter}', Color: ${color}`);
  return color;
}







</script>
<button on:click={() => testColor = 'green'}>Change First Cell Color</button>


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
          <div class={`cell ${getCellColor(row, col)}`}>
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
  .green {
    background-color: green;
  }
  .yellow {
    background-color: yellow;
  }
  .grey {
    background-color: grey;
  }
  .lightgrey {
  background-color: lightgrey;
}

  </style>
  