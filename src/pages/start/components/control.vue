<template>
  <div>
    <div :style="{ height: '20px' }"></div>
    <a-form :model="form" :style="{ width: '600px' }" @submit="handleSubmit">
      <a-form-item field="ip" label="地址">
        <a-input-group>
          <a-input :style="{ width: '80px' }" placeholder="" v-model="form.ssl" />
          <a-input :style="{ width: '160px' }" placeholder="" v-model="form.ip" />
          <a-input :style="{ width: '160px' }" placeholder="" v-model="form.path" />
        </a-input-group>
      </a-form-item>
      <a-form-item field="uid" label="uid">
        <a-input v-model="form.uid" placeholder="请输入玩家UID..." />
      </a-form-item>
      <a-form-item field="password" label="密码">
        <a-input-password v-model="form.password" placeholder="请输入密码" allow-clear />
      </a-form-item>
      <a-form-item field="command" label="command">
        <a-input v-model="form.command" placeholder="请输入命令..." />
      </a-form-item>
      <a-form-item>
        <a-space>
          <a-button html-type="submit">提交</a-button>
          <a-button html-type="reset" @click="handleReset">重置</a-button>
        </a-space>
      </a-form-item>

      
      <a-form-item label="返回数据">
        <a-input v-model="responseData" :disabled="true" />
      </a-form-item>

      
    </a-form>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue'
import { Message } from '@arco-design/web-vue'
import axios from 'axios';

const form = reactive({
  ssl: "http://",
  ip: '',
  path: "/submit",
  uid: '',
  password: '',
  command: ''
});
const handleReset = () => {
  
  localStorage.clear();
  
  form.uid = "";
  form.password = "";
  form.command = "";
  
  responseData.value = "";
  
  showMessage.value = false;
  messageType.value = "";
  message.value = "";
};


const responseData = ref('');

const showMessage = ref(false);
const messageType = ref<'error' | 'success' | ''>('');
const message = ref('');

const handleSubmit = () => {
  console.log(form);

  var Url = `${form.ssl}${form.ip}${form.path}`;
  var data = {
    uid: form.uid,
    password: form.password,
    command: form.command
  };

  axios.post(Url, data).then(res => {
    console.log(res);
    
    responseData.value = JSON.stringify(res.data, null, 2);

    
    if (res.data.retcode === 200) {
      localStorage.setItem('address', form.ssl + form.ip + form.path);
      localStorage.setItem('uid', form.uid);
      localStorage.setItem('password', form.password);
      
      showMessage.value = true;
      messageType.value = 'success';
      message.value = '数据保存成功！';
      Message.success('数据保存成功！');
    } else {
      showMessage.value = true;
      messageType.value = 'error';
      message.value = '数据保存失败！';
    }
  },
  err => {
    Message.error(err.message);
    console.log(err);
  });
};

const copyTocken = () => {
  console.log(1);
};
</script>

<style lang="less" scoped>

a-form {
  margin-top: 20px;
}

a-form-item {
  margin-bottom: 16px;
}

a-input-group {
  display: flex;
  align-items: center;
}

a-input-group > a-input {
  margin-right: 8px;
}
</style>
