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
                :class="`chatbot__message_${item.type}`"
            >
              <transition-group
                  appear
                  @enter="enter"
                  @after-enter="afterEnter"
                  :css="false"
              >
                <component
                    :is="item.component"
                    :data="item"
                        :key="item.text"
                    @click="renderMessage"
                    @back="fillGreeting"
                    @mounted="disableButtons"
                ></component>
              </transition-group>
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
  data() {
    return {
      messages: [],
      greeting: [
        {
          component: 'ChatMessage',
          type: 'incoming',
          text: 'Приветствую! Что я могу сделать для Вас?',
        },
        {
          component: 'ChatMessage',
          type: 'incoming',
          text: 'Я могу заказать такси, узнать какая сейчас погода или даже напугать. Что бы вы хотели?',
        },
      ],
      buttons: [
        {
          component: 'ButtonOption',
          answer: 'Хорошо, сейчас я закажу Вам самое быстрое такси. Что-нибудь еще?',
          text: 'Закажи такси',
          type: 'button',
          id: 0,
        },
        {
          component: 'ButtonOption',
          answer: 'У нас всегда солнечно! Хотите еще что-нибудь?',
          text: 'Узнай погоду',
          type: 'button',
          id: 1,
        },
        {
          component: 'ButtonOption',
          answer: 'Последний человек на Земле сидел в комнате. В дверь постучались… Ну как? Может, что-нибудь другое?',
          text: 'Расскажи мне страшилку',
          type: 'button',
          id: 2,
        },
        {
          component: 'ButtonOption',
          text: 'Назад',
          type: 'button',
          id: 3,
        },
      ],
      time: 0,
    }
  },
  mounted: function () {
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
        if (button.id !== buttonObj.id) {
          this.messages.push(button)
        }
      })
    },
    disableButtons() {
      const buttonOptions = document.querySelectorAll('.button-option');
      buttonOptions.forEach(button => {
        button.disabled = true;
      })
    },
    enter(el, done) {
      let coef = 1;
      setTimeout(() => {
        let interval = setInterval(() => {
          if (el.style.opacity >= 1) {
            clearInterval(interval);
            done();
          }
          coef++;
          el.style.opacity = 0.1 * coef;
        }, 50)
      }, this.time * 1000);
      this.time++
    },
    afterEnter(el) {
      const messageItems = document.querySelectorAll('.chatbot__message');
      if (el.parentElement.parentElement === messageItems[messageItems.length - 1]) {
        const buttonOptions = document.querySelectorAll('.button-option');
        let timeout = setTimeout(() => {
          buttonOptions.forEach(button => {
            button.disabled = false;
            this.time = 0;
          })
        }, 20)
      }
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
  padding: 0;
  border: none;
  font: inherit;
  color: inherit;
  background-color: transparent;
  cursor: pointer;
}

.chatbot {
  width: 100%;
  max-width: 370px;
  max-height: 70vh;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  bottom: 150px;
  position: fixed;
  right: 80px;
  z-index: 9999;
}

.chatbot__inner {
  width: 100%;
  height: 100%;
  max-height: calc(70vh - 70px);
  margin-bottom: 20px;
  border-radius: 16px;
  overflow: hidden;
  border: 2px solid #dc0c53;
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
  height: 100%;
  max-height: calc(70vh - 150px);
  padding: 24px;
  overflow-y: auto;
}

.chatbot__message-track {
  display: flex;
  flex-direction: column;
  gap: 15px;
  align-items: flex-start;
}

.chatbot__message {
  padding: 15px;
  border: 2px solid;
  border-radius: 10px;
  background-color: #fff;
  position: relative;
  /* opacity: 0; */
}

.chatbot__message_incoming {
  border-color: #dc0c53;
}

.chatbot__message_outcoming {
  border-color: #aeb8cd;
  align-self: flex-end;
}

.chatbot__message_incoming::before,
.chatbot__message_outcoming::before {
  content: '';
  display: block;
  position: absolute;
  bottom: -3px;
  width: 22px;
  height: 14px;
  z-index: -1;
}

.chatbot__message_incoming::before {
  left: -10px;
  background: url('./assets/images/message-corner.svg') 0 0/contain no-repeat;
  transform: rotate(7deg);
}

.chatbot__message_outcoming::before {
  right: -10px;
  background: url('./assets/images/message-corner_grey.svg') 0 0/contain no-repeat;
  transform: scaleX(-1) rotate(7deg);
}

.chatbot__message_button {
  border: none;
  padding: 0;
  height: 35px;
  display: flex;
  overflow: hidden;
}
</style>
