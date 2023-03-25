<script setup>

import {ref,onMounted} from 'vue'

const transcript=ref('Text will appear here &#128512;')
const isRecording=ref(false)
const buttonText=ref('Start Recording')
const response=ref('')

const Recognition= window.SpeechRecognition || window.webkitSpeechRecognition

const sr=new Recognition()

onMounted(()=>{
  sr.continuous=true
  sr.interimResults=true

  sr.onstart=()=>{
    isRecording.value=true
  }
  sr.onend=()=>{
    isRecording.value=false
  }
  sr.onresult=(event)=>{

    for (let i = 0; i < event.results.length; i++) {
      const result=event.results[i]
      if (result.isFinal) {
        checkForCommand(result)
      }
    }

    const text= Array.from(event.results)
                .map(result => result[0])
                .map(result => result.transcript)
                .join('')

    transcript.value=text
  }

})

const toggleMic=()=>{
  if(isRecording.value){
    sr.stop()
    buttonText.value="Start Recording"
    response.value="Recording stopped"
  }
  else{
    sr.start()
    buttonText.value="<i class='fa-solid fa-record-vinyl fa-beat'></i> Recording..."
    response.value="Recording started"
  }
}

const checkForCommand=(result)=>{
  const text=result[0].transcript
  if(text.includes('stop recording')){
    sr.stop()
    buttonText.value="Start Recording"
    response.value="Recording stopped"
  }
  else if(text.includes('time')){
    response.value="The time is " + new Date().toLocaleTimeString()
  }
  else if(text.includes('date')){
    response.value="The date is " + new Date().toLocaleDateString()
  }
  else if(text.includes('say my name')){
    response.value= "You're Heisenberg!"
  }
  else if(text.includes('how are you')){
    response.value= "I'm fine thank you"
  }
}

</script>

<template>
  <main class="container text-light text-center py-5">
      <button class="btn btn-light mb-4" @click="toggleMic" v-html="buttonText"></button>
      <div class="transcript text-start p-4 rounded" v-html="transcript"></div>
      <div class="response text-start py-5">Response: {{response}}</div>
  </main>
</template>

