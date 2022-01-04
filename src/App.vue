<template>
  <div id="app">
    <img
      src="@/assets/arrow.png"
      class="arrow-left"
      v-if="agenda.currentFace > 0"
      @click="prevFace()"
    />
    <div v-if="agenda.currentFace < 1" class="front-cover">
      <div class="border"></div>
      <div class="face">
        <div class="header">
          <h1>Agenda</h1>
        </div>
      </div>
    </div>
    <div v-else-if="agenda.currentFace <= agenda.maxFaces" class="agenda">
      <div
        class="face"
        v-for="(page, index) in agenda.currentPages"
        :key="index"
      >
        <input
          type="text"
          spellcheck="false"
          v-model="page.header"
          placeholder="Titolo"
          maxlength="40"
          @input="syncToLocalStorage()"
        />
        <textarea
          spellcheck="false"
          v-model="page.body"
          placeholder="Contenuto..."
          @input="syncToLocalStorage()"
        ></textarea>
        <small>
          {{ page.index }}
        </small>
      </div>
    </div>
    <div v-else class="back-cover">
      <div class="border"></div>
      <div class="face">
        <div class="pie">
          <small> Pagine totali: {{ agenda.pages.length }} </small>
        </div>
      </div>
    </div>
    <img
      src="@/assets/arrow.png"
      class="arrow-right"
      v-if="agenda.currentFace < agenda.maxFaces + 1"
      @click="nextFace()"
    />
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => {
    return {
      agenda: {
        currentFace: 0,
        maxFaces: 10,
        pages: [],
        currentPages: [],
      },
    };
  },
  mounted() {
    this.initAgenda();
    this.loadCurrentPages();
  },
  methods: {
    syncToLocalStorage() {
      localStorage.agenda = JSON.stringify(this.agenda);
    },
    createPages() {
      for (
        let i = this.agenda.currentFace * 2;
        i >= this.agenda.currentFace * 2 - 1;
        i--
      ) {
        this.agenda.pages.push({
          index: i,
          header: "",
          body: "",
        });
      }
    },
    initAgenda() {
      if (localStorage.agenda) this.agenda = JSON.parse(localStorage.agenda);
    },
    loadCurrentPages() {
      // console.log("svuoto currentPages[]");
      this.agenda.currentPages = [];
      for (
        let i = this.agenda.currentFace * 2;
        i >= this.agenda.currentFace * 2 - 1;
        i--
      ) {
        // console.log(i);
        // console.log("aggiungo in currentPages " + this.agenda.pages[i]);
        this.agenda.currentPages.push(this.agenda.pages[i - 1]);
      }
      // this.agenda.currentPages = this.agenda.currentPages.slice().reverse();
    },
    nextFace() {
      this.agenda.currentFace++;
      if (
        this.agenda.currentFace <= this.agenda.maxFaces &&
        this.agenda.pages.length < this.agenda.currentFace * 2
      )
        this.createPages();
      this.loadCurrentPages();
      this.syncToLocalStorage();
    },
    prevFace() {
      this.agenda.currentFace--;
      this.loadCurrentPages();
      this.syncToLocalStorage();
    },
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #bca183;
  .arrow-left,
  .arrow-right {
    position: relative;
    width: 4em;
    cursor: pointer;
    transition: transform 0.4s;
    animation-name: floating;
    animation-duration: 1s;
    animation-iteration-count: infinite;
    animation-timing-function: linear;
  }
  .arrow-left {
    animation-direction: alternate-reverse;
    &:hover {
      transform: scale(1.4);
    }
    &:active {
      transition: none;
      transform: scale(1);
    }
  }
  .arrow-right {
    transform: rotate(180deg);
    animation-direction: alternate;
    &:hover {
      transform: rotate(180deg) scale(1.4);
    }
    &:active {
      transition: none;
      transform: rotate(180deg) scale(1);
    }
  }
  .front-cover,
  .agenda,
  .back-cover {
    border-radius: 0.8em;
    border: 0.2em solid black;
    height: calc(100% / 1.1);
    background-color: #785046;
    background-image: url("~@/assets/ls.png");
    background-repeat: repeat;
    padding: 1em;
    display: flex;
    .face {
      overflow: hidden;
      display: flex;
      flex-direction: column;
      align-items: center;
      flex-grow: 1;
      border: 0.2em solid black;
      border-radius: 0.8em;
      padding: 0.5em;
    }
  }
  .front-cover,
  .back-cover {
    width: calc(40%);
    position: relative;
    .face {
      border: none;
      .header,
      .body {
        width: 100%;
      }
      font-family: "Times New Roman", Times, serif;
      color: hsl(43, 44%, 49%);
      .header {
        text-align: center;
        h1 {
          font-size: 2.4em;
        }
      }
    }
  }
  .front-cover {
    border-radius: 0.2em 0.8em 0.8em 0.2em;
    .border {
      position: absolute;
      // background-color: white;
      top: 50%;
      transform: translateY(-50%);
      left: -0.4em;
      border-left: 0.2em dashed black;
      border-right: 0.2em dashed black;
      height: 99%;
      padding: 0 0.1em;
    }
  }
  .back-cover {
    border-radius: 0.8em 0.2em 0.2em 0.8em;
    .border {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: -0.4em;
      border-left: 0.2em dashed black;
      border-right: 0.2em dashed black;
      height: 99%;
      padding: 0 0.1em;
    }
    .face {
      align-items: flex-start;
      justify-content: flex-end;
      .pie {
        font-style: italic;
        font-size: 1.1em;
        // font-weight: 500;
      }
    }
  }
  .agenda {
    width: calc(80%);
    .face {
      background-color: #efedde;
      input,
      textarea {
        font-family: monospace;
        background-color: transparent;
        width: 100%;
        border: none;
        resize: none;
        &::placeholder {
          opacity: 0.1;
        }
        &:focus {
          outline: none;
          &::placeholder {
            opacity: 0;
          }
        }
      }
      input {
        text-align: center;
        font-size: 1.4em;
        font-weight: 700;
        padding-bottom: 2em;
      }
      textarea {
        flex-grow: 1;
        font-size: 1.2em;
      }
      small {
        position: relative;
        bottom: -0.5em;
        display: block;
      }
    }
  }
}

@keyframes floating {
  0% {
    left: 0;
  }
  100% {
    left: 0.5em;
  }
}
</style>
