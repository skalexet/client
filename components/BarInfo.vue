<template>
  <div class="container">
    
    <div class="editor" hidden>
        <Editor />
    </div>

    <div class="barInfo">

      <div v-for="field of params">
        <strong>{{ field.parameter }}:</strong> 
        <span class="params"> {{ field.value }} </span>
      </div>
      <div v-for="p of sizeParams">
        <strong>{{ p.param_name }}:</strong> 
        <span class="params">{{ p.value }}</span> 
      </div>

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
            timeMessage: this.timeChecker(),
            sizeParams: this.$router.params.props.params_value,
            params: this.paramsToArray(this.$router.params.props),
            object: this.$router.params.props,
        }
    },

    methods: {
        paramsRecognizer(key) {
          switch (key) {
            case "barcode": return "штрих-код";
              break;
            case "date_in": return "дата загрузки";
              break;
            case "date_out": return "дата выгрузки";
              break;
            case "date_revert": return "дата возврата";
              break;
            case "date_check": return "дата проверки";
              break;
            case "classifier": return "классификатор";
              break;
            case "mass": return "масса";
              break;
            default: return false;
              break;
          }
        },

        paramsToArray(obj) {
          const arr = [];
          for (const key in obj) {
            const screenKey = this.paramsRecognizer(key);
            if (screenKey) {
              arr.push({parameter: screenKey, value: obj[key]});
            }
          }
          return arr;
        },

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
            .then(response => console.log(`ok`))
            .catch(error => {
              if (error.response.status === 404) console.log('такой штрих-код не найден в системе');
            });
        },

        outload() {
            const nowDate = this.timeConverter('sql');
            console.log(nowDate);
            axios.patch(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, {
              date_out: nowDate
            })
            .then(response => console.log(`ok`))
            .catch(error => {
              if (error.response.status === 404) console.log('такой штрих-код не найден в системе');
            });
        },

        revert() {
            const nowDate = this.timeConverter('sql');
            console.log(nowDate);
            axios.patch(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, {
              date_revert: nowDate
            })
            .then(response => console.log(`ok`))
            .catch(error => {
              if (error.response.status === 404) console.log('такой штрих-код не найден в системе');
            });
        }
    },

    mounted() {
        if (!this.object.date_in) {
          this.edit();
          console.log(this.$router.params.props);
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

 

.container input {
  margin: 0 0;
}

.container div {
  margin: 0.15em 0;
}

.barInfo div {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  cursor: default;
}

.container span {
  margin: 0.15em 0;
  font-weight: lighter;
  cursor: default;
}

.button {
  font-weight: 100;
  font-size: 21px;
  color: #F1F1F1;
  word-spacing: 5px;
  padding: 0.18em 0.2em;
   
  background-color: #35495e;
  max-width: 41%;
  min-width: 41%;
  border-radius: 4%;
  outline: none;
  cursor: pointer;
  margin-bottom: 15px;
}

.notification {
  background-color: black;
  width: 40%;
  height: 35%;
  position: fixed;
  text-align: center;
  
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