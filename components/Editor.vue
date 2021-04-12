<template>
  <div class="container__editor">
      <label>Классификатор</label>
       <select class="selector" name="selector" @change="setClassifierId">
        <option value="14" selected="selected">Труба прямошовная</option>
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

      setClassifierId() {
        const selector = document.querySelector('.selector');
        return selector.value;
      },

      async save() {
        const object = this.createObject();
        object.classifier_id = this.setClassifierId();
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

 