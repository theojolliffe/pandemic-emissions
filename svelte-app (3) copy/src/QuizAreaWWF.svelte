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
    import Line from './Line.svelte';
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
    let room = 2;
    let share = 1;
    let temp = 21;


    let wfhDays = 1
    let deskWattage = 140
    let lighWattage = 10
    let fte = wfhDays/5
    let workingDays = 240
    let workingHours = 8
    let WHpa = workingHours*workingDays
    let officeElec = (deskWattage+lighWattage)*fte*WHpa/1000
    let wfhEnergyUse = 0
</script>

<div style="width:50%; margin:auto; text-align: left;">


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
        <span style="margin: revert;">others, you will personally emit <strong>{(Math.round(240*2*a*vehicalLU[wwf[5].answerChoice]/(1000*(share+1))*10)/10).toLocaleString()} tonnes</strong> of GHG in a typical year of 240 commuting days.</span>
        <p>This is equivilent to a return flight to Singapore and is 8% off the average Brit's annual footprint (12.7 tonnes GHGe)</p>
        <p>You would save 150 kg of GHG a year if you spent two fewer hours in a car each week</p>
    </div>

    <hr style="margin:40px 100px 40px 100px; color:lightgray">

    <!--  *************************  -->
    <!--  *************************  --> 
    <div>
        <h3>Chart comparing GHG you would emit commuting with different vehicles</h3>
        <div class="chart">
            <LineChart data={dataFake} xKey="year" yKey="value" zKey="group" stacked={true} line={false} areaOpacity={1}/>
        </div>
    </div>
    <!--  *************************  -->
    <!--  *************************  --> 


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


    <div id="span-cont">

        <span style="float:left; margin-top: 0px;">Living in a</span>

        <form style="float:left">&nbsp
            <input type=number step="0.5" bind:value={room}>
    &nbsp</form>

        <span style="float:left">bedroom house with</span>

        <form style="float:left">&nbsp
            <input type=number step="0.5" bind:value={share}>
    &nbsp</form>

        <span style="margin: revert;">other, requires, on average, 20% less energy to heat than a three bedroom house.</span>
    </div>
    <div id="span-cont">
        <span></span>
    </div>

    <!-- SECTION 14: HEAT METHOD  -->
    <!-- SECTION 14: HEAT METHOD  -->
    <!-- SECTION 14: HEAT METHOD  -->
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

    <!-- SECTION: HEAT TEMPERATURE  -->
    <!-- SECTION: HEAT TEMPERATURE  -->
    <!-- SECTION: HEAT TEMPERATURE  -->
    <p>The recommended temparture to heat your house during the cold months is 21°C.</p>
    <p><strong>How hot do you keep you house?</strong></p>
    <div>
        <input class="slider" type=range step="1" min=10 max=30 bind:value={temp}>
    </div>
    <div>
        Setting your temperature to <strong>{temp}°C</strong>, you emit <strong>{Math.round(2*temp*vehicalLU[wwf[5].answerChoice]*10)/100} kg of GHG</strong> during an eight hour working winter day.
    </div>

    <hr style="margin:40px 100px 40px 100px; color:lightgray">
 
    <!--  *************************  -->
    <!--  *************************  --> 
    <!-- <div style="height: 400px; width: 600px">
        <Line></Line>
    </div> -->
    <div>
        <h3>Chart comparing the GHG you would save wfh to other lifestyle changes</h3>
        <div class="chart">
            <LineChart data={dataFake} xKey="year" yKey="value" zKey="group" stacked={true} line={false} areaOpacity={1}/>
        </div>
    </div>

    <hr style="margin:40px 100px 40px 100px; color:lightgray">

    <p>In one year working from home 3 days every week you would save 0.6 tonnes of GHG</p>
    <p>If every UK resident reduced their GHG emissions by 0.6 tonnes the UK would be {(0.6*7000000).toLocaleString()} tonnes of GHG closer to net zero.</p>
    <p>Based on the various commutes and energy efficiencies of homes, if all working adults in the UK began working from home on average 2 times per week, the UK would reduce carbon emissions by 1,000 tonnes of GHG.</p>

 
</div>


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
        margin-top: 25px
    }
    span {
        line-height: 1.8
    }
    #span-cont {
        margin-top: 25px
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
    p {
        margin-top: 25px;
    }
  </style>