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
      <input type="text" v-model="message"/>
      <button v-on:click="add(message)">ADD</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Chatbot',
  data () {
    return {
      displays: [],
      message: 'Hello'
    }
  },
  methods: {
    add(message) {
      this.displays.push({
        message: message,
        who: 'person'
      })
      this.displays.push({
        message: 'Réponse',
        who: 'bot'
      })
    }
  }
}
</script>
