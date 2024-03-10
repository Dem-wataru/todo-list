<template>
  <div class="todo-app">
    <p v-if="isErrMsg" class="error-message">{{ errMsg }}</p>
    <div v-if="isShowModal" class="modal">
      <div class="modal-content">
        <p>{{ deleteItemContent }}を削除してもよろしいですか？</p>
        <button @click="onDeleteItem" class="btn-confirm">はい</button>
        <button @click="onHideItem" class="btn-cancel">キャンセル</button>
      </div>
    </div>
    <table class="todo-table">
      <thead>
        <tr>
          <th class="th-id">ID<button @click="sortById">▼</button></th>
          <th class="th-value">やること</th>
          <th class="th-limit">期限<button @click="sortByLimit">▼</button></th>
          <th class="th-state">状態</th>
          <th class="th-edit">編集</th>
          <th class="th-delete">削除</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="item in items"
          :key="item.id"
          :class="{ red: new Date(item.limit) < today }"
        >
          <td>{{ item.id }}</td>
          <td>
            <span v-if="!item.onEdit">{{ item.content }} </span>
            <input
              v-else
              v-model="inputContent"
              type="text"
              class="edit-input"
            />
          </td>
          <td>
            <span v-if="!item.onEdit">{{ item.limit }} </span>
            <input v-else v-model="inputLimit" type="date" class="edit-input" />
          </td>
          <td>
            <span v-if="!item.onEdit && item.state"
              >{{ item.state.value }}
            </span>
            <select v-else v-model="inputState" class="edit-input">
              <option
                v-for="state in statuses"
                :key="state.id"
                :value="state"
                :selected="item.state && state.id == item.state.id"
              >
                {{ state.value }}
              </option>
            </select>
          </td>
          <td nowrap>
            <button
              v-if="!item.onEdit"
              @click="onEdit(item.id)"
              class="btn-edit"
            >
              編集
            </button>
            <button v-else @click="onUpdate(item.id)" class="btn-update">
              完了
            </button>
          </td>
          <td nowrap>
            <button @click="showDeleteModal(item.id)" class="btn-delete">
              削除
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>



<script setup>
import { ref } from "vue";
import { statuses } from "../const/constList";

let items = ref(JSON.parse(localStorage.getItem("items")) || []);
let inputContent = ref(""); //タスクの内容
let inputLimit = ref(""); //タスクの期限
let inputState = ref(""); //タスクのステータス
let isErrMsg = ref(false);
let errMsg = ref("");
let isShowModal = ref(false);
//削除対象のid
let deleteItemId = "";
let deleteItemContent = ref("");
let isOnEditOther = false;
const today = new Date();

//編集ボタン
function onEdit(id) {
  //他に編集中モードのタスクがないか確認
  items.value.map((item) => {
    if (item.onEdit) {
      //onEditがtrueのタスクがあれば終了
      isOnEditOther = true;
      return;
    }
  });
  if (isOnEditOther) {
    //エラーメッセージ
    errMsg.value = "他に編集中のタスクがあります。";
    isErrMsg.value = true;
    return;
  }
  //それぞれの変数に今の値を渡すことでテキスト状態で表示される
  inputContent.value = items.value[id].content;
  inputLimit.value = items.value[id].limit;
  inputState.value = items.value[id].state;
  items.value[id].onEdit = true;
}

//編集したら完了する
function onUpdate(id) {
  if (inputContent.value == "" || inputLimit.value == "") {
    isErrMsg.value = true;
    errMsg.value = "タスクの内容と期限を入力してください。";
    return;
  }
  const newItem = {
    id: id,
    content: inputContent.value,
    limit: inputLimit.value,
    state: inputState.value,
    onEdit: false,
  };
  //ローカルストレージは更新ができないのでidをもとにすべて置き換える
  items.value.splice(id, 1, newItem);
  // localStorageを更新
  localStorage.setItem("items", JSON.stringify(items.value));
  isErrMsg.value = false;
}

//削除
function showDeleteModal(id) {
  // 選択したアイテムを削除可能に
  isShowModal.value = true;
  //選択した項目のidを変数に
  deleteItemId = id;
  //確認モーダルに削除項目の名前を表示する
  deleteItemContent = items.value[id].content;
}

//モーダル画面「はい」の場合
function onDeleteItem() {
  // 配列のdeleteItemIdの要素から該当の1つを取り除く
  items.value.splice(deleteItemId, 1);
  isShowModal.value = false;
  //Idを振りなおす(1,2,3)が(1,3)みたいになっちゃうから
  items.value = items.value.map((item, index) => ({
    id: index,
    content: item.content,
    limit: item.limit,
    state: item.state,
    onEdit: item.onEdit,
  }));
  // localStorageを更新
  localStorage.setItem("items", JSON.stringify(items.value));
}

//モーダル画面「いいえ」の場合
function onHideItem() {
  isShowModal.value = false;
}

function sortByLimit() {
  items.value.sort((a, b) => new Date(a.limit) - new Date(b.limit));
  localStorage.setItem("items", JSON.stringify(items.value));
}
function sortById() {
  items.value.sort((a, b) => a.id - b.id);
  localStorage.setItem("items", JSON.stringify(items.value));
}
</script>

<style scoped>
.todo-app {
  max-width: 800px;
  margin: auto;
  font-family: "Arial", sans-serif;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  padding: 20px;
  border-radius: 8px;
  text-align: center;
  background-color: #ebebe3;
}

.red {
  color: red;
}

.error-message {
  color: red;
  text-align: center;
  margin-bottom: 20px;
}

.todo-table {
  margin-top: 20px;
}

.todo-table th,
.todo-table td {
  padding: 10px;
  text-align: center;
}

.edit-input {
  padding: 8px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.btn-confirm,
.btn-cancel,
.btn-edit,
.btn-update,
.btn-delete {
  padding: 8px 12px;
  margin: 4px;
  cursor: pointer;
  border: none;
  border-radius: 4px;
  color: #fff;
}

.btn-confirm {
  background-color: #28a745;
}

.btn-cancel {
  background-color: #dc3545;
}

.btn-edit {
  background-color: #007bff;
}

.btn-update {
  background-color: #17a2b8;
}

.btn-delete {
  background-color: #dc3545;
}
</style>