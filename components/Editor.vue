<template>
  <div class="container__editor">
      <label>Классификатор</label>
       <select class="selector" name="selector" @change="setClassifierId">
        <option value="14" selected="selected">Труба прямошовная</option>
        <option value="15">Труба спиралешовная</option>
       </select>
     
     <!-- // Calculator -->
     <div class="grid-classifier-params"
      v-if="selectedNormativDoc"
     >
       <strong>Калькулятор массы:</strong><br />
        <template
          v-for="(param, index) of selectedClassifier.array_params_all"
        >
          <div :key="param.name">
            <label>
              {{ param.name }} = 
                <!-- v-model="calcParams[index].value === '' ? "func" : calcParams[index].value"
                -->
              <input 
                :placeholder="'parameter'"
                class="calc-input"
                type="text" 
              /> {{ param.unit }}
            </label>
          </div>
        </template>

     <!-- -->


        <!-- <template
          v-for="(param, index) of selectedClassifier.array_params_all"
          v-if="selectedNormativDoc"  
        >
         <label>
            {{ param.name }} =
             <input
              v-model="
                calcParams[index].value === ''
                  ? calcInputValue(selectedNormativDoc.array_params, index)
                  : calcParams[index].value
              " 
              type="text"
              class="calc-input"
              :placeholder="
                calcInputValue(selectedNormativDoc.array_params, index)
              "
              :name="calcInputName(selectedClassifier.array_params_all, index)"
              @change="calculate(i)"
            />
            {{ param.unit }} 
          </label>
        </template> -->

        <!-- <div v-for="(item, i) of selectedMassEval">
          <br />
          <strong>Единица измерения:</strong> {{ item.unit }} <br />
          Расчетная масса = {{ calculate(i) }} кг/{{ item.unit }} <br />
          Формула = {{ item.mass_eval }}<br />
          <hr />
        </div> -->
      </div>

    <input type="button" value="сохранить" class="button" id="saveBtn" @click="save" hidden/> 
    <input type="button" value="исправить" class="button" id="treatBtn" @click="treat" /> 
  </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'Editor',

    data() {
      return {
        selectedNormativDoc: true,
                                      // hardcode to change   
        selectedClassifier: {
          array_params_all: [        //
            {
              name: "толщина",
              unit: "мм",
            },
            {                  //
              name: "ширина",
              unit: "мм",
            },
            {                  //
              name: "длинна",
              unit: "мм",
            }]                          //
        },
        calcParams: []
      }
    },

    methods: {
      fillCalcParams() {
        let i;
        while (i < 4) {
          this.calcParams.push({ value: i * 101 });
        }
      },
      createObject() {
        const object = {
          barcode: this.$router.params.props.barcode,
          date_in: this.setDateIn(),
          classifier_id: this.setClassifierId(),
          mass: '',
          params_value: [
            { param_id: 12, value: 1420},
            { param_id: 13, value: 20},
            { param_id: 14, value: 9450}
          ],
        };
        return object;
      },

      setClassifierId() {
        const selector = document.querySelector('.selector');
        return selector.value;
      },

      setSelectedClassifier() {
        const sizes = []
        this.selectedClassifier.array_params_all.forEach((el) => {
            const item = {
              name: el.name,
              unit: el.unit,
              values: [],
            }
            sizes.push(item)
          })
      },

      setDateIn() {
        const jsFormat = new Date();
        const sqlFormat = `${jsFormat.getFullYear()}-${jsFormat.getMonth()}-${jsFormat.getDate()}T${jsFormat.getHours()}:${jsFormat.getMinutes()}:${jsFormat.getSeconds()}.000Z`; 
        return sqlFormat;
      },

      async save() {
        const object = this.createObject();
        const json = JSON.stringify(object);
        console.log(json);
        await axios.post(`http://test.i-mex.pro/api/barcodes`, json, {})
        .then(response => console.log('ok'))
        .catch(error => {
          if (error.response.status == 400) {
            console.log('такой штрих-код уже есть в системе');
          } else {
            console.log(error);
          }
        });
      },

      async treat() {
        axios.patch(`http://test.i-mex.pro/api/barcodes/`, this.$router.params.props)
        .then(response => console.log('ok'))
        .catch(error => {
          if (error.response.status == 405) {
            console.log('параметры не указаны');
          } else {
            console.log(error);
          }
        });
      },

      showSaveButton() {
        const saveBtn = document.querySelector('#saveBtn');
        const treatBtn = document.querySelector('#treatBtn');

        saveBtn.hidden = false;
        treatBtn.hidden = true;
      }
    },

    mounted() {
      if (!this.$router.params.props.date_in) {
        this.showSaveButton();
      }

      this.fillCalcParams();
    }
}
</script>

<style>
.container__editor {
  margin: 0;
  min-height: 100vh;
   
  flex-direction: column;
  justify-content: center;
  font-weight: 100;
  font-size: 20px;
  word-spacing: 1px;
  letter-spacing: 0.5px;
  min-width: 700px;
}

.grid-classifier-params {
  grid-area: grid-classifier-params;
   
}

.grid-classifier-params template {
  grid-area: grid-classifier-params;
  display: flex;
  flex-direction: column;
}

.calc-input {
  width: 120px;
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

</style>

 