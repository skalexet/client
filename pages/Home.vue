<template>
  <div>

    <!-- <div class="notification" hidden>
      <h4>О Т Г Р У Ж Е Н О</h4>
      <span class="timing" hidden>Более пяти часов назад...</span>
      <button class="button" @click="$router.go()">Вернуть</button>
      <button class="button" @click="$router.go()">Отмена</button>
    </div> -->
 
    <keep-alive>
        <component :is="component"></component>
    </keep-alive>
     
  </div>
</template>

<script>
import BarInfo from '../components/BarInfo';
import Editor from '../components/Editor';

export default {
    name: 'Home',
    components: {
        BarInfo,
        Editor
    },

    methods: {
        save() {
            // save logic
        }
    },

    data() {
        this.$router.params.component = this.component;
        console.log(this.$router.params, this.component);

        return {
            component: this.$router.params.status === 404 ? 'Editor' : 'BarInfo'
        } 
    },

    mounted() {
        this.$router.params.component = this.component;
        console.log(this.$router.params.component);
        // if (this.$router.params.status === 200) {
        //     this.component = BarInfo;
        // } else if (this.$router.params.status === 404) {
        //     this.component = Editor;
        // }
    }
}

</script>

<style>

/* .container {
  margin: 0;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  font-weight: 100;
  font-size: 20px;
  word-spacing: 1px;
  letter-spacing: 0.5px;
  min-width: 700px;
}

.timing {
  color: red;
}

.container h6 {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  cursor: default;
}

.container input {
  margin: 0 0;
}

.container div {
  margin: 0.15em 0;
}

.container span {
  margin: 0.15em 0;
  font-weight: lighter;
  cursor: pointer;
}

.button {
  font-weight: 100;
  font-size: 18px;
  color: #F1F1F1;
  word-spacing: 5px;
  padding: 0.18em 0.2em;
  order: 2;
  background-color: #35495e;
  max-width: 30%;
  border-radius: 5%;
  outline: none;
  cursor: pointer;
}

.notification {
  background-color: black;
  width: 40%;
  height: 35%;
  position: fixed;
  text-align: center;
  
}

.notification h4 {
  color: white;
}

@media screen and (max-width: 700px) {
   .button {
    font-weight: 100;
    font-size: 16px;
  }
}

@media screen and (max-width: 400px) {

  .button {
    font-weight: 100;
    font-size: 16px;
  }
} */

</style>