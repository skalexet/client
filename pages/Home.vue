<template>
  <div>

    <!-- <div class="notification" hidden>
      <h4>О Т Г Р У Ж Е Н О</h4>
      <span class="timing" hidden>Более пяти часов назад...</span>
      <button class="button" @click="$router.go()">Вернуть</button>
      <button class="button" @click="$router.go()">Отмена</button>
    </div> -->
 
    <BarInfo />
     
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
        // this.$router.params.component = this.component;
        // console.log(this.$router.params, this.component);

        return {
            // component: this.$router.params.status === 404 ? 'Editor' : 'BarInfo'
        } 
    },

    mounted() {
        // this.$router.params.component = this.component;
        // console.log(this.$router.params.component);
        // if (this.$router.params.status === 200) {
        //     this.component = BarInfo;
        // } else if (this.$router.params.status === 404) {
        //     this.component = Editor;
        // }
    }
}

</script>

<style>

body {
  margin: 0;
}

@media screen and (max-width: 667px) {
  .container {
      max-width: 667px;
      min-width: 300px;
  }
  
  .title {
    font-weight: 200;
    font-size: 60px;
  }
}
</style>