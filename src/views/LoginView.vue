<script>
import { signInWithEmailAndPassword } from "firebase/auth";
import { auth } from '../services/firebase'
import { collection, getDocs, deleteDoc, doc } from "firebase/firestore"
import { ref } from "vue"
import { db } from "../services/firebase";

export default {
  setup() {
    const email = ref('');
    const password = ref('');
    const isLogged = ref(false);
    let musics = ref([]);
    let error = ref('');

    const reloadData = () => {
      musics.value = [];
      getDocs(collection(db, "musics")).then((res) => {
        res.forEach((doc) => {
          musics.value.push({
            id: doc.id,
            name: doc.data().name
          })
        });
      })
    }

    reloadData();

    auth.onAuthStateChanged((user) => {
      if (user) {
        isLogged.value = true;
      } else {
        isLogged.value = false;
      }
    });

    const deleteMusic = (idMusic) => {
      deleteDoc(doc(db, "musics", idMusic)).then(() => {
        musics.value = musics.value.filter((music) => {
          return music.id !== idMusic;
        })
      })
    }
  
    const login = () => {
      signInWithEmailAndPassword(auth, email.value, password.value)
        .catch((err) => {
          if (err?.message)
            error = err.message;
        });
    }

    const disconnect = () => {
      auth.signOut();
    }

    return { email, password, error, login, disconnect, isLogged, musics, deleteMusic }
  }
}
</script>

<template>
  <div v-if="!isLogged">
    <input type="text" placeholder="email" v-model="email" />
    <input type="password" placeholder="mot de passe" v-model="password" />
    <button @click="login">se connecter</button>
    <span v-if="error">{{ error }}</span>
  </div>
  <div v-else class="main-card">
    <div class="card" v-for="(music, index) in musics" :key="index" >
      <span>{{ music.name }}</span>
      <img src="../assets/bin.svg" alt='send' @click="deleteMusic(music.id)"/>
    </div>
  </div>
</template>

<style scoped>
  .main-card {
    display:flex;
    flex-wrap: wrap;
  }

  .card {
    list-style: none;
    padding: 0;
    color: white;
    background-color: rgb(248, 248, 248);
    height: 100px;
    overflow: hidden;
    max-width: 400px;
    margin: 12px;
    display:flex;
    flex-direction: column;
    position:relative;
    border-radius: 12px;
    padding: 8px;
    min-width: 30px;
  }

  .card span {
    word-wrap: break-word;
    color: rgb(33, 33, 33);
  }

  img {
    width: 30px;
    height: 30px;
    position: absolute;
    bottom: 8px;
    right: 8px;
    cursor: pointer;
  }

</style>
