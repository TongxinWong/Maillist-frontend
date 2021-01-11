<template>
  <div class="home">
    <v-container
      class="my-5 d-flex flex-column justify-center"
      style="width:100vw;height:80vh;"
    >
      <div>
        <v-row>
          <v-text-field v-model="inputValue"></v-text-field>
          <v-btn
            class="ma-2"
            :loading="loading"
            :disabled="loading"
            color="secondary"
            @click="getRetInfo"
          >
            解析邮件地址列表
          </v-btn>
        </v-row>
        <div class="text-center">
          <v-chip
            v-for="(mailBox, index) in retInfo.mailBoxes"
            :key="index"
            class="ma-2"
            :color="
              mailBox.isError ? 'red' : mailBox.isValid ? 'green' : 'orange'
            "
            text-color="white"
          >
            {{ mailBox.userName }} | {{ mailBox.mailAddress
            }}{{
              mailBox.isError ? '' : mailBox.isValid ? '' : ' (invalid address)'
            }}
          </v-chip>
        </div>
        <v-alert
          v-for="info in alertInfo"
          :key="info"
          dense
          outlined
          type="error"
          >{{ info }}
        </v-alert>
      </div>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data: () => ({
    inputValue: '',
    retInfo: [],
    alertInfo: [],
    loading: false,
  }),
  methods: {
    getRetInfo: function() {
      this.loading = true;
      let data = { originMaillist: this.inputValue };
      axios
        .post('http://127.0.0.1:8080/api/emailParse', data)
        .then(
          (response) => (
            (this.loading = false),
            (this.retInfo = response.data),
            (this.alertInfo = response.data.errMsg),
            (this.inputValue = response.data.hasError
              ? this.inputValue
              : response.data.finalList)
          )
        );
    },
  },
};
</script>
