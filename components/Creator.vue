<template>
  <div @click="editorOn" class="container__desc">
    <div>
        <h6>Идентификатор: <div class="newParams">{{ params }}</div><small>...</small></h6>
        <h6>Дата поступления: <div class="newParams"> ..required.. </div><span>ред.</span></h6>
        <h6>Дата отгрузки:<div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Дата возврата: <div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Дата проверки: <div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Номенклатура: <div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Размер 1: <div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Размер 2: <div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Размер 3: <div class="newParams">..required.. </div><span>ред.</span></h6>
        <h6>Масса: <div class="newParams">..required.. </div><span>ред.</span></h6>
    </div>
    <div>
        <input type="button" class="button" value="Сохранить новый" @click="onSubmit"/>
        <input type="button" class="button" value="Отмена" @click="$router.go()"/>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
    name: 'Creator',
    props: ['params'], 
    methods: {
      editorOn(event) {
        if (event.target.tagName === "SPAN") {
          const div = event.target.parentElement.children[0];
          div.hidden = true;

          const inp = `<input type="text" value="" class="rewriter" />`;
          
          div.insertAdjacentHTML('afterend', inp);
          this.inputHandler(div);
        }
      },

      editorOff(rewriter, field) {
          rewriter.hidden = true;
          field.textContent = rewriter.value;
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
        const collection = document.querySelectorAll(".newParams");
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
        axios.put(`http://test.i-mex.pro/api/barcodes/${object.barcode}`)
        .then(res => console.log('Well done!'+ ' ' + res.status))
        .catch(err => console.log('Not found...'));
      }
       
    },

    mounted() {
       
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
  padding: 0.18em 0em;
  order: 2;
  background-color: #35495e;
  max-width: 30%;
  border-radius: 4%;
  outline: none;
}

@media screen and (max-width: 700px) {
   .button {
    font-weight: 100;
    font-size: 16px;
  }
}

@media screen and (max-width: 400px) {

  .button {
    font-weight: 100;
    font-size: 16px;
  }
}
 
</style>
