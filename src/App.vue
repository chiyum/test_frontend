<template>
  <div class="container d-flex flex-wrap">
    <div class="col-12 col-md-6 p-3">
      <div class="border rounded p-3">
        <h2>操作</h2>
        <form @submit.prevent>
          <BFormControls
              class="mb-3"
              label="名字"
              v-model="formDate.name"/>

          <BFormControls
              class="mb-3"
              label="年齡"
              type="number"
              v-model="formDate.age"/>

          <div class="d-flex gap-1">
            <button class="btn btn-success" @click="edit">修改</button>
            <button class="btn btn-warning" @click="create">新增</button>
          </div>
        </form>
      </div>
    </div>

    <div class="col-12 col-md-6 p-3">
      <div class="border rounded p-3">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">名字</th>
            <th scope="col">年齡</th>
            <th scope="col">操作</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(v,i) in users" :key="`user_${i}`">
            <th scope="row">{{ v.id }}</th>
            <td>{{ v.name }}</td>
            <td>{{ v.age }}</td>
            <td class="d-flex gap-1">
              <button class="btn btn-success" @click="selectUser(v)">
                修改
              </button>
              <button class="btn btn-danger" @click="remove(v)">
                刪除
              </button>
            </td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import axios from 'axios'
import {computed, onMounted, ref} from 'vue';
import BFormControls from "./components/BFormControls.vue";

interface User {
  id: number;
  name: string;
  age: number;
}

const baseUrl = 'https://tpvtkoia.wuc.us.kg' // 由面試官提供
const users = ref<User[]>([])
const formDate = ref({
  // id readonly
  id: 0,
  name: '',
  age: 0,
})

// 輸入是否無效
const inputIsInvalid = computed(() => {
  return formDate.value.name === '' || !formDate.value.age || formDate.value.age < 0
})

const create = () => {
  // 需有確認步驟
  if(inputIsInvalid.value){
    window.alert('請輸入姓名及年齡')
    return
  }
  const isConfirmed = window.confirm('確定要新增這筆資料嗎？')
  if (isConfirmed) {
    axios({
      method: 'post',
      url: baseUrl + '/api/user',
      data: {
        age: formDate.value.age,
        name: formDate.value.name
      }
    }).then(() => {
      // API文件沒有說明成功時會回傳什麼，所以這邊就直接重新取得資料
      // if(res.data.code === 200){
      //   getUsers();
      // }
      getUsers()
    }).catch(err => {
      alert('新增失敗')
      console.log(err)
    })
  }
}

const edit = () => {
  console.log(formDate.value.name, formDate.value.age)
  if(inputIsInvalid.value){
    window.alert('請輸入姓名及年齡')
    return
  }
  const isConfirmed = window.confirm(`確定修改${formDate.value.name}(${formDate.value.age})嗎？`)
  if (isConfirmed) {
    axios({
      method: 'put',
      url: baseUrl + '/api/user',
      data: {
        id: formDate.value.id,
        age: formDate.value.age,
        name: formDate.value.name
      }
    }).then(() => {
      getUsers()
    }).catch(err => {
      alert('修改失敗')
      console.log(err)
    })
  }
  // 需有確認步驟
}


const selectUser = (user: User) => {
  // 禁止使用 formDate.value = user
  // @DESC: 上面禁止的原因是會導致 formDate 與 user 共用記憶體位置，修改 formDate 會同時修改 user
  // 所以這邊使用展開運算值來避免這個問題
  formDate.value = { ...user }
}

const remove = (user: User) => {
  const { id, name } = user;
  const isConfirmed = window.confirm(`確定刪除${name}嗎？`)
  if(isConfirmed){
    axios({
      method: 'delete',
      url: baseUrl + '/api/user',
      data: {
        id
      }
    }).then(() => {
      getUsers()
    }).catch(err => {
      alert('刪除失敗')
      console.log(err)
    })
  }
  // 需有確認步驟
}

const getUsers = () => {
  axios({
    method: 'get',
    url: baseUrl + '/api/user',
  }).then(res => {
    const {data} = res.data
    users.value = data
  }).catch(err => {
    console.log(err)
  })

}

const setupPage = () => {
  getUsers()
}

onMounted(setupPage)
</script>

<style scoped>

</style>
