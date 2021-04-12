<template>
  <div class="container">
    <div class="title">
      
      <h6>ОТСКАНИРУЙТЕ ШТРИХ-КОД</h6>
      <form @submit.prevent="onSubmit">
          <input type="text" name="id" value="" placeholder="ввести barcode вручную">
          <input type="submit" value="Поиск">
      </form>

    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  components: {
     
  },
 

  methods: {
    
    onSubmit(id) {
      axios.get(`http://test.i-mex.pro/api/barcodes/${id.target[0].value}`)
        .then(response => {
            console.log(response.status)
              
            this.$router.push({
              name: 'Home',
            });
            this.$router.params = {
              props: response.data, 
              status: response.status
            };
        })
        .catch(error => {
          if (error.response.status === 404) {
            this.$router.push({
              name: 'Home',
            });
            this.$router.params = id.target[0].value;
            console.log('такой штрих-код не найден в системе');
            return;
          } else if (error.response.status == 503) {
            console.error('Ошибка на сервере при обработке запроса');
            return;
          } else {
            return;
          }
        });
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
