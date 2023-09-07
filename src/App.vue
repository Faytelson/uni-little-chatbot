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
                class="chatbot__text-messages"
                ref="textMessages"
            ></div>
            <div class="chatbot__buttons">
              <div
                  class="chatbot__button"
                  v-for="button in buttonsToShow"
                  @click="rerenderTrack(button)"
              >
                {{ button.text }}
              </div>
            </div>
          </div>
        </div>
      </div>
      <button class="chatbot__show-button">Show button</button>
    </div>
  </div>
</template>

<script>
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
      buttonsToShow: []
    }
  },
  mounted: function () {
    this.fillGreeting();
  },
  components: {
    ButtonClose,
  },
  methods: {
    fillGreeting() {
      this.$refs.textMessages.innerHTML = '';
      this.renderMessage('incoming', 'Приветствую! Что я могу сделать для Вас?');
      this.renderMessage('incoming', 'Я могу заказать такси, узнать какая сейчас погода или даже напугать. Что бы вы хотели?');
      this.renderButtons(3);
    },
    renderMessage(type, inner) {
      const parent = this.$refs.textMessages;
      let newMessage = document.createElement('div');
      newMessage.className = `chatbot__text-message chatbot__text-message_${type}`;
      newMessage.innerHTML = inner;
      parent.append(newMessage);
      this.initMessageAnimation(newMessage);
    },
    renderButtons(excludedId) {
      this.buttonsToShow =[];
      this.buttons.forEach(button => {
        if(button.id !== excludedId) {
          this.buttonsToShow.push(button);
        }
      })
      this.initButtonAnimation();
    },
    rerenderTrack(button) {
      this.buttonsToShow =[];
      this.$refs.textMessages.innerHTML = '';
      if(button.id !== 3) {
        this.renderMessage('outcoming', button.text);
        this.renderMessage('incoming', button.answer);
        this.renderButtons(button.id);
      } else {
        this.fillGreeting();
        const body = document.querySelector('.chatbot__body');
        body.scrollTo({
          top: 0,
          behavior: "smooth",
        })
      }
    },
    initMessageAnimation(elem) {
      let messagesInTextMessageTrack = document.querySelectorAll('.chatbot__text-message');
      messagesInTextMessageTrack.forEach(message => {
        if(message === elem) {
          this.animateElem(elem, Array.from(messagesInTextMessageTrack).indexOf(elem))
        }
      })
    },
    initButtonAnimation() {
      let buttonContainer = document.querySelector('.chatbot__buttons');
      this.animateElem(buttonContainer, 2);
    },
    animateElem(elem, index) {
      let coef = 1;
      setTimeout(() => {
        let interval = setInterval(() => {
          if (elem.style.opacity >= 1) {
            clearInterval(interval);
          }
          elem.style.opacity = String(0.1 * coef);
          coef++;
        }, 40)
      }, (index + 1) * 1000);
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
  gap: 20px;
  align-items: stretch;
}

.chatbot__text-messages {
  display: flex;
  flex-direction: column;
  gap: 15px;
  align-items: flex-start;
}

.chatbot__text-message {
  font-family: 'Roboto', sans-serif;
  font-size: 18px;
  font-weight: 400;
  line-height: 1.3em;
  padding: 15px;
  border: 2px solid;
  border-radius: 10px;
  background-color: #fff;
  position: relative;
  opacity: 0;
}

.chatbot__text-message_incoming {
  border-color: #dc0c53;
}

.chatbot__text-message_outcoming {
  border-color: #aeb8cd;
  align-self: flex-end;
}

.chatbot__text-message_incoming::before,
.chatbot__text-message_outcoming::before {
  content: '';
  display: block;
  position: absolute;
  bottom: -3px;
  width: 22px;
  height: 14px;
  z-index: -1;
}

.chatbot__text-message_incoming::before {
  left: -10px;
  background: url('./assets/images/message-corner.svg') 0 0/contain no-repeat;
  transform: rotate(7deg);
}

.chatbot__text-message_outcoming::before {
  right: -10px;
  background: url('./assets/images/message-corner_grey.svg') 0 0/contain no-repeat;
  transform: scaleX(-1) rotate(7deg);
}

.chatbot__buttons {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 15px;
  opacity: 0;
}

.chatbot__button {
  font-family: 'Roboto', sans-serif;
  font-size: 16px;
  font-weight: 400;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  background-color: #dc0c53;
  padding: 10px 15px;
  border-radius: 16px;
  cursor: pointer;
}
</style>
