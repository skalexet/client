<template>
  <div @click="editorOn" class="container__desc">
    <div class="notification" hidden>
      <h4>О Т Г Р У Ж Е Н О</h4>
      <span class="timing" hidden>Более пяти часов назад...</span>
      <button class="button" @click="$router.go()">Вернуть</button>
      <button class="button" @click="$router.go()">Отмена</button>
    </div>
    <div class="form">
      <h6>Идентификатор: 
        <div class="params">{{ params.barcode }}</div>
        <small class="">...</small>
      </h6>
      <h6>Дата поступления: <div class="params">{{ params.date_in }}</div><span>ред.</span></h6>
      <h6>Дата отгрузки:<div id='out' class="params"> {{ params.date_out }}</div><span>ред.</span></h6>
      <h6>Дата возврата: <div class="params">{{ params.date_revert }}</div><span>ред.</span></h6>
      <h6>Дата проверки: <div class="params">{{ params.date_check }}</div><span>ред.</span></h6>
      <h6>Номенклатура: <div class="params">{{ params.classifier }}</div><span>ред.</span></h6>
      <h6>Размер 1: <div class="params">{{ params.size_1 }}</div><small>...</small></h6>
      <h6>Размер 2: <div class="params">{{ params.size_2 }}</div><small>...</small></h6>
      <h6>Размер 3: <div class="params">{{ params.size_3 }}</div><small>...</small></h6>
      <h6>Масса: <div class="params">{{ params.mass }} </div><span>ред.</span></h6>
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
        const collection = document.querySelectorAll(".params");
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
      },

      showTimeMessage() {
        // const not = document.querySelector('.notification');
        // const form = document.querySelector('.form');              NOT TESTED YET
        // not.hidden = false;
        // form.style.pointerEvents = "none";
        // form.style.opacity = "0.55";
        
        // const year = dateOut.substring(0, 3);
        // const month = dateOut.substring(5, 6);
        // const date = dateOut.substring(8, 9);
        // const hour = dateOut.substring(11, 12);
        // const minute = dateOut.substring(14, 15);
        // const second = dateOut.substring(17, 18);

        // const prevDate = new Date(year, month, date, hour, minute, second);

        // const nowDate = new Date();

        // if (nowDate.getTime() - prevDate.getTime() > 18000) {
        //   const timeMessage = document.querySelector('.timing'); 
        //   timeMessage.hidden = false;
        }
    },

    mounted() {
      const element = document.querySelectorAll('#out');
      const dateOut = element[0].innerText;

      if (dateOut !== "" && 
          dateOut !== null &&
          dateOut !== "..required.."
          ) {

            this.showTimeMessage();
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

.timing {
  color: red;
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
