<script setup>
import { ref } from "vue";
import axios from "axios";

const showModal = ref(false)
const newNote = ref("")
const errorMessage = ref("")
const notes = ref([]);

//getRandomColor function to select a random color with a standard brightness level so it never chooses a color for a card that can obscure any text being held.
function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}

//Creating generateRandomNote function to generate an entirely random note via an API. This one will generate a programming quote, save the output to a variable, and use that variable to generate a new note.
const generateRandomNote = async () => {
  try {
    const response = await axios.get("https://jsonplaceholder.typicode.com/posts"); // Fetch random data (you can adjust the API endpoint as needed)
    const randomData = response.data[Math.floor(Math.random() * response.data.length)]; // Get a random item from the API response

    // Check if the item has a title (you can adjust this based on your API data structure)
    if (!randomData.title) {
      return;
    }

    notes.value.push({
      id: Math.floor(Math.random() * 1000000),
      text: randomData.title, // Use the title as the text for the note (you can adjust this based on your API data)
      date: new Date(),
      backgroundColor: getRandomColor(),
    });
  } catch (error) {
    console.error("Error generating a random note from API:", error);
  }
};

// handler for click event
const addNote = () => {
  // creating rule to say that a new note can only be created and pushed onto the array IF it has 10 characters or more, this prevents people from creating an empty note.
  if(newNote.value.length < 10)
  {
    return errorMessage.value = "New notes need to be 10 characters or more!"
  }
  notes.value.push({
    //Generating a random number between 0 and 1,000,000 to give the note a unique ID
    id: Math.floor(Math.random() * 1000000),
    text: newNote.value,
    date: new Date(),
    //invoking getRandomColor function whenever a new note is created (pushed onto the notes array)
    backgroundColor: getRandomColor()
  });

  showModal.value = false;
  newNote.value = "";
  errorMessage.value = "";
}

</script>
<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <textarea v-model.trim="newNote" name="note" id="note" cols="30" rows="10"></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <!--Envoking the addNote handler for the click event to add contents to a new note-->
        <button @click="addNote">Add Note</button>
        <button class="close" @click="showModal = false">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <div class="header-buttons">
          <button @click="showModal = true">Add New Note</button>
          <button class="generate-button" @click="generateRandomNote">Generate Random Note</button>
        </div>
      </header>
      <!-- creating a div with class "cards-container" -->
      <div class="cards-container">
        <div v-for="note in notes"
             :key="note.id"
             class="card" 
             :style="{backgroundColor: note.backgroundColor}">
          <p class="main-text">{{note.text}}</p>
          <p class="date">{{ note.date.toLocaleDateString("en-US") }}</p>
        </div>
      </div>  
    </div>
  </main>
</template>

<style scoped>

  @import url('./assets/base.css');
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
    /* width: 50px;
    height: 50px; */
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

  .cards-container {
    display: flex;
    flex-wrap: wrap;
  }

  .overlay {
    position: absolute;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.7);
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
    border: none;
    color: white;
    cursor: pointer;
    margin-top: 15px;
  }
  .modal .close {
    background-color: red;
    margin-top: 7px;
  }

  .modal p {
    color: red;
  }

  .header-buttons {
  display: flex;
  align-items: center;
}

.header-buttons button {
  margin-left: 10px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: blueviolet;
  border: none;
  color: white;
  cursor: pointer;
  border-radius: 5px;
}

.header-buttons .generate-button {
  background-color: #FFA500;
  display: inline-block;
  min-width: auto; /* Reset the minimum width */
  white-space: nowrap; /* Prevent text from wrapping */
}
</style>