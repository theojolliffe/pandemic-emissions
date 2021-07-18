<script>
  import { onMount, createEventDispatcher } from 'svelte';
  import Snackbar from './Snackbar.svelte';
  import ENVDIALS from './ENVDIALS.svelte';


  import { fadeIn, fadeOut } from './transitions.js';
  import { htmlDecode, shuffle } from './utils.js'; 

  import questions from './questions.json';  

  const dispatch = createEventDispatcher();
  let data = [];

  let questionNo = 0;
  let buttonBarVisibility = true;
  let snackbarVisibility = false;
  let snackbarMessage = false;
  let resultsScreen = false;

  $: representation = [];
  $: score = 0;
  $: finalMessage = '';

  function repro() {
    return representation
  };

  function buildData() {
      data = questions.results  
      representation = data.reduce((acc, curr) => {
          if (curr.type == "fieldEntry") {
              acc.push({
              question: htmlDecode(curr.question),
              type: curr.type,
              id: htmlDecode(curr.id),
              answerChoice: '',
              answered: false
              });
          } else if (curr.type == "options") {
              acc.push({
              question: htmlDecode(curr.question),
              type: htmlDecode(curr.type),
              id: htmlDecode(curr.id),
              answerChoices:[...curr.options].map(ans => htmlDecode(ans)),
              answerChoice: '',
              answered: false
              });
          } else if (curr.type == "boolean") {
              acc.push({
              question: htmlDecode(curr.question),
              type: htmlDecode(curr.type),
              id: htmlDecode(curr.id),
              subQuestion: htmlDecode(curr.subQuestion),
              subOptions:[...curr.subOptions].map(ans => htmlDecode(ans)),
              answerChoices:["Yes", "No"],
              answerChoice: '',
              subAnswerChoice: '',
              answered: false
              });
          } else if (curr.incorrect_answers) {
              acc.push({
              question: htmlDecode(curr.question),
              type: htmlDecode(curr.type),
              id: htmlDecode(curr.id),
              answerChoices: shuffle(
                  [...curr.incorrect_answers, curr.correct_answer].map(ans =>
                  htmlDecode(ans)
                  )
              ),
              answer: htmlDecode(curr.correct_answer),
              answerChoice: '',
              answered: false
              });
          } 
          return acc;
      }, []);
  }

  onMount(buildData);

  function handleClick(change) {
    if (change === 'f') questionNo += 1;
    else questionNo -= 1;
  }

  function handleAnswerChoiceNum(e = {}, members) {
    console.log("members,", members)
      const representationCopy = { ...representation[questionNo] };
      representationCopy.answered = true;
      representationCopy.answerChoice = members;
      representation[questionNo] = representationCopy;
  }

  function handleAnswerChoiceOpt(e = {}) {
      const representationCopy = { ...representation[questionNo] };
      representationCopy.answered = true;
      representationCopy.answerChoice = e.target.innerText;
      representation[questionNo] = representationCopy;
  }

  function handleAnswerChoiceBool(e = {}) {
      const representationCopy = { ...representation[questionNo] };
      representationCopy.answered = true;
      representationCopy.answerChoice = e.target.innerText;
      representation[questionNo] = representationCopy;
  }

  function handleAnswerChoiceSubBool(e = {}, oilCoal) {
      const representationCopy = { ...representation[questionNo] };
      representationCopy.answered = true;
      representationCopy.subAnswerChoice = oilCoal;
      representation[questionNo] = representationCopy;
  }

  let members = '';
  let oilCoal = {};
</script>


{#if questionNo < 4}

  <div id="main" in:fadeIn out:fadeOut>

    {#if representation.length > 0 && !resultsScreen}
      <span id="heading">
        Question {questionNo + 1}
      </span>
      <span>{representation[questionNo].question}</span>

        {#if representation[questionNo].type == "fieldEntry"}

            <input type=number bind:value={members} min=0 max=10 on:click={e => handleAnswerChoiceNum(e, members)}>

        {/if}

        {#if representation[questionNo].type == "options"}
            {#each representation[questionNo].answerChoices as choice}
                {#if representation[questionNo].answerChoice == choice}
                    <div
                    id="choice"
                    style="background: #7DDF64; color: white; border-color: white">
                    <i>{choice}</i>
                    </div>
                {:else}
                    <div id="choice" on:click={e => handleAnswerChoiceOpt(e)}>
                        <i>{choice}</i>
                    </div>
                {/if}
            {/each}
        {/if}

        {#if representation[questionNo].type == "boolean"}

            {#if representation[questionNo].answerChoice == "Yes"}

                <span>{representation[questionNo].subQuestion}</span>
                
                <span>{representation[questionNo].subOptions[0]}</span>
                <input type=number bind:value={oilCoal["Oil"]} min=0 max=10>                    
                <span>{representation[questionNo].subOptions[1]}</span>
                <input type=number bind:value={oilCoal["Coal"]} min=0 max=10>                    
                <span>{representation[questionNo].subOptions[2]}</span>
                <input type=number bind:value={oilCoal["Wood"]} min=0 max=10>                    
                <span>{representation[questionNo].subOptions[3]}</span>
                <input type=number bind:value={oilCoal["Gas"]} min=0 max=10>
        
                <button value={oilCoal} on:click={e => handleAnswerChoiceSubBool(e, oilCoal)}>Update</button>

            {:else}

                {#each representation[questionNo].answerChoices as choice}
                    {#if representation[questionNo].answerChoice == choice}
                        <div
                        id="choice"
                        style="background: #7DDF64; color: white; border-color: white">
                        <i>{choice}</i>
                        </div>
                    {:else}
                        <div id="choice" on:click={e => handleAnswerChoiceBool(e)}>
                            <i>{choice}</i>
                        </div>
                    {/if}
                {/each}

            {/if}

        {/if}


      {#if buttonBarVisibility}
        <div id="button-bar">
          {#if questionNo < 9}
            <button value="Next" on:click={() => handleClick('f')}>Next</button>
          {/if}
          {#if questionNo > 0}
            <button value="Back" on:click={() => handleClick('b')}>
              Previous
            </button>
          {/if}
        </div>
      {/if}

      {#if snackbarVisibility}
        <div id="snackbar" in:fadeIn out:fadeOut>
          <Snackbar message={snackbarMessage} />
        </div>
      {/if}
      <!-- Uncomment this if you want the user to go back and answer pending questions
        {:else if answeredQuestions === 10}
        <p>All answered</p>
      -->
    {:else if resultsScreen}
      <div id="results">
        <p id="score">
          Final Score:
          <i>{score} / 10</i>
        </p>
        <p style="font-size: 24px">
          {@html finalMessage}
        </p>
        <p style="font-size: 24px">Refresh the page to play again</p>
      </div>
    {:else}
      <span
        style="position: absolute; left: 50%; top: 50%; transform:
        translateX(-50%) translateY(-50%); font-weight: bolder; font-size: 36px;
        margin: 0">
        Fetching questions...
      </span>
    {/if}

    
  </div>

{:else}

  <ENVDIALS reps={repro()}></ENVDIALS>       

{/if}


     



<style>
  #main {
    position: relative;
    height: 500px;
    width: 700px;
    margin: auto;  
    background-color: white;
    border-radius: 6px;
    box-shadow: 0 0 5px white;

    text-align: left;
  }

  span {
    display: block;
    margin-top: 20px;
  }

  button {
    margin-top: 15px;
    margin-right: 15px;
    padding: 10px;
    float: right;

    color: white;
    background-color: #16302b;
    border: none;
    border-radius: 10px;
    cursor: pointer;
  }

  button:hover {
    box-shadow: 0 0 5px #16302b;
  }

  #heading {
    font-size: 24px;
    font-weight: bolder;
  }

  #button-bar {
    position: absolute;
    bottom: -66px;
    right: 0;
  }

  #choice {
    margin-top: 16px;
    padding: 8px;

    border: 1px solid #4e5656;
    border-radius: 8px;
  }

  #choice:hover {
    cursor: pointer;
    background: #7ddf64;
    border: 1px solid #7ddf64;
    color: white;
  }

  #snackbar {
    position: absolute;
    left: 16px;
    bottom: 24px;
  }

  #results {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translateX(-50%) translateY(-50%);

    text-align: center;
  }

  #score {
    font-size: 48px;
  }

  @media screen and (max-width: 960px) {
    #main {
      width: calc(100vw - 15%);
      height: calc(100vh - 30%);
    }
    #score {
      font-size: 36px;
    }
  }
</style>