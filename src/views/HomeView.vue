

<template>
    <div class=" h-screen bg-gray-100">
  





    
    
    <h2 class=" font-mono text-sm font-bold py-2 px-2">Created Cards</h2>
    <section class=" mx-3 flex createdCards space-x-6 overflow-scroll" v-if="cards.length > 0">
      
      <div v-for="(cardItem, index) in cards" :key="index" class="card-display relative cardUi bg-white w-[200px] min-w-[200px] h-[300px] mt-10 space-y-1 ">
        
        <div class="topNarration flex justify-between items-center px-4">
          <h3 class=" uppercase text-xs font-bold tracking-widest"> {{ cardItem.title }}</h3>
          <p class=" text-xl font-bold">{{ cardItem.rank }}</p>
        </div>

        <div class=" imageHolder flex justify-center items-center">
          <div class=" bg-white h-36 w-36 rounded-full flex justify-center items-center">
            <img class=" rounded-full object-cover h-36 w-36" :src="cardItem.previewSrc" alt="Card Image" width="100">
          </div>
        </div>


        <div class=" description flex justify-center items-center tracking-wider text-center text-xs w-10/12 mx-auto">
          <p class="">{{ cardItem.description }}</p>
        </div>
        
        <div class=" tollAction absolute bottom-10 inset-x-0 space-x-2 flex justify-center items-center text-sm">
          <div class=" uppercase tracking-widest font-bold">{{ cardItem.action }}</div>
          <div>{{ cardItem.toll }}</div>
        </div>
        <div class="bottomNarration absolute bottom-2 inset-x-0 flex justify-between items-center px-4">
          <p class=" text-xl font-bold">{{ cardItem.rank }}</p>
          <h3 class=" uppercase text-xs font-bold tracking-widest"> {{ cardItem.title }}</h3>
          
        </div>
        
        <div class=" cardEditAndSave absolute -top-6 inset-x-0 text-xs  w-[200px] flex justify-between px-4">
          <button @click="editCard(index)">Edit</button>
          <button @click="deleteCard(index)">Delete</button>
          <button v-if="editCardIndex === index" @click="saveCardChanges(index)">Save</button>
          
        </div>
        
      </div>
    </section>

    <section class=" fixed bottom-0 inset-x-0 createcardForm shadow-2xl bg-white rounded-t-2xl py-4 mx-auto">
      
      <form class=" space-y-2"  @submit.prevent="addCard">
       
        <div class=" flex justify-between items-center w-11/12 mx-auto bg-gray-100">
          
          <input class=" block w-full text-sm text-slate-500
        file:mr-4 file:py-2 file:px-4
        file:rounded-full file:border-0
        file:text-sm file:font-semibold
        file:bg-violet-50 file:text-violet-700
        hover:file:bg-violet-100
      " type="file" id="cardImage" @change="previewCardImage" required>
          <div v-if="card.previewSrc">
            <img :src="card.previewSrc" alt="Card Image Preview" width="100">
          </div>
        </div>
        <div class=" w-full flex">
          
        </div>
        <div class=" w-full flex justify-center items-center mx-auto">

          <textarea class=" bg-gray-100 flex w-11/12  mx-auto h-16 px- py-2" placeholder=" Type the event description" id="cardDescription" v-model="card.description" required></textarea>
        </div>
        <div class=" flex justify-between w-11/12 items-center mx-auto space-x-2">
          <input placeholder=" Theme"  class=" bg-gray-100 w-11/12 mx-auto h-10 px-2" type="text" id="cardTitle" v-model="card.title" required>

          <input class=" bg-gray-100 w-11/12 mx-auto h-10 px-2" placeholder=" rank" type="number" id="rank" v-model.number="card.rank" required>
        </div>
        <div class="flex justify-between w-11/12 mx-auto items-center">
        
          <div class=" flex justify-between w-full items-center mx-auto space-x-2">
            <input placeholder=" Action" class=" bg-gray-100 w-11/12 mx-auto h-10 px-2" type="text" id="action" v-model="card.action" required>

            <input placeholder="Toll" class=" bg-gray-100 w-11/12 mx-auto h-10 px-2" type="number" id="toll" v-model.number="card.toll" required>
          </div>
      </div>
        <button class="bg-green-500 text-white uppercase flex justify-center items-center h-10 font-bold tracking-widest w-11/12 mx-auto" :disabled="!isCardFormValid">Create Card</button>
      </form>
    </section>
  </div>
  
</template>

<script>

import { ref, computed } from 'vue';

export default {
  name: 'CardDeckForm',
  data() {
    return {
      cardDeck: {
        coverImage: null,
        title: '',
        description: '',
        rules: '',
        gamePoints: null,
        numberOfCards: 50,
        previewSrc: null,
      },
      decks: [],
      editIndex: null,
      card: {
        cardImage: null,
        title: '',
        description: '',
        rank: null,
        toll: null,
        action: '',
        previewSrc: null,
      },
      cards: [],
      editCardIndex: null,
    };
  },
  computed: {
    isFormValid() {
      const isTitleValid = this.cardDeck.title.trim();
      const isDescriptionValid = this.cardDeck.description.trim();
      const isRulesValid = this.cardDeck.rules.trim();
      const isGamePointsValid = this.cardDeck.gamePoints && this.cardDeck.gamePoints > 0;
      const isCoverImageValid = this.cardDeck.coverImage && this.cardDeck.previewSrc;
      const isNumberOfCardsValid = this.cardDeck.numberOfCards >= 50 && this.cardDeck.numberOfCards <= 100;
      return isTitleValid && isDescriptionValid && isRulesValid && isGamePointsValid && isCoverImageValid && isNumberOfCardsValid;
    },
    isCardFormValid() {
      return this.card.title && this.card.description && this.card.rank && this.card.toll && this.card.action && this.card.cardImage;
    },
  },
  methods: {
    addDeck() {
      this.decks.push({...this.cardDeck});
      this.resetDeckForm();
    },
    resetDeckForm() {
      this.cardDeck = {
        coverImage: null,
        title: '',
        description: '',
        rules: '',
        gamePoints: null,
        numberOfCards: 50,
        previewSrc: null,
      };
    },
    previewImage(event) {
      const file = event.target.files[0];
      this.cardDeck.coverImage = file;
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
            this.cardDeck.previewSrc = e.target.result;
        };
        reader.readAsDataURL(file);
      } else {
        this.cardDeck.previewSrc = null;
      }
    },
    addCard() {
      this.cards.push({...this.card});
      this.resetCardForm();
    },
    resetCardForm() {
      this.card = {
        cardImage: null,
        title: '',
        description: '',
        rank: null,
        toll: null,
        action: '',
        previewSrc: null,
      };
    },
    previewCardImage(event) {
      const file = event.target.files[0];
      this.card.cardImage = file;
      if (file) {
        const reader = new FileReader();
        reader.onload = (e) => {
            this.card.previewSrc = e.target.result;
        };
        reader.readAsDataURL(file);
      } else {
        this.card.previewSrc = null;
      }
    },
    editDeck(index) {
      this.cardDeck = { ...this.decks[index] };
      this.editIndex = index;
    },
    saveChanges(index) {
      this.decks[index] = this.cardDeck;
      this.resetDeckForm();
      this.editIndex = null;
    },
    deleteDeck(index) {
      this.decks.splice(index, 1);
      if (this.editIndex === index) this.editIndex = null;
    },
    editCard(index) {
      this.card = { ...this.cards[index] };
      this.editCardIndex = index;
    },
    saveCardChanges(index) {
      this.cards[index] = this.card;
      this.resetCardForm();
      this.editCardIndex = null;
    },
    deleteCard(index) {
      this.cards.splice(index, 1);
      if (this.editCardIndex === index) this.editCardIndex = null;
    },
  },
};

</script>