<template>
  <div class="container">
    <div class="editor" hidden>
        <Editor />
    </div>
    <div class="barInfo">
      <h6>Идентификатор: <span class="params">{{ this.$router.params.props.barcode }}</span></h6>
      <h6>Дата поступления: <span class="params">{{ this.$router.params.props.date_in }}</span></h6>
      <h6>Дата отгрузки:<span id='out' class="params"> {{ this.$router.params.props.date_out }}</span></h6>
      <h6>Дата возврата: <span class="params">{{ this.$router.params.props.date_revert }}</span></h6>
      <h6>Дата проверки: <span class="params">{{ this.$router.params.props.date_check }}</span></h6>
      <h6>Номенклатура: <span class="params">{{ this.$router.params.props.classifier }}</span></h6>
      <h6>Размер 1: <span class="params">{{ this.$router.params.props.size_1 }}</span></h6>
      <h6>Размер 2: <span class="params">{{ this.$router.params.props.size_2 }}</span></h6>
      <h6>Размер 3: <span class="params">{{ this.$router.params.props.size_3 }}</span></h6>
      <h6>Масса: <span class="params">{{ this.$router.params.props.mass }} </span></h6>
      <input type="button" class="button" value="редактировать" @click="edit"/>
      <input type="button" class="button" value="проверено" @click="checked"/>
      <input type="button" class="button" value="отгрузить" v-if="!isOut" @click="outload"/>
      <input type="button" class="button" value="вернуть" v-if="isOut" @click="revert"/>
    </div>
    <div class="message">{{ timeMessage }}</div>
  </div>
</template>

<script>
import Editor from './Editor';
import axios from 'axios';

export default {
    name: "BarInfo",
    components:{
        Editor
    },
    data() {
        return {
            isOut: !this.$router.params.props.date_out ? false : true,
            timeMessage: this.timeChecker()
        }
    },

    methods: {
        edit() {
            const editor = document.querySelector('.editor');
            const current = document.querySelector('.barInfo');
            editor.hidden = false;
            current.hidden = true;
        },  

        timeChecker() {
          const dateOut = this.$router.params.props.date_out;

          if (dateOut) {
            const dateOutJs = this.timeConverter('js', dateOut);
            const dateNow = new Date();
            const timeLeft = dateNow.getTime() - dateOutJs.getTime();

            if (timeLeft < 18000000) {
              return 'Отгрузку можно отменить'
            } else 
            if (timeLeft > 18000000 && timeLeft < 86400000 ) {
              return `Внимание, товар отгружен ${Math.floor(timeLeft / 3600000)} часов назад!` 
            } else
            if (timeLeft > 86400000) {
              return `Внимание, товар отгружен ${Math.floor(timeLeft / 86400000)} дней назад!` 
            }
          }
        },

        timeConverter(to, toConvert) {
            if (to == 'js') {
              const year = toConvert.substring(0, 4);
              const month = toConvert.substring(5, 7);
              const date = toConvert.substring(8, 10);
              const hour = toConvert.substring(11, 13);
              const minute = toConvert.substring(14, 16);
              const second = toConvert.substring(17, 19);

              const jsFormat = new Date(year, month, date, hour, minute, second);
              
              return jsFormat;
            } else if (to == 'sql') {

              const jsFormat = new Date();
              const sqlFormat = `${jsFormat.getFullYear()}-${jsFormat.getMonth()}-${jsFormat.getDate()}T${jsFormat.getHours()}:${jsFormat.getMinutes()}:${jsFormat.getSeconds()}.000Z`; 
              return sqlFormat;
            }

        },

        checked() {
            const nowDate = this.timeConverter('sql');
            console.log(nowDate);
            axios.patch(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, {
              date_check: nowDate
            })
            .then(response => {
                console.log(`ok`);
            })
            .catch(err => console.log(error));
        },

        outload() {
            const nowDate = this.timeConverter('sql');
            console.log(nowDate);
            axios.patch(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, {
              date_out: nowDate
            })
            .then(response => {
                console.log('ok');
            })
            .catch(err => console.log(error));
        },

        revert() {
            const nowDate = this.timeConverter('sql');
            console.log(nowDate);
            axios.patch(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, {
              date_revert: nowDate
            })
            .then(response => {
                console.log('ok');
            })
            .catch(err => console.log(error));
        }
    },

    mounted() {
        if (!this.$router.params.props.date_in) {
          this.edit();
        }
    }

}
</script>

<style>
  .container {
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
  max-width: 41%;
  min-width: 41%;
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

.message {
  color: rgb(235, 12, 12);
  font-weight: 300;
}

@media screen and (max-width: 700px) {
   .button {
    font-weight: 200;
    font-size: 16px;
  }
}

 
</style>