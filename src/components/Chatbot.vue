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
        <div class="suggestion" v-for="{title} in suggestions" :key="title">
          <button v-on:click="selectSuggestion(title)">{{title}}</button>
        </div>
      </div>
      <input type="text" v-model="message" placeholder="..." @keyup.enter="send(message)"/>
      <button v-on:click="send(message)">Envoyer</button>
    </div>
  </div>
</template>

<script>
import * as Dialogflow from "../dialogflow";
export default {
  name: "Chatbot",
  data() {
    return {
      displays: [],
      suggestions: [],
      message: ""
    };
  },
  methods: {
    addBotMessage(message) {
      this.displays.push({
        message: message,
        who: "bot"
      });
    },
    addSuggestions(suggestions) {
      this.suggestions = suggestions;
    },
    selectSuggestion(message) {
      this.suggestions = [];
      this.send(message);
    },
    displayResponse(res) {
      switch (res.type) {
        case "simple_response":
          this.addBotMessage(res.textToSpeech);
          break;
        case "suggestion_chips":
          this.addSuggestions(res.suggestions);
          break;
        case 0:
          this.addBotMessage(res.speech);
          break;
      }
    },
    askChatbot(req) {
      Dialogflow.askChatbot(req).then(responses => {
        responses.forEach(res => {
          this.displayResponse(res);
        });
      });
    },
    askChatbotEvent(req) {
      Dialogflow.requestEventChatbot(req).then(displayResponse);
    },
    send(message) {
      this.displays.push({
        message: message,
        who: "person"
      });
      this.askChatbot(message);
      this.message = "";
    }
  }
};
</script>
