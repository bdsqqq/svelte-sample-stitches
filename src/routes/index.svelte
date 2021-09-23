<script>
  import { createBoardWithJustNumbers, singleToMultiDimentionalArray } from "$lib/utils";
  import Cell from "../components/Cell.svelte" ;
  import Draggable from '../components/Draggable.svelte';


  let dimSize = 10;
  let bombNum = 10;

  let alreadyDug = []

  const board = createBoardWithJustNumbers(dimSize, bombNum);
  let tempArray = new Array(dimSize * dimSize).fill(false);
  let isThisCellRevealed = singleToMultiDimentionalArray(tempArray, dimSize)


  const handleReveal = (row, col, value) => {
    alreadyDug = [...alreadyDug, `${row}-${col}`];
    isThisCellRevealed[row][col] = true;
    isThisCellRevealed = isThisCellRevealed;

    switch (value) {
      case 0: //this was an empty cell
        for (let r = -1; r <= 1; r++) {
          for (let c = -1; c <= 1; c++) {
            let checking_row = row + r;
            let checking_col = col + c;
            if (
              // we don't need to check the cell we passed
              !(checking_row == row && checking_col == col)
            ) {
              if (
                // checking_row and checking_col are inside the board
                checking_row >= 0 &&
                checking_row <= board.length - 1 &&
                checking_col >= 0 &&
                checking_col <= board.length - 1
              ) {
                if (
                  !alreadyDug.find(
                    (element) => element == `${checking_row}-${checking_col}`
                  )
                ) {
                  console.log(
                    `Running handleReveal on ${checking_row}-${checking_col}`
                  );
                  handleReveal(
                    checking_row,
                    checking_col,
                    board[checking_row][checking_col]
                  );
                }
              }
            }
          }
        }

        break;

      case 9: //this was a bomb
        console.log(`dang, ${row}-${col} was a bomb`);
        break;
    }
  }

  import { css } from '../../stitches.config';
  const gameContainer = css({
    position: "relative",
    overflow: "hidden",
    minHeight: "100vh",

    display: "flex",
    justifyContent: "center",
    alignItems: "center",

    backgroundColor: "$background",

    color: "$text",
  })

  const styledRow = css({
  display: "flex",
  gap: "2px",

  /* Add bottom border for all boxes except the last row */
  "&:not(:last-child) > *::after": {
    zIndex: "0",
    content: "",
    position: "absolute",
    bottom: "-2px",
    width: "50%",
    left: "25%",
    height: "2px",
    backgroundColor: "$border",
  },

  /* Add right border for all indexed boxes except last one */
  "& > *:not(:last-child):before": {
    zIndex: "0",
    content: "",
    position: "absolute",
    right: "-2px",
    height: "50%",
    top: "25%",
    width: "2px",
    backgroundColor: "$border",
  },
});

</script>

<main class={gameContainer()}>
  <!-- For each entry in the board, make a cell-->
  <Draggable>
      <div style="display: flex; flex-direction: column;">
        {#each board as row, i}
          <div class={styledRow()}>
            {#each row as cell, j}
              <Cell value={cell} revealed={isThisCellRevealed[i][j]} handleReveal={() => {handleReveal(i, j, cell)}} />
            {/each}
          </div>
        {/each}
      </div>
  </Draggable>
</main>