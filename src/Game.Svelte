<script>
    import { gameState } from './store.js';
    import WordGrid from './WordGrid.svelte';
    import Keyboard from './Keyboard.svelte';
  
    let currentGuess = '';
  
    function handleKeyPress(key) {
      // Add your logic for handling key press here
    }
  
    // Add more game logic as needed
  </script>
  
  <WordGrid {currentGuess} />
  <Keyboard on:keyPress={handleKeyPress} />
  