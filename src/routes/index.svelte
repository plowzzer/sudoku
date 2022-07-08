<script>
  let columns = 9
  let lines = 9
	let matrix = new Array(columns) 

	const createMatrix = () => {
    for (var i = 0; i < columns; i++) {
      matrix[i] = new Array(lines)
    }
    
    for (let column = 0; column < 9; column++) {
      for (let line = 0; line < 9; line++) {
				const superposition = [1, 2, 3, 4, 5, 6, 7, 8, 9];
        matrix[column][line] = {
					column,
					line,
					possibilities: superposition,
          checked: false
				};
			}
		}
	};

  const removeElement = (array, element) => {
    const index = array.indexOf(element)
    if(index > -1) {
      return array.splice(index, 1)
    }
  }

  const elementClick = (column, line, possibility) => {
    console.log(matrix[column][line], 'chose:', possibility)
    const block = {
      startColumn: (Math.floor(column / 3) * 3),
      startLine: (Math.floor(line / 3) * 3),
      endColumn: (Math.floor(column / 3) * 3) + 3,
      endLine: (Math.floor(line / 3) * 3) + 3
    }

    // // Check Columns 
    for (let c = 0; c < columns; c++) {
      removeElement(matrix[c][line].possibilities, possibility)
    }
    // // Check Lines 
    for (let l = 0; l < lines; l++) {
      removeElement(matrix[column][l].possibilities, possibility)
    }
    // Check Block
    for (let c = block.startColumn; c < block.endColumn; c++) {
      for (let l = block.startLine; l < block.endLine; l++) {
        removeElement(matrix[c][l].possibilities, possibility)
      }
    }

    matrix[column][line].possibilities = [possibility]
    matrix[column][line].checked = true
  }

  const solve = () => {
    let trys = 1

    
    while (trys <= 100){
      let minEntropyValue = 9
      let minEntropyElements = Array()
      //checking entropy
      for (let column = 0; column < 9; column++) {
        for (let line = 0; line < 9; line++) {
          if (matrix[column][line].checked !== true) {
            if (matrix[column][line].possibilities.length < minEntropyValue) {
              minEntropyElements = Array(matrix[column][line])
              minEntropyValue = matrix[column][line].possibilities.length
            } 
            if (minEntropyElements[0] !== matrix[column][line] && matrix[column][line].possibilities.length === minEntropyValue) {
              minEntropyElements.push(matrix[column][line])
            }
          }
        }
      }

      if (minEntropyElements.length > 0) {
        // Getting random element
        const min = 0
        const max = Math.floor(minEntropyElements.length)
        const randomIndex = Math.floor(Math.random() * (max - min)) + min
        
        const {column, line, possibilities} = minEntropyElements[randomIndex]
        if (possibilities.length > 0) {
          const min = 0
          const max = Math.floor(possibilities.length)
          const randomPossibilityIndex = Math.floor(Math.random() * (max - min)) + min
          elementClick(column, line, possibilities[randomPossibilityIndex])
        } else {
          elementClick(column, line, possibilities[0])
        } 
      } else {
        const {column, line, possibilities} = minEntropyElements[0]
        elementClick(column, line, possibilities[0])  
      }

      trys++
    }
  }

	createMatrix();
</script>

<header>
  <h1>Sudoku</h1>
  <div class="buttons">
    <button on:click={solve}>solve</button>
    <button on:click={createMatrix}>reset</button>
  </div>
</header>

<div class="board">
  {#each matrix as element}
  <ul>
      {#each element as {column, line, possibilities}}
        <li class={`element matrix[${column}][${line}]`}>
          {#each possibilities as possibility}
            <button on:click={() => elementClick(column, line, possibility)}>{possibility}</button>
          {/each}  
        </li>
      {/each}
    </ul>
    {/each}
</div>

<style lang="scss">
  header {
    width: 996px;
    margin: auto;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }
	.board {
		// display: grid;
    width: 650px;
    margin: auto;
    display: flex;
    flex-direction: row;
    ul {
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;

      &:nth-child(3),
      &:nth-child(6) {
        border-right: 2px solid black;
      }
    }
		li {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      font-size: 10px;
      width: 60px;
      height: 50px;
      padding: 2px;
      border: 1px solid rgba(0,0,0,0.1);

      &:nth-child(3),
      &:nth-child(6) {
        border-bottom: 2px solid black;
      }

      button {
        background: transparent;
        border: none;
        cursor: pointer;
      }
		}
	}
</style>
