<script>
// @ts-nocheck

    import { createEventDispatcher } from 'svelte'
    import Confetti from 'svelte-confetti'
    import { pass } from './storage.js'

    const dispatch = createEventDispatcher()
    let currentQuestion = 1;

    const NOTES = ["A", "A#", "B", "C", "C#", "D", "D#", "E", "F", "F#", "G", "G#"]
    const M_AND_M = ["Major", "Minor"]

    const MAJOR_SCALE_FORMULA = "WWHWWWH"
    const MINOR_SCALE_FORMULA = "WHWWHWW"

    const TYPES_OF_CHORDS = ["", "SUS 2", "SUS 4", "7", "ADD 9", "9"]

    let currentScaleAns = "";
    let currentChordAns = "";
    let userAns;

    const makeScale = (which_scale_to_play, scale_m_or_m) => {
        let ans = [which_scale_to_play]
        let current_note = NOTES.indexOf(which_scale_to_play)
        if (scale_m_or_m == "Major"){
            for (let step of MAJOR_SCALE_FORMULA){
                if (step == "W"){
                    current_note += 2
                }
                else{
                    current_note += 1
                }
                current_note %= NOTES.length
                ans.push(NOTES[current_note])
            }
        }
        else{
            for (let step of MINOR_SCALE_FORMULA){
                if (step == "W"){
                    current_note += 2
                }
                else{
                    current_note += 1 
                }
                current_note %= NOTES.length
                ans.push(NOTES[current_note])
            }
        }
        return ans.join(" ")
    }

    const getRandomScale = () => {
        let which_scale_to_play  = NOTES[Math.floor(Math.random()*NOTES.length)]
        let scale_m_or_m = M_AND_M[Math.floor(Math.random()*M_AND_M.length)]
        let ans = makeScale(which_scale_to_play, scale_m_or_m)
        return [which_scale_to_play, scale_m_or_m, ans]
    }

    const makeQuestion1 = () => {
        let randScale = getRandomScale();
        return `Play the ${randScale[0]} ${randScale[1]} scale.`
    }

    const nextQuestion = () => {
        currentQuestion += 1
    }

    const makeQuestion2 = () => {
        let randScale = getRandomScale();
        currentScaleAns = randScale[2]
        return `What are the notes in the ${randScale[0]} ${randScale[1]} scale?`
    }

    const makeQuestion3 = () => {
        let randChord = getRandomChord();
        return `Play the ${randChord[0]} ${randChord[1]} ${randChord[2]} chord.`
    }

    const makeQuestion4 = () => {
        let randChord = getRandomChord();
        currentChordAns = randChord[3]
        return `What are the notes in the ${randChord[0]} ${randChord[1]} ${randChord[2]} chord?`
    }

    const checkScaleAns = () => {
        if (userAns.toUpperCase() === currentScaleAns){
            alert("Correct!")
        }
        else{
            alert("Incorrect! You will be redirected to the main page.")
            // dispatch an event to the parent component
            dispatch("failed")
        }
    }

    const checkChordAns = () => {
        if (userAns.toUpperCase() === currentChordAns){
            alert("Correct!")
        }
        else{
            alert("Incorrect! You will be redirected to the main page.")
            // dispatch an event to the parent component
            dispatch("failed")
        }
    }

    const proceedAfterSubmissionScale = () => {
        checkScaleAns()
        nextQuestion()
    }

    const proceedAfterSubmissionChord = () => {
        checkChordAns()
        nextQuestion()
    }

    const makeChord = (scale, which_chord_to_play, type_of_chord) => {
        let formula = ""
        let splitScale = scale.split(" ")
        // remove the last element
        splitScale.pop()
        if(type_of_chord === ""){
            formula = "135"
        }else if (type_of_chord === "SUS 2"){
            formula = "125"
        }else if (type_of_chord === "SUS 4"){
            formula = "145"
        }else if (type_of_chord === "7"){
            formula = "1357"
        }else if (type_of_chord === "ADD 9"){
            formula = "1359"
        }else if (type_of_chord === "9"){
            formula = "13579"
        }
        let ans = []
        for (let step of formula){
            let intStep = (parseInt(step)-1)
            ans.push(splitScale[intStep%splitScale.length])
        }
        return ans.join(" ")
    }

    const getRandomChord = () => {
        let which_chord_to_play = NOTES[Math.floor(Math.random()*NOTES.length)]
        let scale_m_or_m = M_AND_M[Math.floor(Math.random()*M_AND_M.length)]
        let type_of_chord = TYPES_OF_CHORDS[Math.floor(Math.random()*TYPES_OF_CHORDS.length)]
        let scale = makeScale(which_chord_to_play, scale_m_or_m);
        let ans = makeChord(scale, which_chord_to_play, type_of_chord)

        return [which_chord_to_play, scale_m_or_m, type_of_chord, ans]
    }

    const returnToHome = () => {
        $pass = true
        dispatch("failed")
    }
</script>

<style>
    #mainQuestion{
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    #mainQuestionHead{
        font-size: xx-large;
    }

    #mainQuestionBody{
        font-size: large;
    }

    button{
        font-size: large;
        border-radius: 10px;
        background-color: rgb(0, 255, 170);
    }

    button:hover{
        opacity: 0.7;
    }

    button:active{
        background-color: rgb(0, 126, 84);
    }

    #textIn{
        font-size: large;
        margin-bottom: 10px;
    }
</style>

<div>
    <div id='mainQuestion'>
        {#if currentQuestion === 1}
            <h1 id='mainQuestionHead'>Question #{currentQuestion}</h1>
            <h3 id='mainQuestionBody'>{makeQuestion1()}</h3>
            <h3>Press this button to continue, once you have played this(This part is reliant on your integrity!)</h3>
            <button id='nextQuestionBtn' on:click={nextQuestion}>Next question</button>
        {:else if currentQuestion === 2}
            <h1 id='mainQuestionHead'>Question #{currentQuestion}</h1>
            <h3 id='mainQuestionBody'>{makeQuestion2()}</h3>
            <input id="textIn" type="text" bind:value={userAns}>
            <button on:click={proceedAfterSubmissionScale}>Submit</button>
        {:else if currentQuestion === 3}
            <h1 id='mainQuestionHead'>Question #{currentQuestion}</h1>
            <h3 id='mainQuestionBody'>{makeQuestion3()}</h3>
            <h3>Press this button to continue, once you have played this(This part is reliant on your integrity!)</h3>
            <button id='nextQuestionBtn' on:click={nextQuestion}>Next question</button>
        {:else if currentQuestion === 4}
            <h1 id='mainQuestionHead'>Question #{currentQuestion}</h1>
            <h3 id='mainQuestionBody'>{makeQuestion4()}</h3>
            <input id="textIn" type="text" bind:value={userAns}>
            <button on:click={proceedAfterSubmissionChord}>Submit</button>
        {:else if currentQuestion === 5}
            <h1 id='mainQuestionHead'>Question #{currentQuestion}</h1>
            <h3 id='mainQuestionBody'>Play a song</h3>
            <h3>Press this button to continue, once you have played this(This part is reliant on your integrity!)</h3>
            <button id='nextQuestionBtn' on:click={nextQuestion}>Next question</button>
        {:else}
            <h1 id='mainQuestionHead'>You have reached the end of the test!</h1>
            <h2 id='mainQuestionBody'>Congratulations!</h2>
            <h2 id='mainQuestionBody'>Click on the following button to return to home!</h2>
            <button on:click={returnToHome}>&lt;- Return to home</button>
            <!--I just copied this from the svelte confetti site, I know it shows confetti all over the screen, and have no idea how it does that-->
            <div style="
            position: fixed;
            top: -50px;
            left: 0;
            height: 100vh;
            width: 100vw;
            display: flex;
            justify-content: center;
            overflow: hidden;
            pointer-events: none;">
            <Confetti x={[-5, 5]} y={[0, 0.1]} delay={[500, 2000]} infinite duration=5000 amount=200 fallDistance="100vh" />
            </div>
        {/if}
    </div>

</div>
