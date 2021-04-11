<template>
  <div class="container">
    <div class="title">
      <div class="start">
        <h6>ОТСКАНИРУЙТЕ ШТРИХ-КОД</h6>
        <form @submit.prevent="onSubmit">
            <input type="text" name="id" value="" placeholder="ввести значение вручную">
            <input type="submit" value="Поиск">
        </form>
      </div>

      <div class="description" hidden>
        <Description :params="props"/>
      </div>
      <div class="creator" hidden>
        <Creator :params="props" />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Description from '../components/Description';
import Creator from '../components/Creator';

export default {
  components: {
    Description,
    Creator
  },

  data() {
    return {
      props: {}
    }
  },

  methods: {
    
    async onSubmit(id) {
        axios.get(`http://test.i-mex.pro/api/barcodes/${id.target[0].value}`)
          .then(response => {
              console.log(response.status)
              this.props = response.data;
              this.openUpTheDesc(this.props);
          })
          .catch(error => {
            if (error.response.status == 404) {
              this.props = id.target[0].value;
              this.openUpTheCreator();
              return;
            } else if (error.response.status == 503) {
              console.error(error.message, 'Server is not avaliable');
              return;
            } else {
              alert('Error: ', error);
              return;
            }
          });
    },

    openUpTheDesc(res) {
      const start = document.querySelector('.start');
      const comp = document.querySelector('.description');
      start.hidden = true;
      comp.hidden = false;
    },

    openUpTheCreator() {
      const start = document.querySelector('.start');
      const comp = document.querySelector('.creator');
      start.hidden = true;
      comp.hidden = false;
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}


form input:nth-child(1) {
  font-weight: 300;
  font-size: 35px;
  color: #526488;
  word-spacing: 5px;
  padding: 9px 0;
}


form input:nth-child(2) {
  font-weight: 300;
  font-size: 35px;
  color: #F1F1F1;
  word-spacing: 5px;
  padding: 9px 5px;
  background-color: #35495e;
  border-radius: 5%;
  outline: none;
  cursor: pointer;
}

.links {
  padding-top: 15px;
}

@media screen and (max-width: 700px) {
  .title {
    font-weight: 200;
    font-size: 60px;
  }
}

@media screen and (max-width: 400px) {
  .title {
    font-weight: 100;
    font-size: 40px;
  }

  form input:nth-child(1) {
    font-weight: 100;
    font-size: 16px;
    color: #526488;
    word-spacing: 5px;
    padding: 9px 0;
  }

  form input:nth-child(2) {
    font-weight: 100;
    font-size: 16px;
  }
}

</style>
