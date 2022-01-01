<template>
  <div id="app">
    <div class="agenda">
      <div
        class="face"
        v-for="(page, index) in agenda.currentPages"
        :key="index"
      >
        <textarea
          spellcheck="false"
          v-model="page.textarea"
          @input="syncToLocalStorage()"
          @focus="agenda.currentPage = page.index"
        ></textarea>
        <span>
          {{ page.index }}
        </span>
      </div>
    </div>
    <button @click="nextFace()">Next</button>
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => {
    return {
      agenda: {
        facesNumber: 2,
        currentFace: 2,
        faces: [],
      },
    };
  },
  mounted() {
    if (localStorage.agenda) this.agenda = JSON.parse(localStorage.agenda);
    else this.createAgenda();
    this.loadAgenda();
  },
  methods: {
    syncToLocalStorage() {
      localStorage.agenda = JSON.stringify(this.agenda);
    },
    createAgenda() {
      for (let face = 0; face <= this.agenda.facesNumber; face++) {
        for (let page = face; page <= face + 1; page++) {
          this.agenda.faces.push({
            index: this.agenda.pages.length + 1,
            textarea: "",
          });
        }
      }
    },
    loadAgenda() {
      this.agenda.currentPages = [];
      for (
        let page = this.agenda.currentFace;
        page <= this.agenda.currentFace + 1;
        page++
      ) {
        console.log(page);
        this.agenda.currentPages.push(this.agenda.pages[page - 1]);
      }
    },
    nextFace() {
      if (this.agenda.currentFace >= this.agenda.facesNumber) {
        for (let page = face; page <= face + 1; page++) {
          this.agenda.pages.push({
            index: this.agenda.pages.length + 1,
            textarea: "",
          });
        }
      }
      this.agenda.currentFace++;
      this.loadAgenda();
    },
    // prevFace() {
    //   this.agenda.currentFace--;
    //   this.loadAgenda();
    // },
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
