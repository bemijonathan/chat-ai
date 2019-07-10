<template>
  <div class="wizard-panel columns">
    <div
      style="position: fixed;
      display:none;
    width: 100%;
    height: 100%;
    z-index: 5;
    background: rgba(102, 111, 117, 0.64);top:0;right:0"
      :style="{'display:block' : !hide }"
    >
      <h1 style="color:white; font-size:40px; margin-top:100px">Thank you</h1>
    </div>
    <div class="column is-two-thirds panel chat-panel" :style="{'display:none': hide}">
      <div class="panel-body chat-holder" ref="panel" v-if="name != '' ">
        <div
          v-for="(question, index) in nancyQuestions"
          :key="index"
          :class="{'imageafter' : ( index === 7 )|| (index === 8)}"
        >
          <div>
            <div class="chat-bubble nancy-ai">
              <img
                src="https://cdn-images-1.medium.com/max/1200/1*8mpWApzQD5gZZlnYYUkbcA.png"
                v-if="counter >= index "
                width="20"
              >
              <div style="width:100%;">
                <div class="chat" v-if="counter >= index ">{{question.question}}</div>
                <div class="imagequestion" v-if="counter >= index && question.questionimage">
                  <img :src="question.questionimage">
                </div>
                <div class="chat" v-if="counter >= index  && question.meta" v-html="question.meta"></div>
              </div>
            </div>
          </div>

          <div class="chat-bubble customer">
            <div class="chat" v-if="counter >= index && question.answer">
              <p v-if="typeof(question.answer) === 'string' ">{{question.answer}}</p>
              <li v-else v-for="(ans, index) in question.answer" :key="index">{{ans}}</li>
            </div>
            <div
              class="upload-img"
              @click="$refs.file.click()"
              v-if="question.imageLoaded && showUploader"
            >
              <img :src="imageUrl" v-if="imageLoaded">
              <span v-else>UPLOAD</span>
            </div>
          </div>
        </div>

        <div class="typing-indicator" v-if="typing">
          <span></span>
          <span></span>
          <span></span>
        </div>
      </div>

      <div class="chat-box" :class="{'extraHeight':(counter === 5)}">
        <form @submit.prevent="submit()" validate v-if="name != ''">
          <div v-if=" counter === 5 " class="nancy-benefits-holder">
            <span
              class="button nancy-option-button"
              @click="activeButton('a')"
              :class="{ nacyoption : ('a' === activeOption) }"
            >First Reason</span>
            <span
              class="button nancy-option-button"
              @click="activeButton('b')"
              :class="{ nacyoption : ('b' === activeOption) }"
            >Second Reason</span>
            <span
              class="button nancy-option-button"
              @click="activeButton('c')"
              :class="{ nacyoption : ('c') === activeOption}"
            >Third Reason</span>
          </div>

          <div v-if="counter === 5">
            <input
              type="text"
              class="input shown"
              :class=" {'a' : ('a' === activeOption)}"
              v-model="a"
              @keyup.enter.prevent
              ref="a"
              autofocus
              placeholder="1st reason"
            >
            <input
              type="text"
              class="input shown"
              :class=" {'b' : ('b' === activeOption)}"
              v-model="b"
              @keyup.enter.prevent
              ref="b"
              autofocus
              placeholder="2nd reason"
            >
            <input
              type="text"
              class="input shown"
              :class=" {'c' : ('c' === activeOption)}"
              v-model="c"
              @keyup.enter.prevent
              ref="c"
              autofocus
              placeholder="3rd reason"
            >
          </div>

          <input
            class="input"
            type="text"
            v-if="nancyQuestions[counter].option.length === 0 && counter < 4"
            v-model="answer"
          >
          
          <input
            class="input"
            type="text"
            v-if="nancyQuestions[counter].option.length === 0 && counter >= 6 "
            v-model="answer"
          >
          <input
            type="file"
            style="display: none"
            @change="onImageSelected($event)"
            accept=".png, .jpg, .jpeg"
            ref="file"
          >
          <div class="nancy-form-options" v-if="nancyQuestions[counter].option.length >= 1">
            <span v-for="(option, index) in nancyQuestions[counter].option" :key="index">
              <label
                :class="{ nacyoption : (option == answer) }"
                class="button nancy-option-button"
                @change="submit"
              >
                <input type="radio" :value="option" v-model="answer">
                {{option}}
              </label>
            </span>
          </div>

          <button class="nancy-send" v-if="showSendButton">
            <img src="https://image.flaticon.com/icons/svg/60/60525.svg" alt>
            SEND
          </button>
        </form>
      </div>
    </div>

    <div class="column wizard-progress">
      <ul>
        <li :class="{'bolder' : counter === 1}">
          <span>Niche:</span>
          <br>
          {{nancyQuestions[1].answer}}
        </li>
        <li :class="{'bolder' : counter === 2}">
          <span>Company Name:</span>
          <br>
          {{nancyQuestions[2].answer}}
        </li>
        <li :class="{'bolder' : counter === 3}">
          <span>Logo:</span>
          <br>
          {{nancyQuestions[3].answer}}
        </li>
        <li :class="{'bolder' : counter === 5} ">
          <span>Benefits:</span>
          <div v-if="answeredAll">
            <p v-for="(answer, i) in nancyQuestions[5].answer" :key="i">{{answer}}</p>
          </div>
        </li>
        <li :class="{'bolder' : counter === 6}">
          <span>Tag Line</span>
          <br>
          <p v-if="counter > 6">{{nancyQuestions[6].answer}}</p>
        </li>
        <li :class="{'bolder' : counter === 7}">
          <span>Value Proposition</span>
          <br>
          <p v-if="counter >= 7">{{nancyQuestions[7].answer}}</p>
        </li>
        <li :class="{'bolder' : counter === 8}">
          <span>Elevator Pitch</span>
          <br>
          <p v-if="counter >= 8">{{nancyQuestions[8].answer}}</p>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      hide: false,
      title: "Wizard",
      niche: "Marketing",
      companyName: "Luna Base",
      logo: "Brand",
      name: "jonathan",
      tagLine: "Selling is life and we know it",
      valueProposition: "Marketing",
      nancyQuestions: [
        {
          question:
            "Welcome .  I am FastBot. Let me walk you trough the process. We will create your business website and brand assets in under ⏱️3 minutes!",
          option: ["YES, Lets Do This!"]
        },
        {
          question: "What Nice is Your Business ?",
          option: ["Marketing", "SEO", "eCommerce", "Email Marketing"]
        },
        {
          question: "What Name would you like to call your company?",
          option: []
        },
        {
          question: "Do you have a logo",
          option: ["YES, I do", "No, I dont Create a Logo for me!"]
        },
        {
          question: "What are 3 benefits to using your product or service",
          option: []
        }
      ],
      valuePropositionImage: "",
      pitch: "",
      options: ["Marketing", "SEO", "eCommerce", "Email Marketing"],
      question2: "",
      counter: 0,
      done: false,
      answer: "",
      imageUrl: "",
      imageFile: "",
      imageLoaded: false,
      typing: false,
      showUploader: false,
      activeOption: "",
      a: "",
      b: "",
      c: "",
      answeredAll: false,
      questionimages: [
        "http://pre.innovation.pitt.edu/wp-content/uploads/2016/06/VP_graphic.png",
        "https://c8.alamy.com/comp/R9A6K7/word-writing-text-develop-your-value-proposition-business-concept-for-prepare-marketing-strategy-sales-pitch-messenger-room-with-chat-heads-speech-bu-R9A6K7.jpg"
      ]
    };
  },

  head() {
    return {
      title: this.title,
      meta: [
        // hid is used as unique identifier. Do not use `vmid` for it as it will not work
        {
          hid: "WizardPage",
          name: "WizardPage",
          content: "Nancy.ai WWizard"
        }
      ]
    };
  },

  methods: {
    check() {},
    initialize() {
      this.nancyQuestions[0].question = `Welcome . ${
        this.name
      } I am FastBot. Let me walk you trough the process. We will create your business website and brand assets in under ⏱️3 minutes!`;
    },
    activeButton(i) {
      this.activeOption = i;
      console.dir(this.$refs[i]);
    },
    scrollToEnd() {
      let container = this.$refs.panel;
      var scrollHeight = container.scrollHeight;
      container.scrollTop = scrollHeight;
    },
    hideFooter(payload) {},

    submit() {
      this.nancyQuestions[this.counter].answer = this.answer;
      if (this.counter <= this.nancyQuestions.length) {
        if (this.counter === 5) {
          let a = [];

          this.answeredAll = true;
          if (this.a !== "" && this.b !== "" && this.c !== "") {
            this.nancyQuestions[5].answer = a.concat(this.a, this.b, this.c);
            this.typing = true;
            var next = this;
            setTimeout(() => {
              this.typing = false;
              this.userAnswer();
            }, 2000);
          }
        } else {
          if (this.answer !== "") {
            this.typing = true;
            this.userAnswer();
          }
        }
      }
    },

    userAnswer() {
      this.answer = "";
      var show = false;
      if (this.counter == 1) {
        this.nancyQuestions[2].question = `What Name would you like to call your ${
          this.nancyQuestions[1].answer
        } company?`;
      }
      if (this.counter === 5) {
        var a = {
          question: "what is your tagline",
          option: []
        };
        this.nancyQuestions.push(a);
        setTimeout(() => {
          this.typing = false;
          this.counter++;
        }, 1500);
      } else if (this.counter === 6) {
        var a = {
          question: "Value Proposition",
          option: [],
          questionimage: this.questionimages[0],
          meta: `Type in your <b> Main value proposition </b>`
        };
        this.nancyQuestions.push(a);
        setTimeout(() => {
          this.typing = false;
          this.counter++;
        }, 1500);
      } else if (this.counter === 7) {
        var a = {
          question:
            "Awesome what is your elevator pitch? please explain what you offer who should buy it and why they should buy from you. All in 30 seconds",
          option: [],
          questionimage: this.questionimages[1],
          meta: `Type in your <b>Elevator Pitch</b> `
        };
        this.nancyQuestions.push(a);
        setTimeout(() => {
          this.typing = false;
          this.counter++;
        }, 1500);
      } else if (this.counter === 8) {
        setTimeout(() => {
          this.typing = false;
        }, 1500);
        this.done = true;
      }
      if (this.done && this.counter === 8) {
        let payload = this.nancyQuestions;
        setTimeout(() => {
          this.hide = true;
        }, 2000);
      }

      if (this.counter <= 4 || this.counter > 5) {
        if (this.counter <= 3) {
          if (this.nancyQuestions[3].answer === "YES, I do") {
            this.nancyQuestions[4].question =
              "Great, Click the bock below to upload your logo. ensure it of at lease 800x800 in deimension, and also on a transparent backgroung";
            this.nancyQuestions[4].image = "";
            show = true;
          } else if (
            this.nancyQuestions[3].answer === "No, I dont Create a Logo for me!"
          ) {
            this.nancyQuestions[4].question = "Great";
            this.nancyQuestions[4].image = null;
            show = false;
          }
        }

        setTimeout(() => {
          if (this.counter < 5) {
            this.counter++;
          }

          this.typing = false;
          if (this.counter === 4 && show) {
            this.showUploader = true;
          } else if (this.counter === 4 && show === false) {
            this.userAnswer();
            this.setQuestion5();
          }
          this.nancyQuestions[4].imageLoaded = true;
        }, 1500);
      }
    },

    onImageSelected(e) {
      console.log(e);
      this.imageFile = event.target.files[0];
      let reader = new FileReader();
      this.userAnswer();
      if (event.target.files[0]) {
        this.imageLoaded = true;
        reader.readAsDataURL(event.target.files[0]); // read file as data url
        reader.onload = event => {
          // called once readAsDataURL is completed
          this.imageUrl = event.target.result;
          this.nancyQuestions[4].image = event.target.result;
          console.log(this.counter);
        };
      }

      this.setQuestion5();
    },
    setQuestion5() {
      this.nancyQuestions[5] = {
        question: "What are 3 benefits to using your product or service",
        option: []
      };
    }
  },

  created() {
    this.hideFooter(false);
    // this.name = prompt('what is your name')
  },
  mounted() {
    // this.scrollToEnd()
    this.initialize();
  },
  updated() {
    this.scrollToEnd();
  },
  computed: {
    showSendButton() {
      if (
        this.counter === 2 ||
        this.counter === 5 ||
        this.counter === 6 ||
        this.counter === 7 ||
        this.counter === 8
      ) {
        return true;
      } else {
        return false;
      }
    }
  }
};
</script>

<style>
.imageafter .imagequestion {
  height: 300px;
  width: 100%;
  margin-bottom: 20px;
  overflow: hidden;
}
.bolder {
  opacity: 1 !important;
}
.imagequestion img {
  object-fit: cover;
}
@media screen and (max-width: 700px) {
  .imageafter .imagequestion {
    height: 200px;
    width: 100%;
  }
}

@media screen and (max-width: 500px) {
  .chat-bubble.nancy-ai {
    display: block;
    align-items: flex-end;
  }
  .imagequestion img {
    height: 100% !important;
  }
}
</style>
