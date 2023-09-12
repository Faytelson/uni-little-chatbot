<template>
  <div id="app">
    <div class="chatbot">
      <transition name="slide">
      <div
          class="chatbot__inner"
          v-show="showChatbot"
      >
        <div class="chatbot__header">
          <div class="chatbot__logo">
            <img src="./assets/images/logo-robot.png" alt="logo" class="chatbot__logo-img">
          </div>
          <div class="chatbot__title">
            <div class="chatbot__title-inner">Uni Little Chatbot</div>
          </div>
          <div class="chatbot__button-close">
            <ButtonClose @close="closeChatbot"></ButtonClose>
          </div>
        </div>
          <div
              class="chatbot__body"
              ref="chatbotBody"
          >
            <div class="chatbot__message-track">
              <div
                  class="chatbot__text-messages"
                  ref="textMessages"
              ></div>
              <div
                  class="chatbot__buttons"
                  ref="buttons"
              >
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
      </transition>
      <button
          class="chatbot__show-button"
          @click="showChatbot = !showChatbot"
      >
        <img src="./assets/images/logo-robot.png" alt="Uni Chatbot" class="chatbot__show-button-img">
      </button>
    </div>
  </div>
</template>

<script>
import ButtonClose from './components/ButtonClose.vue';

export default {
  name: 'app',
  data() {
    return {
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
      buttonsToShow: [],
      showChatbot: false,
    }
  },
  components: {
    ButtonClose,
  },
  watch: {
    showChatbot: function (value) {
      if(value) {
        this.fillGreeting();
        setTimeout(() => {
          this.scrollBody();
        }, 20)
      }
    }
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
      this.$refs.textMessages.innerHTML = '';
      if(button.id !== 3) {
        this.renderMessage('outcoming', button.text);
        this.renderMessage('incoming', button.answer);
        this.renderButtons(button.id);
      } else {
        this.fillGreeting();
      }
      this.scrollBody();
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
      buttonContainer.style.opacity = '0';
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
      }, (index) * 1000);
    },
    scrollBody() {
      this.$refs.chatbotBody.scrollTo({
        top: 0,
        behavior: "smooth",
      })
    },
    closeChatbot() {
      console.log('click')
      this.showChatbot = false;
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
  min-height: 350px;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  justify-content: flex-end;
  gap: 20px;
  position: fixed;
  bottom: 50px;
  right: 80px;
  z-index: 9999;
}

.chatbot__inner {
  width: 100%;
  height: 100%;
  max-height: calc(70vh - 120px);
  min-height: 230px; /*calc(350px - 120px)*/
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
  max-height: calc(70vh - 200px);
  min-height: 150px; /*(calc(350px - 200px))*/
  padding: 24px;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: lightgray transparent;
  background-color: #fff;
}

.chatbot__body::-webkit-scrollbar {
  width: 6px;
}

.chatbot__body::-webkit-scrollbar-track {
  background: transparent;
}

.chatbot__body::-webkit-scrollbar-thumb {
  background-color: lightgray;
  border-radius: 16px;
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
  max-width: 500px;
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
}

.chatbot__text-message_incoming::before {
  left: -12px;
  background: url('./assets/images/message-corner.svg') 0 0/contain no-repeat;
  transform: rotate(30deg);
}

.chatbot__text-message_outcoming::before {
  right: -12px;
  background: url('./assets/images/message-corner_grey.svg') 0 0/contain no-repeat;
  transform: scaleX(-1) rotate(30deg);
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

.chatbot__show-button {
  width: 100px;
  height: 100px;
  background-color: #dc0c53;
  border-radius: 50%;
}

.chatbot__show-button-img {
  width: 100%;
  max-width: 100%;
  height: auto;
}

.slide-enter {
  opacity: 0;
}

.slide-enter-active {
  animation: slide-in .5s linear;
}

.slide-leave-active {
  animation: slide-out .5s linear;
}

@keyframes slide-in {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

@keyframes slide-out {
  from {
    opacity: 1;
  }

  to {
    opacity: 0;
  }
}

@media screen and (max-width: 767px) {
  .chatbot {
    width: auto;
    max-width: 100%;
    height: auto;
    max-height: 100%;
    min-height: auto;
  }

  .chatbot__inner {
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    max-height: 100%;
    min-height: 100%;
    border-radius: 0;
    overflow: hidden;
    border: 2px solid #dc0c53;
    z-index: 9999;
}

.chatbot__show-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
}

.chatbot__body {
  height: calc(100% - 80px);
  max-height: calc(100% - 80px);
  min-height: calc(100% - 80px);
}
}
</style>
