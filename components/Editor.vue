<template>
  <div class="container__editor">
       <select name="selector">
        <option value="14">Труба прямошовная</option>
        <option value="15">Труба спиралешовная</option>
       </select>
     <div class="grid-classifier-params">

       <strong>Калькулятор массы:</strong><br />
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

    <input type="button" value="сохранить" class="button" @click="save"/> 
    <input type="button" value="исправить" class="button" @click="treat"/> 
  </div>
</template>

<script>
export default {
    name: 'Editor',

    data() {
      return {
        selectedClassifier: this.$router.params.props,
      }
    },

    methods: {
      createObject() {
        const object = {
          barcode: this.$router.params.props.barcode,
          classifier_id: '',
          mass: '',
          params_value: [],
        };
        return object;
      },

      save() {
        const json = JSON.stringify(this.createObject());
        console.log(json);
        axios.post(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, json)
        .then(response => console.log('ok'))
        .catch(error => {
          if (error.response.status == 400) {
            console.log('такой штрих-код уже есть в системе');
          } else {
            console.log(error);
          }
        });
      },

      treat() {
        axios.patch(`http://test.i-mex.pro/api/barcodes/${this.$router.params.props.barcode}`, {

        })
        .then(response => console.log('ok'))
        .catch(error => {
          if (error.response.status == 405) {
            console.log('параметры не указаны');
          } else {
            console.log(error);
          }
        });
      }
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

 