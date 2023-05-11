<template>
  <div id="app">
    <p>
      <img alt="Vue logo" class="logo" src="./assets/logo.png" />
      <!-- 图片上传 -->
      <input
        type="file"
        class="photoFime"
        name="upload_file"
        id="uploadFile"
        ref="foreheadCir"
        mutiple="mutiple"
        accept="image/*"
        @change="uploadImg()"
        multiple
      />
    </p>
    <div class="qin_lin" @click="qinLin">清零</div>
    <div class="jie_guo">
      <p>表报数据： {{ list }}</p>
      <br />
      <span class="zong_shu">总数等于：{{ count }}</span>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  components: {},
  data() {
    return {
      token: "",
      list: "",
      count: 0,
    };
  },
  methods: {
    uploadImg() {
      let files = document.getElementById("uploadFile").files;
      for (let i = 0; i < files.length; i++) {
        var FileR = new FileReader();
        var that = this;
        FileR.readAsDataURL(files[i]);
        FileR.onload = (ele) => {
          var targetRes = ele.target.result;
          var targetRes02 = targetRes.split(",")[1];
          var FormD = new FormData();
          FormD.append("image", targetRes02);
          axios({
            method: "post",
            headers: {
              "Content-Type": "application/x-www-form-urlencoded",
            },
            url: `https://aip.baidubce.com/rest/2.0/ocr/v1/table?access_token=${that.token}`,
            data: FormD,
          })
            .then((e) => {
              e = e.data.tables_result[0].body;
              console.log(e);
              for (let i = 0; i < e.length; i++) {
                if (e[i].col_start == 6 || e[i].col_start == 7) {
                  if (e[i].words != "" && !isNaN(Number(e[i].words))) {
                    that.list += e[i].words + "  +  ";
                    that.count += Number(e[i].words);
                  }
                }
              }
              console.log(that.list);
              console.log(that.count);
            })
            .catch((e) => {
              console.log(e);
            });
        };
      }
    },
    qinLin() {
      this.count = 0;
      this.list = 0;
    },
  },
  mounted() {
    this.token =
      "24.26b7e71cb8ac1e2c5c07f6639a30b864.2592000.1685440397.282335-27736960";
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #46502c;

  margin-top: 60px;
}
.logo {
  width: 100px;
  border-radius: 16px;
  height: 100px;
  margin: 0 0 40px 40px;
}
.qin_lin {
  width: 81px;
  height: 50px;
  border: 1px solid #333;
  position: absolute;
  top: 103px;
  left: 215px;
  border-radius: 20px;
  text-align: center;
  line-height: 48px;
}
.photoFime {
  overflow: hidden;
  position: absolute;
  top: 62px;
  left: 48px;
  height: 102px;
  width: 109px;
  color: none;
  background: none;
  outline: none;
  border: none;
  opacity: 0;
  border-radius: 20px;
  cursor: pointer;
}
.jie_guo p {
  margin-left: 40px;
  font-size: 14px;
  text-align: left;
  width: 272px;
  font-size: 17px;
}
.jie_guo .zong_shu {
  font-size: 20px;
  margin-left: 40px;
}
</style>
