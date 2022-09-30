<script>
  import { ref } from "vue"
  import { collection, addDoc } from "firebase/firestore"; 
  import { db } from "../services/firebase";

  const EXAMPLES = [
    'exemple: Ed Sheeran - Shape of you',
    'exemple: The Weeknd - Blinding Lights',
    'exemple: Bruno Mars - 24K Magic',
    'exemple: Justin Bieber - Yummy',
  ];

  export default {
    setup() {
      const music = ref('');
      let i = ref(0);
      const refMusic = ref();
      const submit = () => {
        if (music.value === '')
          return;
        document.body.style.background = 'var(--secondary)';
        setTimeout(() => {
          document.body.style.background = "var(--primary)";
        }, 700);

        addDoc(collection(db, "musics"), {
          name: music.value,
        }).then(() => {
          console.log("Document successfully written!");
        })
        music.value = '';
      }
      
      setInterval(() => {
        i.value++;
        i.value = i.value % EXAMPLES.length;
      }, 2000);

      return {music, submit, i, EXAMPLES, refMusic}
    },
    mounted() {
      this.$refs.refMusic.focus();
      console.log("this.$refs.refMusic", this.$refs.refMusic);
    }
  }

</script>

<template>
  <div class="main">
    <h1>Proposez une musique</h1>
    <form  @submit.prevent="this.submit">
      <input ref="refMusic" type="text" :placeholder="EXAMPLES[i]" v-model="music" />
    </form>
    <div class="second" @click="this.submit">
      <img src="../assets/send.svg" alt='send' />
    </div>
  </div>
</template>

<style scoped>
  form {
    width: 42%;
    height: 10%;
    display: flex;
    box-shadow: 5px black;
    align-items: center;
  }

  h1 {
    color: white;
    font-size: 48px;
  }

  form input {
    width: 100%;
    padding: 0 12px 12px 12px;
    outline: none;
    border:none;
    border-bottom:4px solid white;
    font-size: 24px;
    background: transparent;
    color: white;
    text-align: center;
  }

  form input::placeholder {
    text-align: center;
    color: rgba(255, 255, 255, 0.5);
  }

  img {
    margin-top: 24px;
  }

  img:hover {
    transition: transform 0.4s ease-in-out;
    transform: translateX(6px);
  }
  .main{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }

  .second {
    display: flex;
    align-items: center;
    justify-content: center;
    color:white;
    width: 20%;
    gap: 15px;
    cursor: pointer;
  }

  @media screen and (min-width: 320px) and (max-width: 768px){
    h1 {
      font-size: 30px;
    }

    form input::placeholder {
      font-size: 16px;
    }

    form {
      width: 70%;
    }
  }

</style>
