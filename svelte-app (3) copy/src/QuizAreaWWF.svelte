<script>
    import { onMount } from 'svelte';
    import questions from './wwfgetQs.json';  
    import WWFQs from './WWFQs.json';  
    import ans from './wwfpostanswers.json';  
    import WWFDIALS from './WWFDIALS.svelte';
    import { fadeIn, fadeOut } from './transitions.js';
    import robojournalist from "robojournalist";
    import roboStrings from './robo-strings/robo-strings.csv';
    import answerLookup from './data/answerLookup.json';
    import stringsRos from './robo-strings/rosae-strings.js';
    import { LineChart } from '@onsvisual/svelte-charts';
    import dataFake from './data';

    // Transform report string csv to object
    let strings = {};
    roboStrings.forEach(d => {strings[d.qNo] = d.template;});

    let data = [];
    let questionNo = 0;
    $: representation = [];
    $: openRep = []
    $: insideRep = [];
    let wwf = WWFQs.results;
    

    function repro() {
        return ans
    };
    
    function buildData() {
        data = questions.sections;
        representation = data.reduce((acc, curr) => {

            let topicQs = curr.questions
            insideRep = topicQs.reduce((accIn, currIn) => {
                accIn.push({
                    question: currIn.question,
                    answers: currIn.answers.map(ob => ob.answer),
                    answerChoice: '',
                    answered: false,
                    section: currIn.s_id,
                    type: currIn.type,
                    opening: currIn.opening,
                })
                return accIn;
            }, []);
            acc.push(insideRep)
            return acc;
        }, []);
        representation = [].concat.apply([], representation);

        openRep = representation.filter( item => {
            return item['opening'] == true
        });
    }
    
    onMount(buildData);
    
    function handleAnswer(e = {}, members) {
        ans.data.Travel.q3.a1 = wwf[6].answers[0].input
        ans.data.Travel.q6.a1 = wwf[9].answers[0].input1
        ans.data.Travel.q6.a2 = wwf[9].answers[0].input2
        ans.data.Home.q4.a1 = wwf[14].answers[0].text
    }

    wwf[6].answers[0].input = 4
    wwf[9].answers[0].input1 = 0
    wwf[9].answers[0].input2 = 0

    let string5 = strings[wwf[5].answers[0].id.slice(0, -1)].split(" {input} ")
    let string11 = strings[wwf[11].answers[0].id.slice(0, -1)].split(" {input} ")
    let string14 = strings[wwf[14].answers[0].id.slice(0, -1)].split(" {input} ")

    let vehicalLU = {"Medium petrol or diesel car": 0.28}

    console.log('wwf', wwf)

    let a = 9.9;
    let share = 0

</script>

<div style="width:60%; margin:auto; text-align: left;">

    <h2>How does working from home impact your carbon footprint?</h2>

    <!--  SECTION 5: COMMUTE METHOD  -->
    <!--  SECTION 5: COMMUTE METHOD  -->
    <!--  SECTION 5: COMMUTE METHOD  -->
    <hr style="margin:40px 100px 40px 100px; color:lightgray">
    <div>
        <span style="float:left; margin-top: 0px;">
            {string5[0]}
        </span>
        <form style="float:left">&nbsp
            <!-- svelte-ignore a11y-no-onchange -->
            <select bind:value={wwf[5].answerChoice} on:change={e => handleAnswer(e, members)}>
                {#each wwf[5].answers as question}
                    <option selected={question.selected} value={question.text.toLowerCase()}>
                        {question.text.toLowerCase()}
                    </option>
                {/each}
            </select>
    &nbsp</form>
        <span style="margin: revert">
            {robojournalist(string5[1], {
                similar: answerLookup[wwf[6].answers[0].id].similar,
                carConv: vehicalLU[wwf[5].answerChoice]
            })}
        </span>
    </div>
    <br>
    <!--  SECTION 6: COMMUTE LENGTH  -->
    <!--  SECTION 6: COMMUTE LENGTH  -->
    <!--  SECTION 6: COMMUTE LENGTH  -->
    <p>The average daily car commute is 9.9 miles each way.</p>
    <p><strong>What is your daily commute?</strong></p>
    <div>
        <input class="slider" type=range step="1" min=0 max=200 bind:value={a}>
    </div>
    <div>
        Commuting <strong>{a} miles</strong>, you emit <strong>{Math.round(2*a*vehicalLU[wwf[5].answerChoice]*10)/10} kg of GHG</strong>, once the return journey is complete.
    </div>

    <!--  SECTION 7: COMMUTE SHARING  -->
    <!--  SECTION 7: COMMUTE SHARING  -->
    <!--  SECTION 7: COMMUTE SHARING  -->
    <div id="span-cont">
        <span style="float:left; margin-top: 0px;">Sharing your journey with, on average</span>
        <form style="float:left">&nbsp
            <input type=number step="0.5" min=0 max=4 bind:value={share}>
    &nbsp</form>
        <span style="margin: revert;">others, you will emit <strong>{(Math.round(240*2*a*vehicalLU[wwf[5].answerChoice]/(1000*(share+1))*10)/10).toLocaleString()} tonnes</strong> of GHG in a typical year of 240 commuting days.</span>
        <br>
        <p>The average person in the UK has an annual carbon footprint of 12.7 tonnes CO2e</p>
    </div>

    <hr style="margin:40px 100px 40px 100px; color:lightgray">

    <div>
        <h3>An average person would save 2 tonnes of CO2 a year if they spent two hours a week less in a car</h3>
        <div class="chart">
            <LineChart data={dataFake} xKey="year" yKey="value" zKey="group" stacked={true} line={false} areaOpacity={1}/>
        </div>
    </div>


    <!--  SECTION 11: KIND OF HOUSE  -->
    <!--  SECTION 11: KIND OF HOUSE  -->
    <!--  SECTION 11: KIND OF HOUSE  -->
    <hr style="margin:40px 100px 40px 100px; color:lightgray">
    <div>
        <span style="float:left; margin-top: 0px;">
            {string11[0]}
        </span>
        <form style="float:left">&nbsp
            <!-- svelte-ignore a11y-no-onchange -->
            <select bind:value={wwf[11].answerChoice} on:change={e => handleAnswer(e, members)}>
                {#each wwf[11].answers as question}
                    <option value={question.text.toLowerCase()}>
                        {question.text.toLowerCase()}
                    </option>
                {/each}
            </select>
    &nbsp</form>
        <span style="margin: revert">
            {robojournalist(string11[1], {
                similar: answerLookup[wwf[6].answers[0].id].similar,
            })}
        </span>
    </div>

    <hr style="margin:40px 100px 40px 100px; color:lightgray">
    
    <p>{wwf[11].answerChoice}</p>
    <p>{wwf[14].answerChoice}</p>

        <div id="cont">
            <p>How many homes have green energy</p>

            <div id="span-cont">

                <span style="float:left; margin-top: 0px;">Living in a</span>

                <form style="float:left">&nbsp
                    <input type=number step="0.5" bind:value={share}>
            &nbsp</form>

                <span style="float:left">bedroom house of</span>

                <form style="float:left">&nbsp
                    <input type=number step="0.5" bind:value={share}>
            &nbsp</form>

                <span style="margin: revert;">residents reduces your household energy consumption by 20%, compared to a house of 3.</span>
            </div>

            <div>
                <input type=range bind:value={a} min=0 max=200>
            </div>

        </div>


    <!-- SECTION 14: HEAT YOUR HOME  -->
    <!-- SECTION 14: HEAT YOUR HOME  -->
    <!-- SECTION 14: HEAT YOUR HOME  -->
    <hr style="margin:40px 100px 40px 100px; color:lightgray">
    <div>
        <span style="float:left; margin-top: 0px;">
            {string14[0]}
        </span>
        <form style="float:left">&nbsp
            <!-- svelte-ignore a11y-no-onchange -->
            <select bind:value={wwf[14].answerChoice} on:change={e => handleAnswer(e, members)}>
                {#each wwf[14].answers as question}
                    <option value={question.text.toLowerCase()}>
                        {question.text.toLowerCase()}
                    </option>
                {/each}
            </select>
    &nbsp</form>
        <span style="margin: revert;">
            {robojournalist(string14[1], {
                choice: wwf[6].answers[0].text,
                similar: answerLookup[wwf[6].answers[0].id].similar,
            })}
        </span>
    </div>
        
    <div>**************</div>

    <p>If 40% of the country worked from home {3} days a week it would have x result on UK carbon emissions </p>

 
</div>

<hr style="margin:40px 100px 40px 100px; color:lightgray">

<style>

.slider {
  -webkit-appearance: none;
  width: 100%;
  height: 15px;
  border-radius: 5px;
  background: #d3d3d3;
  outline: none;
  opacity: 0.7;
  -webkit-transition: .2s;
  transition: opacity .2s;
}

.slider:hover {
  opacity: 1;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 25px;
  height: 25px;
  border-radius: 50%;
  background: #04AA6D;
  cursor: pointer;
}

.slider::-moz-range-thumb {
  width: 25px;
  height: 25px;
  border-radius: 50%;
  background: #04AA6D;
  cursor: pointer;
}
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
    select {
        border-width: inherit;
        border-bottom-width: thin;
    }
    input[type="number"] {
        width: 40px;
        text-align: center;
        padding-left: 2px;
        border-width: inherit;
        border-bottom-width: thin;
    }
    span {
        line-height: 1.8;
    }
    h1, h2, h3, h4, h5 {
        font-size: revert;
        line-height: revert;
    }
    h1, h2, h3, h4, h5 {
    width: 500px;
}    
.chart {
        height: 250px;
        width: 500px;
        margin-right: auto;
        margin-left: auto;
        margin-bottom:  20px;
    }
@media screen and (min-width: 960px) {
    .chart {
        margin-left: revert;
    }
}    
    #cont {
        width: 90%;
        margin-top: 50px;
        margin-left: auto;
        margin-right: auto;
    }
    div {
        margin-top: 50px
    }
    span {
        line-height: 1.8
    }
    #span-cont {
        margin-top: 50px
    }
    input[type="number"] {
        width: 40px;
        text-align: center;
        padding-left: 2px;
        border-width: inherit;
        border-bottom-width: thin;
    }
    hr {
        margin: 40px;
    }
    .profile-grid {
            width:70%;
            grid-gap:20px 5%;
            grid-template-columns:50% 50%;
            justify-content:stretch;
            margin-left:auto;
            margin-right:auto
        }

    @media screen and (min-width: 960px) {
        .profile-grid {
            display:grid;
        }
    }
  </style>