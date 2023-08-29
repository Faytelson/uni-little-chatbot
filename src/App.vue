<template>
  <div id="app">
    <div class="chatbot">
      <div class="chatbot__inner">
        <div class="chatbot__header">
          <div class="chatbot__logo">
            <img src="./assets/images/logo-robot.png" alt="logo" class="chatbot__logo-img">
          </div>
          <div class="chatbot__title">
            <div class="chatbot__title-inner">Uni Little Chatbot</div>
          </div>
          <div class="chatbot__button-close">
            <ButtonClose @emitClose="closeChatbot"></ButtonClose>
          </div>
        </div>
        <div class="chatbot__body">
            <div class="chatbot__message-track">
                <div 
                    v-for="item in messages"
                    class="chatbot__message"
                >
                    <component :is="item.component" :data="item" @click="renderMessage" @back="fillGreeting"></component>
                </div>
            </div>
        </div>
      </div>
      <button class="chatbot__show-button">Show button</button>
    </div>
  </div>
</template>

<script>
import ChatMessage from './components/ChatMessage.vue';
import ButtonOption from './components/ButtonOption.vue';
import ButtonClose from './components/ButtonClose.vue';

export default {
  name: 'app',
  data () {
    return {
      messages: [],
      greeting: [
                {
                    component: 'ChatMessage',
                    type: 'incoming',
                    text: 'This is a first message text'
                },
                {
                    component: 'ChatMessage',
                    type: 'incoming',
                    text: 'This is a second message text'
                },
      ],
      buttons: [
      {
                    component: 'ButtonOption',
                    answer: 'This is first btn answer',
                    text: 'This is a first button',
                    id: 0,
                },
                {
                    component: 'ButtonOption',
                    answer: 'This is first btn answer',
                    text: 'This is a second button',
                    id: 1,
                },
                {
                    component: 'ButtonOption',
                    answer: 'This is first btn answer',
                    text: 'This is a third button',
                    id: 2,
                },
                {
                    component: 'ButtonOption',
                    text: 'This is a back button',
                    id: 3,
                },
      ]
    }
  },
  mounted: function() {
    this.fillGreeting();
  },
  components: {
    ChatMessage,
    ButtonOption,
    ButtonClose,
  },
  methods: {
    fillGreeting() {
      this.messages = [];
      this.messages = [...this.greeting, ...this.buttons.filter(button => button.id !== 3)];
    },
    renderMessage(buttonObj) {
      this.messages = [];
      this.messages.push(
        {
          component: 'ChatMessage',
          type: 'outcoming',
          text: buttonObj.text,
        },
        {
          component: 'ChatMessage',
          type: 'incoming',
          text: buttonObj.answer,
        }
      );
      this.buttons.forEach(button => {
        if(button.id !== buttonObj.id) {
          this.messages.push(button)
        }
      })
    },
    closeChatbot() {
      console.log('close')
    }
  }
}
</script>

<style>
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

button {
  border: none;
}

.chatbot {
  width: 100%;
  max-width: 300px;
  max-height: 60vh;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  bottom: 140px;
  position: fixed;
  right: 80px;
  z-index: 9999;
}
.chatbot__inner {
  width: 100%;
  margin-bottom: 20px;
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid #dc0c53;
}

.chatbot__header {
    width: 100%;
    height: 80px;
    background-color: #dc0c53;
    display: flex;
    justify-content: space-between;
    align-items: stretch;
    padding: 10px;
}

.chatbot__button-close {
  
}

.chatbot__logo {
  height: 100%;
  max-height: 60px;
}

.chatbot__logo-img {
  height: 100%;
  object-fit: contain;
}

.chatbot__title {
  display: flex;
  align-items: center;
}

.chatbot__title-inner {
  font-family: 'Roboto', sans-serif;
  font-size: 18px;
  font-weight: 500;
  color: #fff;
}

.chatbot__body {
  padding: 24px;
}
</style>
