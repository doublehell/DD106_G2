<template>
  <div class="login">
    <div class="welcome">
      <div class="movebox">
        <div class="signup nodisplay">
          <h1>果粉註冊</h1>
          <form class="less-padding" autocomplete="off">
            <div class="formarea">
              <div class="formtext">
                <label>姓名:</label>
                <br />
                <label>暱稱:</label>
                <br />
                <label>性別:</label>
                <br />
                <label>帳號:</label>
                <br />
                <label>密碼:</label>
                <br />
                <label>再次輸入密碼:</label>
                <br />
                <label>e-mail:</label>
                <br />
                <label>手機:</label>
              </div>
              <form class="forminputbox">
                <input type="text" v-model="form.name" />
                <input type="text" v-model="form.nick" />
                <div class="from_gender">
                  男
                  <input type="radio" name="gender" value="2" /> 女
                  <input type="radio" name="gender" value="1" />
                  其他
                  <input type="radio" name="gender" value="0" />
                </div>

                <input type="text" v-model="form.acc" />
                <input
                  id="signupPsw"
                  type="password"
                  v-model="form.psw"
                  @blur="checkPsw"
                />
                <input
                  id="signupRePsw"
                  type="password"
                  v-model="form.rePsw"
                  @blur="checkPsw"
                />
                <input type="text" v-model="form.mail" />
                <input type="text" v-model="form.phone" />
              </form>
            </div>

            <div class="signupsubmit">
              <p @click="signup">註冊</p>
            </div>
            <div class="switch_btn">
              <span>已經是果粉了?</span>
              <span id="switch_signup">登入</span>
            </div>
          </form>
        </div>
        <div class="signin">
          <h1>果粉登入</h1>
          <form class="more-padding" autocomplete="off">
            <label>帳號:</label>
            <input type="text" placeholder="請輸入帳號" v-model="member.acc" />
            <br />
            <label>密碼:</label>
            <input
              type="password"
              placeholder="請輸入密碼"
              v-model="member.psw"
            />
            <br />
            <div class="signinsubmit" @click="login">
              <p>登入</p>
            </div>
            <div class="switch_btn">
              <span>還不是果粉嗎?</span>
              <span id="switch_signin">註冊</span>
            </div>
          </form>
        </div>
      </div>
      <div class="leftbox">
        <h1>已經是果粉了?</h1>
        <img
          class="loginbutton"
          id="signin"
          src="@/assets/login.png"
          @click="changeSignin"
          alt
        />
      </div>
      <div class="rightbox">
        <h1>還不是果粉嗎?</h1>
        <img class="signupbutton" id="signup" src="@/assets/signup.png" alt />
      </div>
    </div>
  </div>
</template>
<script>
import $ from "jquery";
import { TubeGeometry, log } from "three";
export default {
  mounted() {
    $(window).resize(function() {
      if (window.innerWidth > 767) {
        if ($(".signin").hasClass("nodisplay") == false) {
          $(".movebox").css("left", "5%");
        } else {
          $(".movebox").css("left", "45%");
        }
        $("#signup").click(function() {
          $(".movebox").css("left", "45%");
          $(".signin").addClass("nodisplay");
          $(".signup").removeClass("nodisplay");
        });

        $("#signin").click(function() {
          $(".movebox").css("left", "5%");
          $(".signup").addClass("nodisplay");
          $(".signin").removeClass("nodisplay");
        });
      } else {
        $(".movebox").css("left", "0");

        $("#switch_signin").click(function() {
          $(".signin").addClass("nodisplay");
          $(".signup").removeClass("nodisplay");
        });

        $("#switch_signup").click(function() {
          $(".signup").addClass("nodisplay");
          $(".signin").removeClass("nodisplay");
        });
      }
    });
    if (window.innerWidth > 767) {
      $("#signup").click(function() {
        $(".movebox").css("left", "45%");
        $(".signin").toggleClass("nodisplay");
        $(".signup").removeClass("nodisplay");
      });

      $("#signin").click(function() {
        $(".movebox").css("left", "5%");
        $(".signup").addClass("nodisplay");
        $(".signin").removeClass("nodisplay");
      });
    } else {
      $(".movebox").css("left", "0");

      $("#switch_signin").click(function() {
        $(".signin").addClass("nodisplay");
        $(".signup").removeClass("nodisplay");
      });

      $("#switch_signup").click(function() {
        $(".signup").addClass("nodisplay");
        $(".signin").removeClass("nodisplay");
      });
    }
  },
  data() {
    return {
      member: {
        acc: "",
        psw: "",
      },
      form: {
        acc: "",
        psw: "",
        rePsw: "",
        mail: "",
        phone: "",
      },
    };
  },
  methods: {
    login: function() {
      const api = "/api/api_memberLogin.php";

      this.$http
        .post(api, JSON.stringify(this.member))
        .then((res) => {
          const data = res.data;

          if (data == "") {
            alert("痾喔，帳號或密碼輸入錯誤。請重新再試一次！");
          } else {
            alert(data.nick + " 您好，歡迎回來！");

            // 1. 傳遞登入狀態到父層
            this.$emit("loginStatus", true);

            // 清除表單
            this.member = { acc: "", psw: "" };
            this.$router.go(-1);
          }
        })
        // eslint-disable-next-line no-console
        .catch((err) => console.log(err));
    },
    changeSignin: function() {
      const form = this.form;

      form.acc = "";
      form.psw = "";
      form.rePsw = "";
      form.mail = "";
    },
    signup: function() {
      const api = "/api/api_memberSignup.php";

      for (let i in this.form) {
        if (this.form[i] == "") {
          alert("請檢查是否所有欄位都有輸入資料");
          return;
        }
      }

      const sexs = document.getElementsByName("gender");

      // eslint-disable-next-line no-console
      console.log(sexs);
      // eslint-disable-next-line no-console
      console.log(this.form);

      // this.$http
      //   .post(api, JSON.stringify(this.form))
      //   .then(res => {
      //     const data = res.data;

      //     if (data == 0) {
      //       alert("註冊完成！");

      //       this.form = {
      //         acc: "",
      //         psw: "",
      //         rePsw: "",
      //         mail: ""
      //       };

      //       $(".movebox").css("transform", "translateX(-10%)");
      //       $(".signup").addClass("nodisplay");
      //       $(".signin").removeClass("nodisplay");
      //     } else if (data == 1) {
      //       alert("此帳號已經被註冊過！");
      //     }
      //   })
      //   // eslint-disable-next-line no-console
      //   .catch(err => console.log(err));
    },
    checkPsw: function() {
      const form = this.form;

      if (form.rePsw != form.psw) {
        document.getElementById("signupPsw").style.backgroundColor = "red";
        document.getElementById("signupRePsw").style.backgroundColor = "red";
      } else {
        document.getElementById("signupPsw").style.backgroundColor = "";
        document.getElementById("signupRePsw").style.backgroundColor = "";
      }
    },
  },
};
</script>
