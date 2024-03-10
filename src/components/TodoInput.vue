<template>
    <h1>
    TODOリスト
  </h1>
    <div>
        <p v-if="isErrMsg">{{ errMsg }}</p>
        <form v-on:submit="onSubmitForm">
            <label class="textbox-5-label">やること<input type="text" class="textbox-5" v-model="input"/></label>
            <label class="textbox-5-label">期限<input type="date" class="textbox-5" v-model="inputDate"/></label><br>
            <input type="submit"  class="button-3" value="登録"/>
        </form>
    </div>
</template>

<script setup>
import { ref } from "vue";
import { statuses } from "../const/constList";

const input = ref("");
const inputDate = ref("");
const isErrMsg = ref(false);
const errMsg = ref("");

function onSubmitForm(){
    if(input.value=="" || inputDate.value==""){
        isErrMsg.value = true;
        errMsg.value = "タスク・期限の両方入力してください。";
        //デフォルトでは、送信でリロードされるためエラーメッセージが
        //一瞬だけしか表示されないので、リロードを防ぐ設定
        event.preventDefault();
        return;
    }
    //ローカルストレージからJSON(文字列形式)で取得
const items = JSON.parse(localStorage.getItem("items")) || [];

const newItem ={
    id: items.length,
    //ref()で定義しているので.valueが必要
    content: input.value,
    limit: inputDate.value,
    state: statuses.NOT_START,
    onEdit: false,
};
//itemsに「やること、期限、ステータス、初期値」を詰める
items.push(newItem);
//ローカルストレージにJSON形式で追加(配列のままでは無理)
localStorage.setItem("items",JSON.stringify(items));
}
</script>



<style scoped>
.textbox-5,
.textbox-5-label {
    color: #24282e;
}

.textbox-5-label {
    display: block;
    margin-bottom: 5px;
    font-size: .9em;
}

.textbox-5 {
    width: 100%;
    padding: 8px 10px;
    border: none;
    border-radius: 3px;
    background-color: #eff2ee;
    font-size: 1em;
    line-height: 1.5;
}

.textbox-5:focus{
    outline: 1px solid #fff;
}

.textbox-5::placeholder {
    color: rgba(255,255,255,.5);
}

.button-3 {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 250px;
    margin:0 auto;
    padding: .9em 2em;
    border: none;
    border-radius: 5px;
    box-shadow: 0 2px 3px rgb(0 0 0 / 25%), 0 2px 3px -2px rgb(0 0 0 / 15%);
    background-color: #56c82d;
    color: #fff;
    font-weight: 600;
    font-size: 1em;
}

.button-3:hover {
    background-color: #46b81d;
}
</style>