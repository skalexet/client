<template>
  <div @click="editorOn" class="container__desc">
    <div class="notification" hidden>
      <h4>О Т Г Р У Ж Е Н О</h4>
      <button class="button" @click="$router.go()">Вернуть</button>
      <button class="button" @click="$router.go()">Отмена</button>
    </div>
    <div class="form">
      <h6>Идентификатор: 
        <div class="params1">{{ params.barcode }}</div>
        <small class="">...</small>
      </h6>
      <h6>Дата поступления: <div class="params1">{{ params.date_in }}</div><span>ред.</span></h6>
      <h6>Дата отгрузки:<div id='out' class="params1"> {{ params.date_out }}</div><span>ред.</span></h6>
      <h6>Дата возврата: <div class="params1">{{ params.date_revert }}</div><span>ред.</span></h6>
      <h6>Дата проверки: <div class="params1">{{ params.date_check }}</div><span>ред.</span></h6>
      <h6>Номенклатура: <div class="params1">{{ params.classifier }}</div><span>ред.</span></h6>
      <h6>Размер 1: <div class="params1">{{ params.size_1 }}</div><small>...</small></h6>
      <h6>Размер 2: <div class="params1">{{ params.size_2 }}</div><small>...</small></h6>
      <h6>Размер 3: <div class="params1">{{ params.size_3 }}</div><small>...</small></h6>
      <h6>Масса: <div class="params1">{{ params.mass }} </div><span>ред.</span></h6>
      <input type="button" class="button" value="Сохранить изменения" @click="onSubmit"/>
      <input type="button" class="button" value="Отмена" @click="$router.go()"/>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
    name: 'Description',
    props: ['params'],
    methods: {
      editorOn(event) {
        if (event.target.tagName === "SPAN") {
          const div = event.target.parentElement.children[0];
          div.hidden = true;

          const inp = `<input type="text" value="${div.innerHTML}" class="rewriter" />`;
          
          div.insertAdjacentHTML('afterend', inp);
          this.inputHandler(div);
        }
      },

      editorOff(rewriter, field) {
          rewriter.hidden = true;
          field.innerHTML = rewriter.value;
          field.hidden = false;
          rewriter.remove();
      },

      inputHandler(field) {
        const rewriter = document.querySelector(".rewriter");
        rewriter.focus();
       
        rewriter.addEventListener('blur', () => {
          this.editorOff(rewriter, field);
        });

        rewriter.addEventListener('change', () => {
          this.editorOff(rewriter, field);
        });
      },

      createObject() {
        return {
            barcode: '',
            date_in: '',
            date_out: '',
            date_revert: '',
            date_check: '',
            classifier: '',
            size_1: '',
            size_2: '',
            size_3: '',
            mass: ''
          };
      },

      onSubmit() {
        const collection = document.querySelectorAll(".params1");
        const object = this.createObject();
        
        let i = 0;

        for (let key in object) {
          object[key] = collection[i].innerText;

          i++;
        }
        console.log(object);
        this.serve(object);
      },

      serve(object) {
        axios.patch(`http://test.i-mex.pro/api/barcodes/${object.barcode}`)
        .then(res => console.log('Well done'+' '+res.status))
        .catch(err => console.log('Not found'))
      }
       
    },

    data() {
      return {
         
      }
    },


    mounted() {
      const dataOut = document.querySelector('#out');
      if (dataOut.innerHTML !== "" || dataOut.innerHTML !== null) {
        const not = document.querySelector('.notification');
        const form = document.querySelector('.form');
        not.hidden = false;
        form.style.pointerEvents = "none";
        form.style.opacity = "0.55";
        console.log(dataOut)
      }
    }
}

</script>

<style>
.container__desc {
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

.container__desc h6 {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  cursor: default;
}

.container__desc input {
  margin: 0 0;
}

.container__desc div {
  margin: 0.15em 0;
}

.container__desc span {
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
  max-width: 20%;
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
 
</style>
