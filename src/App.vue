<script setup>
import { ref, reactive } from 'vue';
import axios from 'axios';
let username = ref('')
let password = ref('')
let token = ref('')
let info_obj = reactive({})
function login() {
  let requestBody = {
    username: username.value,
    password: password.value
  };
  console.log(requestBody)

  fetch('https://wyapi.chouluochi.online/api/login', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify(requestBody)
  })
    .then(response => response.json())
    .then(data => {
      console.log(data)
      // console.log(data.code)
      if (data.code != 200) {
        alert('密码错误!')
        return
      }
      token.value = data.token
      console.log(token)
    })
    .catch((error) => {
      console.error('Error:', error);
    });
}
function getToken() {
  if (!token.value) {
    alert('请先登录获取token')
  } else {
    console.log(token.value)
    fetch('https://wyapi.chouluochi.online/api/user/getInfo', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + token.value
      }
    })
      .then(response => response.json())
      .then(data => {
        console.log(data)
        // info_obj = data.data
        Object.assign(info_obj, data.data)
        console.log(info_obj)
      })
      .catch((error) => {
        console.error('Error:', error);

      });
  }
}
function logout() {
  token.value = ''
  info_obj = reactive({})
}
async function loginAxios() {
  let requestBody = {
    username: username.value,
    password: password.value
  };
  console.log(requestBody);

  let res =
    await axios.post('https://wyapi.chouluochi.online/api/login', requestBody)
  console.log(res);
  if (res.data.code != 200) {
    alert('输入账户错误,请检查')
    return
  }
  token.value = res.data.token
  console.log(token.value)
}
async function tokenAxios() {
  if (!token.value) {
    alert('请先登录获取token')
  } else {
    let res = await axios('https://wyapi.chouluochi.online/api/user/getInfo', {
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer ' + token.value
      }
    })
    console.log(res)
    Object.assign(info_obj, res.data.data)
  }
}

/* function test() {
  let a = {
    a: 2
  }
  let b = ['2', '2']
  let s = 22
  console.log(typeof tokenAxios);
  console.log(typeof b);

  console.log('typeof null类型', typeof null);
  console.log('typeof undefined类型', typeof undefined);
  console.log(undefined instanceof Object);
  console.log(null instanceof Object);
  console.log(undefined instanceof Object);
  console.log(a instanceof Object);
  console.log(b instanceof Object);
  console.log(tokenAxios instanceof Object);
} */
</script>

<template>
  <div class="myForm">
    账号:<input type="text" placeholder="输入账号" v-model="username">
    密码:<input type="password" placeholder="输入密码" v-model="password">
    <br>
  </div>
  <div class="btnGroup">
    <button @click="login">fetch登录获取</button>
    <br>
    <button @click="getToken">fetch验证token</button>
    <br>
    <button @click="loginAxios">axios登录</button>
    <br>
    <button @click="tokenAxios">axios验证token</button>
    <br>
    <button @click="logout">退出登录</button>
    <!-- <button @click="test"></button> -->
  </div>
  <hr>
  <div></div>
  <div class="info" v-if="token">
    <p>用户信息如下,点击验证可获取信息</p>
    <div class="data">
      创建时间: {{ info_obj.createTime }}
      <br>
      更新时间:{{ info_obj.updateTime }}
      <br>
      用户id:{{ info_obj.clcId }}
      <br>
      用户账号:{{ info_obj.userName }}
      <br>
      用户昵称:{{ info_obj.nickName }}
      <br>
      用户真实姓名:{{ info_obj.realName }}
    </div>
  </div>
</template>

<style scoped lang="scss">
.myForm {
  margin: 10px;
  display: flex;
  justify-content: center;
  align-items: center;

  input {
    margin: 4px;
    height: 30px;
  }
}

.btnGroup {
  display: flex;
  justify-content: center;
  gap: 10px;

}

.info {

  border: 2px solid black;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
