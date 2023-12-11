<script>
    import { createEventDispatcher } from 'svelte';
  
    const dispatcher = createEventDispatcher();
  
    // Define the QWERTY keyboard layout
    const keyboardLayout = [
      ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P'],
      ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', '⏎'], // Move "⏎" to the left
      ['⌫', 'Z', 'X', 'C', 'V', 'B', 'N', 'M'], // Move "⌫" to the right
    ];
  
    // Function to handle key press
    function handleKeyPress(key) {
      dispatcher('keyPress', key);
    }
  
    // Function to handle backspace key press
    function handleBackspace() {
      dispatcher('keyPress', '⌫'); // You can use the actual Unicode character for backspace
    }
  
    // Function to handle enter key press
    function handleEnter() {
      dispatcher('keyPress', '⏎'); // You can use the actual Unicode character for enter
    }
  </script>
  
  <div class="keyboard">
    {#each keyboardLayout as row, rowIndex}
      <div class="keyboard-row" class:bottom-row={rowIndex === 2}>
        {#each row as key}
          <button on:click={() => handleKeyPress(key)}>{key}</button>
        {/each}
      </div>
    {/each}
  </div>
  
  <style>
    /* Add styles for the on-screen keyboard here */
  
    .keyboard {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }
  
    .keyboard-row {
      display: flex;
      justify-content: center;
      gap: 10px;
    }
  
    .keyboard-row.bottom-row {
      margin-bottom: 10px;
    }
  
    button {
      width: 40px;
      height: 40px;
      font-size: 18px;
      border: 1px solid #ccc;
      border-radius: 4px;
      background-color: #fff;
      cursor: pointer;
    }
  
    button.special {
      /* Style special buttons (backspace and enter) */
      width: 60px;
      font-size: 24px;
    }
  
    .empty {
      /* Style empty buttons to align enter button */
      width: 40px;
    }
  
    button:hover {
      background-color: #f0f0f0;
    }
  </style>
  