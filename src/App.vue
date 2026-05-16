<script setup>
import { ref, computed } from 'vue'

import easyImg from './assets/easy.jpg'
import mediumImg from './assets/medium.jpg'
import hardImg from './assets/hard.jpg'

/* CURRENT IMAGE */
const currentImage = ref(easyImg)

/* WIN STATE */
const hasWon = ref(false)

/* PUZZLE PIECES */
const pieces = ref([
  1,2,3,
  4,5,6,
  7,8,9
])

const correctOrder = [
  1,2,3,
  4,5,6,
  7,8,9
]

const selectedPiece = ref(null)

const seconds = ref(0)

let timer = null

/* TIMER */
const formattedTime = computed(() => {

  const hrs = String(
    Math.floor(seconds.value / 3600)
  ).padStart(2,'0')

  const mins = String(
    Math.floor((seconds.value % 3600) / 60)
  ).padStart(2,'0')

  const secs = String(
    seconds.value % 60
  ).padStart(2,'0')

  return `${hrs}:${mins}:${secs}`
})

/* LEVELS */
function playEasy(){
  currentImage.value = easyImg
}

function playMedium(){
  currentImage.value = mediumImg
}

function playHard(){
  currentImage.value = hardImg
}

/* START GAME */
function startGame(){

  hasWon.value = false

  shufflePieces()

  clearInterval(timer)

  seconds.value = 0

  timer = setInterval(() => {
    seconds.value++
  },1000)
}

/* QUIT GAME */
function quitGame(){

  clearInterval(timer)

  pieces.value = [...correctOrder]

  selectedPiece.value = null

  seconds.value = 0

  hasWon.value = false
}

/* SHUFFLE */
function shufflePieces(){

  for(let i = pieces.value.length - 1; i > 0; i--){

    const j = Math.floor(
      Math.random() * (i + 1)
    )

    ;[pieces.value[i], pieces.value[j]] =
    [pieces.value[j], pieces.value[i]]
  }
}

/* SELECT PIECE */
function selectPiece(index){

  if(selectedPiece.value === null){

    selectedPiece.value = index
    return
  }

  const first = selectedPiece.value

  ;[pieces.value[first], pieces.value[index]] =
  [pieces.value[index], pieces.value[first]]

  selectedPiece.value = null

  checkWin()
}

/* WIN CHECK */
function checkWin(){

  const won = pieces.value.every(
    (piece,index) =>
      piece === correctOrder[index]
  )

  if(won){

    clearInterval(timer)

    hasWon.value = true
  }
}
</script>

<template>

  <div class="container">

    <!-- TOP TITLE -->
    <h1 class="game-name">
 <U>ALAIN @Puzzle Game</U> 
    </h1>

    <!-- MAIN TITLE -->
    <h1 class="main-title">
      Select a Puzzle Challenge
    </h1>

    <!-- CARDS -->
    <div class="cards">

      <!-- EASY -->
      <div class="card">

        <img
          :src="easyImg"
          alt="Easy"
        >

        <h2>Easy</h2>

        <button
          class="play-btn"
          @click="playEasy"
        >
          Play
        </button>

      </div>

      <!-- MEDIUM -->
      <div class="card">

        <img
          :src="mediumImg"
          alt="Medium"
        >

        <h2>Medium</h2>

        <button
          class="play-btn"
          @click="playMedium"
        >
          Play
        </button>

      </div>

      <!-- HARD -->
      <div class="card">

        <img
          :src="hardImg"
          alt="Hard"
        >

        <h2>Hard</h2>

        <button
          class="play-btn"
          @click="playHard"
        >
          Play
        </button>

      </div>

    </div>

    <!-- RECORDS -->
    <h1 class="records-title">
      Records
    </h1>

    <button class="update-btn">
      Update
    </button>

    <!-- PUZZLE TITLE -->
    <h1 class="bottom-title">
      Swap the Images to Win
    </h1>

    <!-- GAME BUTTONS -->
    <div class="buttons">

      <button
        class="game-btn"
        @click="startGame"
      >
        Start Game
      </button>

      <button
        class="game-btn"
        @click="quitGame"
      >
        Quit
      </button>

    </div>

    <!-- TIMER -->
    <p class="timer">
      Elapsed Time:
      {{ formattedTime }}
    </p>

    <!-- WIN MESSAGE -->
    <h1
      v-if="hasWon"
      class="win-message"
    >
      You Have Won!!
    </h1>

    <!-- PUZZLE GRID -->
    <div class="puzzle-grid">

      <div
        v-for="(piece,index) in pieces"
        :key="index"
        class="piece"
        :class="{
          selected:selectedPiece === index
        }"
        @click="selectPiece(index)"
      >

        <img
          :src="currentImage"
          :style="{
            left: `${-((piece - 1) % 3) * 150}px`,
            top: `${-Math.floor((piece - 1) / 3) * 150}px`
          }"
          class="piece-image"
        >

      </div>

    </div>

  </div>

</template>

<style>

body{
  margin:0;
  background:#f3f3f3;
  font-family:Arial, Helvetica, sans-serif;
}

.container{
  text-align:center;
  padding:20px;
}

/* TOP TITLE */
.game-name{
  font-size:45px;
  color:#17395c;
  margin-bottom:10px;
}

/* MAIN TITLE */
.main-title{
  font-size:28px;
  color:#17395c;
  margin-bottom:20px;
}

/* CARDS */
.cards{
  display:flex;
  justify-content:center;
  gap:20px;
  flex-wrap:wrap;
}

.card{
  background:#f6dddd;
  width:300px;
  padding:15px;
  border-radius:10px;
}

.card img{
  width:100%;
  height:200px;
  object-fit:cover;
  border-radius:5px;
}

.card h2{
  font-size:35px;
  color:#17395c;
}

/* BUTTONS */
.play-btn,
.game-btn{
  margin-top:15px;
  width:140px;
  height:45px;
  background:#304d2f;
  color:white;
  border:none;
  font-size:20px;
  font-weight:bold;
  cursor:pointer;
  border-radius:5px;
}

/* RECORDS */
.records-title{
  margin-top:40px;
  font-size:30px;
  color:#17395c;
}

.update-btn{
  width:140px;
  height:45px;
  background:#2f5c5c;
  border:none;
  border-radius:25px;
  color:white;
  font-size:20px;
  font-weight:bold;
  cursor:pointer;
}

/* BOTTOM TITLE */
.bottom-title{
  margin-top:50px;
  font-size:45px;
  color:#17395c;
}

/* GAME BUTTONS */
.buttons{
  display:flex;
  justify-content:center;
  gap:10px;
  margin-top:20px;
}

/* TIMER */
.timer{
  margin-top:20px;
  font-size:22px;
  color:#17395c;
}

/* WIN MESSAGE */
.win-message{
  margin-top:10px;
  font-size:60px;
  color:#17395c;
  font-weight:bold;
}

/* PUZZLE GRID */
.puzzle-grid{
  margin:30px auto;

  width:450px;
  height:450px;

  display:grid;
  grid-template-columns:repeat(3,1fr);
}

/* PIECES */
.piece{
  position:relative;
  overflow:hidden;
  border:1px solid #ddd;
  cursor:pointer;

  width:150px;
  height:150px;
}

.piece.selected{
  border:4px solid red;
}

.piece-image{
  position:absolute;

  width:450px;
  height:450px;
}

</style>