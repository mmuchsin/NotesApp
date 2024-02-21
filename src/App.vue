<script setup>
import { ref, watch } from 'vue'
import { v4 as uuidv4 } from 'uuid'

const isEnable = ref(false)
const showModal = () => isEnable.value = true
const hideModal = () => isEnable.value = false

// Load notes from localStorage or start with an empty note
const notes = ref(JSON.parse(localStorage.getItem('notes')) || [])

const noteText = ref('') // This will hold the text of the new note
const getRandomLightColor = () => {
  const hue = Math.floor(Math.random() * 360);
  const saturation = Math.floor(Math.random() * 30) + 70;
  const lightness = Math.floor(Math.random() * 30) + 70;
  return `hsl(${hue}, ${saturation}%, ${lightness}%)`;
}


const addNote = () => {
  const currentDate = new Date()
  const formattedDate = `${currentDate.getDate()}-${currentDate.getMonth() + 1}-${currentDate.getFullYear()}`
  notes.value.push({ id: uuidv4(), text: noteText.value, date: formattedDate, backgroundColor: getRandomLightColor() })
  noteText.value = ''
  hideModal()
}

const deleteNote = (index) => {
  notes.value.splice(index, 1)
}

// Watch for changes in notes and save to localStorage
watch(notes, () => {
  localStorage.setItem('notes', JSON.stringify(notes.value))
})

</script>



<template>
  <main>
    <div class="overlay" v-if="isEnable">
      <div class="modal">
        <textarea v-model.trim="noteText" name="note" id="note" cols="30" rows="10"></textarea>
        <button @click="addNote">Add Note</button>
        <button class="close" @click="hideModal">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal">+</button>
      </header>
      <div class="card-container">
        <div class="card" v-for="note in notes" :key="note.id" :style="{backgroundColor: note.backgroundColor}">
          <p class="main-text">{{ note.text }}</p>
          <p class="date">{{ note.date }}</p>
          <button @click="deleteNote(index)">Delete Note</button>
        </div>
      </div>
    </div>
  </main>
</template>




<style scoped>
main {
  height: 100vh;
  width: 100vw;
}

.container {
  max-width: 1000px;
  padding: 10px;
  margin: 0 auto;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 75px;
}

header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: rgb(21, 20, 20);
  border-radius: 100%;
  color: white;
  font-size: 20px;
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
}

.date {
  font-size: 12.5px;
  font-weight: bold;
}

.card-container {
  display: flex;
  flex-wrap: wrap;
}

.card button {
  background-color: rgb(43, 39, 39);
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.77);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
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
  background-color: blueviolet;
  cursor: pointer;
  border: none;
  color: white;
  margin-top: 15px;
}

.modal .close {
  background-color: rgb(166, 4, 4);
  margin-top: 7px;
}
</style>