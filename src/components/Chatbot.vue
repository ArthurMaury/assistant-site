<style scoped>
  .messages {
    width: 100%;
  }
  .message {
    display: inline-block;
    margin: 10px;
    padding: 10px;
    border: 1px solid gray;
    background-color: lightgray;
    width: auto;
    border-radius: 4px;
  }
  .bot {
    text-align: left;
  }
  .bot .message {
    background-color: #aaddaa;
    border-color: #88dd88;
    color: #003300;
  }
  .person {
    text-align: right;
  }
  .person .message {
    background-color: #aaaadd;
    border-color: #8888dd;
    color: #000033;
  }
</style>

<template>
  <div>
    <div>
      <div class="messages" 
          v-for="display in displays"
          v-bind:key="display.id">
        <div v-bind:class="{ bot: display.who === 'bot', person: display.who === 'person' }">
          <div class="message">
            {{display.message}}
          </div>
        </div>
      </div>
    </div>
    <div>
      <div class="suggestions">
        <div class="suggestion" v-for="sugg in suggestions" v-bind:key="sugg.title">
          <button v-on:click="setSuggestion('blabla')">{{sugg.title}}</button>
        </div>
      </div>
      <input type="text" v-model="message" placeholder="..."/>
      <button v-on:click="add(message)">ADD</button>
    </div>
  </div>
</template>

<script>
import * as Dialogflow from '../dialogflow'
export default {
  name: 'Chatbot',
  data () {
    return {
      displays: [],
      suggestions: [],
      message: ''
    }
  },
  methods: {
    addBotMessage(message){
      this.displays.push({
          message: message,
          who: 'bot'
        })
    },
    addSuggestions(suggestions){
      this.suggestions = suggestions
    },
    setSuggestion(message){
      console.log(message)
      this.suggestions = []
      this.add(message)
    },
    displayResponse(res) {
      switch(res.type){
        case "simple_response":
          this.addBotMessage(res.textToSpeech)
          break;
        case "suggestion_chips":
          this.addSuggestions(res.suggestions)
          break;
        case 0:
          this.addBotMessage(res.speech)
          break;
      }
      
    },
    askChatbot(req) {
      Dialogflow.askChatbot(req)
      .then(responses => {
        responses.forEach(res => {
          this.displayResponse(res)
        });
        
      })
    },
    add(message) {
      this.displays.push({
        message: message,
        who: 'person'
      })
      this.askChatbot(message)
      this.message = ''
    }
  }
}
</script>
