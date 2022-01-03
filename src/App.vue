<template>
  <div id="app">
    <button :disabled="agenda.currentFace <= 1" @click="prevFace()">
      Prev
    </button>
    <div class="agenda">
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
    <button
      :disabled="agenda.currentFace >= agenda.maxFaces"
      @click="nextFace()"
    >
      Next
    </button>
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => {
    return {
      agenda: {
        currentFace: 1,
        maxFaces: 364 / 2,
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
      else this.createPages();
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
      if (this.agenda.pages.length < this.agenda.currentFace * 2)
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
  button {
    border: none;
    padding: 1em;
    margin: 1em;
    &:enabled {
      cursor: pointer;
    }
  }
  .agenda {
    border-radius: 0.4em;
    border: 0.2em solid black;
    // width: calc(100% / 1.2);
    height: calc(100% / 1.1);
    background-color: #785046;
    flex-grow: 1;
    padding: 1em;
    display: flex;
    .face {
      background-color: #efedde;
      overflow: hidden;
      display: flex;
      flex-direction: column;
      align-items: center;
      flex-grow: 1;
      border: 0.2em solid black;
      border-radius: 0.4em;
      padding: 0.5em;
      display: flex;
      input,
      textarea {
        background-color: transparent;
        width: 100%;
        font-family: monospace;
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
</style>
