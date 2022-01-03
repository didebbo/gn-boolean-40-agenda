<template>
  <div id="app">
    <div class="agenda">
      <div
        class="face"
        v-for="(page, index) in agenda.currentPages.slice().reverse()"
        :key="index"
      >
        <textarea
          spellcheck="false"
          v-model="page.body"
          @input="syncToLocalStorage()"
        ></textarea>
        <span>
          {{ page.index }}
        </span>
      </div>
    </div>
    <!-- <button @click="nextFace()">Next</button> -->
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => {
    return {
      agenda: {
        facesNumber: null,
        currentFace: null,
        pages: [],
        currentPages: [],
      },
    };
  },
  mounted() {
    this.initAgenda();
    this.loadPages();
  },
  methods: {
    syncToLocalStorage() {
      localStorage.agenda = JSON.stringify(this.agenda);
    },
    initAgenda() {
      if (localStorage.agenda) this.agenda = JSON.parse(localStorage.agenda);
      else {
        this.agenda.facesNumber = 1;
        this.agenda.currentFace = 1;
        for (let i = 1; i <= 2; i++) {
          this.agenda.pages.push({
            index: i,
            header: "",
            body: "",
          });
        }
      }
    },
    loadPages() {
      console.log("svuoto currentPages[]");
      this.agenda.currentPages = [];
      for (
        let i = this.agenda.currentFace * 2;
        i >= this.agenda.currentFace * 2 - 1;
        i--
      ) {
        console.log(i);
        console.log("aggiungo in currentPages " + this.agenda.pages[i]);
        this.agenda.currentPages.push(this.agenda.pages[i -1]);
      }
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
  .agenda {
    border: 0.2em solid black;
    width: (100% / 1.1);
    height: (100% / 1.1);
    padding: 1em;
    display: flex;
    .face {
      display: flex;
      flex-direction: column;
      align-items: center;
      flex-grow: 1;
      border: 0.2em solid black;
      padding: 0.5em;
      display: flex;
      textarea {
        width: 100%;
        flex-grow: 1;
        font-family: monospace;
        font-size: 1.2em;
        border: none;
        resize: none;
        // &:focus {
        //   outline: none;
        // }
      }
      span {
        position: relative;
        bottom: -0.5em;
        display: block;
        // margin-top: 1em;
      }
    }
  }
}
</style>
