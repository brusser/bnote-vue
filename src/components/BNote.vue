<template>
  <div>
    <Hero/>
    <div class="container">
      <div id="newNote" class="block">
        <div v-cloak class="tile is-parent">
          <article class="tile notification is-child is-5">
            <div>
              <p class="title is-4">Add New Note</p>
              <form v-on:submit.prevent="newNote">
                <div class="field">
                  <label class="label">Note Title</label>
                  <p class="control has-icon has-icon-right">
                    <input v-model="newNoteTitle" type="text" class="input is-success" placeholder="Title input" required />
                    <span class="icon is-small">
                      <i class="fa fa-check"></i>
                    </span>
                  </p>
                  <p class="help is-success">Title Required</p>
                </div>

                <div class="field">
                  <label class="label">Date</label>
                  <p class="control">
                    <input v-model="newNoteDateTime" type="datetime-local" class="input" />
                  </p>
                </div>

                <div class="field">
                  <label class="label">Note Content</label>
                  <p class="control">
                    <textarea v-model="newNoteContent" type="text" class="textarea" placeholder="Text area"></textarea>
                  </p>
                </div>

                <div class="field is-grouped">
                  <p class="control">
                    <button type='submit' class="button is-primary">Submit</button>
                  </p>
                  <p class="control">
                    <button type='reset' v-on:click="clearNewNote" class="button is-link">Cancel</button>
                  </p>
                </div>
              </form>
            </div>
          </article>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="box">
        <h1 class="title is-2">
          <strong>Notes</strong>
        </h1>
        <div v-if="notes && notes.length">
          <div class="columns" v-for="noteChunk in chunkedNotes" :key="noteChunk[0].id">
            <div class="column is-one-third" v-for="note of noteChunk" :key="note.id">
              <article class="tile is-child box">
                <div class="content">
                  <p class="title is-4">{{note.title}}</p>
                  <div class="content">
                    <p>{{note.content}}</p>
                  </div>
                  <p class="buttons is-centered">
                    <a class="button is-info" v-on:click="editCard">
                      <span class="icon is-small">
                        <i class="fa fa-edit "></i>
                      </span>
                    </a>
                    <a class="button is-danger" v-cloak v-on:click="deleteCard">
                      <span class="icon is-small">
                        <i class="fa fa-times-circle "></i>
                      </span>
                    </a>
                  </p>
                </div>
              </article>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import chunk from 'lodash.chunk'
import Hero from './Hero.vue'

export default {
  name: 'BNote',
  components: {
    Hero
  },
  data() {
    return {
      notes: [],
      errors: [],
      newNoteTitle: '',
      newNoteDateTime: new Date().toISOString().slice(0, 16),
      newNoteContent: '',
    }
  },
  created() {
      // axios.defaults.headers['Authorization'] =
      // 'JWT ' + sessionStorage.getItem('authToken')
      // this.token = sessionStorage.getItem('authToken')
    axios
      .get(`http://localhost:8080/v1/note/`)
      .then(response => {
        console.log(response);
        this.notes = response.data;
      })
      .catch(e => {
        console.log(e);
        this.errors.push(e);
      });
  },
  computed: {
    chunkedNotes() {
      return chunk(this.notes, 3);
    }
  },
  methods: {
    newNote() {
      axios
        .post('http://localhost:8080/v1/note/', {
          title: this.newNoteTitle,
          // noteDateTime: this.newNoteDateTime,
          content: this.newNoteContent,
        })
        .then(response => {
          this.notes.push(response.data);
          this.clearNewNote()
        })
        .catch(e => {
          if (e.response.status === 400) {
            alert(e.message + ' : ' + JSON.stringify(e.response.data))
          } else {
            alert(e.message)
          }
        })
    },
    clearNewNote() {
      this.newNoteTitle = ''
      this.newNoteDateTime = new Date().toISOString().slice(0, 16)
      this.newNoteContent = ''
    },
    editCard() {
      // TODO
    },
    deleteCard() {
      // TODO
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>