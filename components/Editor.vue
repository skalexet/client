<template>
  
    <div class="container">
      <div>
      <strong>Класс товара</strong><br>
       <select class="classifier" @change="setClassifierId">
         <option value="0" selected="selected">Не выбрано</option>
        <option value="14" >Труба прямошовная</option>
        <option value="15">Труба спиралешовная</option>
       </select>
      </div>

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
                value=""
              /> {{ param.unit }}
            </label>
          </div>
        </template>

        <div class="units">
          <br />
          <strong>Единица измерения:</strong> {{ "мм" }} <br />
          Расчетная масса = {{ 500 }} кг/{{ "mm" }} <br />
          Формула = {{ 'Formula*Formula' }} <br />
          <hr />
        </div>
       

      </div>
    <input type="button" value="сохранить" class="button" id="saveBtn" @click="save" hidden/> 
    <input type="button" value="исправить" class="button" id="treatBtn" @click="treat" /> 
    </div>
 
</template>

<script>
import axios from 'axios'
import qs from 'qs';

export default {
    name: 'Editor',

    data() {
      return {
        unitObject: '',
        selectedNormativDoc: true, 
        selectedClassifierId: '',
        selectedClassifier: '',
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
          date_out: '',
          date_revert: '',
          date_check: '',
          classifier: '',
          classifier_id: '',
          mass: '',
          params_value: [],
        };
        
        return object;
      },

      setClassifierParams(id) {
        console.log(id);
        if (id == 14) {
          this.selectedClassifier = {                            // hard code for a while
            array_params_all: [{ id: 12, name: "диаметр трубы", unit: 'мм', value: ''},
            { id: 13, name: "толщина стенки", unit: 'мм', value: ''},
            { id: 14, name: "длина", unit: 'мм', value: ''}]
          } 
        } else {
          console.log('hi');
          this.selectedClassifier = {};
        }
      },

      setClassifier(id) {
        switch (id) {
          case '14':                                             // hard code for a while
            this.unitObject.classifier = "Труба прямошовная";
            break;
          case '15': 
            this.unitObject.classifier = "Труба спиралешовная";
            break;
          default:
            break;
        };

        this.setClassifierParams(id);
      },

      setClassifierId() {
        const classifier = document.querySelector('.classifier');
        this.unitObject.classifier_id = classifier.value;
        this.setClassifier(classifier.value);
      },

      // setSelectedClassifier() {
      //   const sizes = [];

      //   this.selectedClassifier.arrayParamsAll.forEach((el) => {
      //       const item = {
      //         name: el.name,
      //         unit: el.unit,
      //         values: [],
      //       };

      //       sizes.push(item)
      //     })
      // },

      setDateIn() {
        const jsFormat = new Date();
        const sqlFormat = `${jsFormat.getFullYear()}-${jsFormat.getMonth()}-${jsFormat.getDate()}T${jsFormat.getHours()}:${jsFormat.getMinutes()}:${jsFormat.getSeconds()}.000Z`; 
        return sqlFormat;
      },

      save() {
        
        const json = JSON.stringify(this.unitObject);
        console.log(json);

        axios.post(`http://test.i-mex.pro/api/barcodes`, json)
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
        const object = this.$router.params.props;
        const json = JSON.stringify(object);
        const id = this.$router.params.props.barcode;
        console.log(id, json);
        axios.patch(`http://test.i-mex.pro/api/barcodes/${id}`, json)
        .then(response => console.log('ok'))
        .catch(error => {
              if (error.response.status === 404) console.log('такой штрих-код не найден в системе');
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
      const props = this.$router.params.props;
      if (!props.date_in) {
        this.showSaveButton();
      }
      // console.log(this.unitObject);
      // this.fillCalcParams();
      
      if (props.classifier_id != '') {
        const classifier = document.querySelector('.classifier');
        const options = classifier.options;
        
        for (let i = 0; i < options.length; i++) {
          console.log(options[i], props.classifier_id);
          if (options[i].value == props.classifier_id) {
            classifier.selectedIndex = i;
            this.setClassifierParams(props.classifier_id);
          }

          this.unitObject = props;
        }
      } else {
        this.unitObject = this.createObject();
      }
      
    }
}
</script>

<style>
/* .container__editor {
  margin: 0;
  min-height: 100vh;
   
  flex-direction: column;
  justify-content: center;
  font-weight: 100;
  font-size: 20px;
  word-spacing: 1px;
  letter-spacing: 0.5px;
  min-width: 700px;
} */

.units {

}

.grid-classifier-params {
  grid-area: grid-classifier-params;
   
}

.calc-input {
  width: 120px;
}

.button {
  font-weight: 200;
  font-size: 21px;
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

 