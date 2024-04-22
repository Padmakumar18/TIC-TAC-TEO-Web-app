<script lang="ts">
    import '../global.css';
    import toast, { Toaster } from 'svelte-french-toast';
  
    let player_1 : string = "";
    let player_2 : string = "";
    $: isDisabled = false ;
    let show : string = "none" ;
    let checkCount = 0 ;
    // let chance : string = "X" ;
  
    let valueArray = [["-", "-", "-"],
                      ["-", "-", "-"],
                      ["-", "-", "-"]] ;
  
    let char : string = "X";
  
    function submit() : void
    {
      if (player_1 === "" || player_2 === "") 
      {
        toast.error("Enter both player names!") ;
      } 
      else 
      {
        if( player_1 === player_2 )
        {
          toast.error("Name must be unique")
        }
        else if( player_1.length > 10 || player_2.length > 10 )
        {
          toast.error("Name size should be < 10 characters !") ;
        }
        else
        {
          toast.success("Start the match...") ;
          isDisabled = true;
          show = "" ;
        }        
      }
    }
  
    function changeValue( num : number ) : void
    {
      if( valueArray[Math.floor(num / 10)][num % 10] === "X" || valueArray[Math.floor(num / 10)][num % 10] === "O")
      {
        toast.error("Already selected...!")
        checkCount -- ;
      }
      else if( valueArray[Math.floor(num / 10)][num % 10] === "-" ) 
      {
        valueArray[Math.floor(num / 10)][num % 10] = char ;
        char = char === "X" ? "O" : "X" ;
        // chance = char === "X" ? "two" : "one" ;
        // let button : HTMLElement | null = document.getElementById(`${chance}`);
        // if( button )
        // {
        //   if(chance === "X")
        //   {
        //     button.style.border = '3px solid red' ;
        //     button = document.getElementById('one');
        //     if(button)
        //     button.style.border = '3px solid white' ;
        //   }
        //   else
        //   {
        //     button.style.border = '3px solid red' ;
        //     button = document.getElementById('two');
        //     if(button)
        //     button.style.border = '3px solid white' ;
        //   }
        // }
      }
    }
  
    function Clear( name : string = "" ) : void
    {
      if(name !== "")
      {
        toast.success(`${name} Wins....`)
      }
        player_1 = "" ;
        player_2 = "" ;
        isDisabled = false ;
        char = "X" ;
        checkCount = 0 ;
        setTimeout(()=>
        {
        valueArray = [["-", "-", "-"],
                      ["-", "-", "-"],
                      ["-", "-", "-"]] ;
        show  = "none" ;
        },2000) ;
    }
  
    function whoWins() : void
    {
      checkCount ++ ;
      for(let i=0;i<3;i++)
      {
        for(let j=0;j<3;j++)
        {
            let count1 = 0 ;
            let count2 = 0 ;
            for(let k=0;k<3;k++)
            {
                if( valueArray[i][j] === valueArray[j][k] && valueArray[i][j] !== '-' && valueArray[j][k] !== '-' )
                {
                    count1 ++ ;
                }
                if( valueArray[j][i] === valueArray[k][j] && valueArray[j][i] !== '-' && valueArray[k][j] !== '-' )
                {
                    count2 ++ ;
                }
            }
            if( count1 === 3 || count2 === 3 )
            {
                Clear( char === "X" ? player_2 : player_1 ) ;
                break ;
            }
        }
      }
  
      let diagonal1 : number = 0 ;
      let diagonal2 : number = 0 ;
  
      for( let i=1;i<3;i++ )
      {
        for( let j=0;j<3;j++ )
        {
          if( i === j && valueArray[0][0] === valueArray[i][j] && valueArray[0][0] !== '-' && valueArray[i][j] !== '-' )
          {
            diagonal1 ++ ;
          }
          if( i + j === 2 && valueArray[0][2] === valueArray[i][j] && valueArray[0][2] !== '-' && valueArray[i][j] !== '-' ) 
          {
            diagonal2 ++ ;
          }
        }
        if( diagonal1 === 2 || diagonal2 === 2 )
        {
          Clear(char === "X" ? player_2 : player_1) ;
        }
      }
  
      if( checkCount === 9 )
      {
        toast.success( "Draw the match !" ) ;
        Clear() ;
      }
    }
  
    function callFunction( value : number ) : void
    {
      changeValue( value ) ;
      whoWins() ;
    }

    // $:if( char )
    // {
    //   try = char === "X" ? player_2 : player_1 ;
    // }
  </script>
  
  <main>
    <Toaster/>
    <div class="Flex">
      <div class="container p-4 bg-white mt-3 mb-4 rounded">
        <div class="row">
          <form>
            <div class="form-group my-2">
              <label>Player 1 : <input class="form-control bg-dark" type="text" bind:value={player_1}  disabled={isDisabled}/></label>
            </div>
  
            <div class="form-group my-2">
              <label>Player 2 : <input class="form-control bg-dark" type="text" bind:value={player_2}  disabled={isDisabled}/></label>
            </div>
  
            <button type="button" class="button text-white btn btn-info mt-2 p-1" on:click={submit} disabled={isDisabled}>Submit</button>
          </form>
        </div>
      </div>
    </div>

    <!-- <div class="box" style="display: {show};">
      <div>
        <div id="one" class="players one">
        {player_1}
        </div>

        <div id="two" class="players two">
          {player_2}
          </div>
      </div>
    </div> -->

  
    <div class="box" style="display: {show};">
      <div class="boxContainer p-4 bg-white mt-3 mb-2 rounded">
        <p>Tic-Tac-Toe</p>
        <hr>
        {#each valueArray as row, rowIndex}
          <div>
            {#each row as cell, colIndex}
              <button on:click={()=>callFunction( rowIndex * 10 + colIndex )} class="input" >{cell}</button>
            {/each}
          </div>
        {/each}
      </div>
    </div>
  </main>

  <!-- .one
    {
      margin-right: 10px;
    }
    .players
    {
      background-color: white;
      color: black;
      display: inline-block;
      width: 5em;
      text-align: center;
    }
   -->
  <style>
    
    .boxContainer 
    {
      background-color: rgba(255, 255, 255, 0.2) !important;
      color: white;
    }
    .box 
    {
      display: flex;
      justify-content: center;
      align-items: center;
    }
    p 
    {
      color: white;
      font-family: Verdana, Geneva, Tahoma, sans-serif;
      letter-spacing: 1px;
      font-size: 21px;
    }
    input 
    {
      color: white;
    }
    .box button 
    {
      width: 50px !important;
      height: 50px !important;
      flex-shrink: 0;
    }
    .container 
    {
      margin-top: 30px !important;
      background-color: rgba(255, 255, 255, 0.2) !important;
      color: white;
      width: auto;
    }
    .Flex 
    {
      display: flex;
      justify-content: center;
      align-items: center;
    }
  </style>
  