<script setup>
import { ref } from "vue"
import { Icon } from '@iconify/vue'
import deleteIcon from '@iconify/icons-mdi/delete';

const showModal = ref(false)
const newNote = ref("")
const notes = ref([])
const errorMessage = ref("")
const idNoteToEdit = ref(null)

function getRandomColor() {
  const color = "hsl(" + Math.random() * 360 + ", 100%, 75%)";
  return color;
}

const addOrEditNote = () => {
  if (newNote.value.length < 10) {
    return errorMessage.value = "Note must be at least 10 characters long";
  }

  if (idNoteToEdit.value && notes.value.find(note => note.id === idNoteToEdit.value)) {
    notes.value = notes.value.map(note => {
      if (note.id === idNoteToEdit.value) {
        return {
          ...note,
          text: newNote.value
        }
      }
      return note
    })
  } else {
    notes.value.push({
      id: Math.floor(Math.random() * 1000),
      text: newNote.value,
      color: getRandomColor(),
      date: new Date()
    })
  }
  showModal.value = false;
  newNote.value = "";
  idNoteToEdit.value = null;
  errorMessage.value = "";
}

const deleteNote = (id) => {
  notes.value = notes.value.filter(note => note.id !== id)
}

const closeButton = () => {
  showModal.value = false;
  errorMessage.value = "";
  newNote.value = "";
}

const editNote = (id, text) => {
  showModal.value = true;
  newNote.value = text;
  idNoteToEdit.value = id;
  errorMessage.value = "";
}
</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <p @click="showModal = false"></p>
        <textarea v-model.trim="newNote" name="note" cols="30" rows="10" />
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addOrEditNote">Save</button>
        <button @click="closeButton" class="close">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="cards-container">
        <div v-for="note in notes" :key="note.id" class="card" @click="editNote(note.id, note.text)"
          :style="{ backgroundColor: note.color }">
          <div class="card-header">
            <p class="main-text">{{ note.text }}</p>
          </div>
          <div class="card-footer">
            <p class="date">{{ note.date.toLocaleDateString("en-US") }}</p>
            <button @click.stop="deleteNote(note.id)">
              <icon :icon="deleteIcon" class="delete-icon" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto
}

h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 75px;
}

.card {
  width: 225px;
  height: 225px;
  background-color: rgb(237, 182, 44);
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 20px;
  color: black;
  cursor: pointer;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-footer button {
  cursor: pointer;
  border: none;
  background-color: transparent;
}

.delete-icon {
  color: white;
  font-size: 20px;
}

.main-text {
  line-height: 1.25;
  font-size: 17.5px;
  font-weight: bold;
}

.date {
  font-size: 12.5px;
  margin-top: auto
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: white;
  border-radius: 100px;
  color: black;
  font-size: 20px;
}

.cards-container {
  display: flex;
  flex-wrap: wrap;
}


.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.77);
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
}

main {
  height: 100vh;
  width: 100vw;
}

.modal {
  width: 750px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}

.modal button {
  padding: 10px 20px;
  font-size: 20px;
  width: 100%;
  background-color: #2E86C1;
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 15px;
}

.modal .close {
  background-color:#000000;
  margin-top: 7px
}

.modal p {
  color: red;
}

textarea {
  width: 100%;
  height: 200px;
  padding: 5px;
  font-size: 20px;
}
</style>