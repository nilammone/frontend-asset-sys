<template>
  <div>
    <div class="limiter">
      <div class="container-login100">
        <div class="wrap-login100">
          <!-- <div
            class="login100-form-title"
            style="background-image: url(bg-01.jpg)"
          > -->
          <!-- <span class="login100-form-title-1"> Login Asset Sys </span> -->
          <!-- </div> -->
          <div style="margin-top: 30px">
            <v-img
              src="/assetlogo.png"
              max-height="320"
              max-width="420"
              style="display: block; margin-left: auto; margin-right: auto"
            ></v-img>
          </div>

          <div
            style="
              text-align: center;
              margin-top: 20px;
              color: #808080;
              font-family: NotoSerifLao-Regular, Roboto-Regular;
            "
          >
            <h3><u> ລົງທະບຽນຜູ້ໃຊ້ງານລະບົບ </u></h3>
          </div>

          <form class="login100-form validate-form" @submit.prevent="submit">
            <!-- <div
              class="wrap-input100 validate-input m-b-26"
              data-validate="first_name is required"
            >
              <span class="label-input100">First_name</span>
              <input
                class="input100"
                type="text"
                v-model="first_name"
                placeholder="Enter first_name"
              />
              <span class="focus-input100"></span>
            </div> -->

            <div
              class="wrap-input100 validate-input m-b-26"
              data-validate="username is required"
            >
              <span class="label-input100">ຊື່ຜູ້ໃຊ້ງານ</span>
              <input
                class="input100"
                type="text"
                v-model="username"
                required
                placeholder="ປ້ອນຊື່ຜູ້ໃຊ້ງານ"
              />
              <span class="focus-input100"></span>
            </div>

            <div
              class="wrap-input100 validate-input m-b-26"
              data-validate="email is required"
            >
              <span class="label-input100">ອີເມວ</span>
              <input
                class="input100"
                type="email"
                required
                v-model="email"
                placeholder="ປ້ອນອີເມວ"
              />
              <span class="focus-input100"></span>
              <span style="font-size: 15px; color: red" v-if="msg.email">{{
                msg.email
              }}</span>
            </div>

            <div
              class="wrap-input100 validate-input m-b-18"
              data-validate="Password is required"
            >
              <span class="label-input100">ລະຫັດຜ່ານ</span>
              <input
                class="input100"
                type="password"
                required
                v-model="password"
                placeholder="ປ້ອນລະຫັດຜ່ານ"
              />
              <span class="focus-input100"></span>
            </div>

            <div
              class="wrap-input100 validate-input m-b-26"
              data-validate="employee_name is required"
            >
              <span class="label-input100">ເລືອກພະນັກງານ</span>
              <!-- <input
                class="input100"
                type="text"
                v-model="empname"
                required
                placeholder="Enter Employee Name"
              /> -->
              <select class="input100select" v-model="empname">
                <option
                  v-for="item in itemsemp"
                  :key="item.emp_id"
                  :value="item.emp_id"
                >
                  {{ item.emp_firstname }}
                </option>
              </select>
              <span class="focus-input100"></span>
            </div>

            <div
              class="wrap-input100 validate-input m-b-26"
              data-validate="employee_name is required"
            >
              <span class="label-input100">ເລືອກສະຖານະ</span>
              <!-- <input
                class="input100"
                type="text"
                v-model="empname"
                required
                placeholder="Enter Employee Name"
              /> -->
              <select class="input100select" v-model="status">
                <option
                  v-for="itemstatus in itemsstatus"
                  :key="itemstatus.id"
                  :value="itemstatus.id"
                >
                  {{ itemstatus.text }}
                </option>
              </select>
              <span class="focus-input100"></span>
            </div>

            <!-- <div
              class="wrap-input100 validate-input m-b-18"
              data-validate="password_confirm is required"
            >
              <span class="label-input100">Pass_confirm</span>
              <input
                class="input100"
                type="password"
                v-model="password_confirm"
                placeholder="Enter password_confirm"
              />
              <span class="focus-input100"></span>
            </div> -->

            <div class="flex-sb-m w-full p-b-30">
              <div class="contact100-form-checkbox">
                <input
                  class="input-checkbox100"
                  id="ckb1"
                  type="checkbox"
                  name="remember-me"
                />
                <!-- <label class="label-checkbox100" for="ckb1">
                  Remember me
                </label> -->
              </div>

              <!-- <div>
                <a href="#" class="txt1"> Forgot Password? </a>
              </div> -->
            </div>

            <div class="container-login100-form-btn" style="margin-top: 20px">
              <button class="login100-form-btn">ລົງທະບຽນ</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  layout: 'default_login',
  auth: false,

  data: () => ({
    // first_name: '',
    username: '',
    email: '',
    msg: [],
    password: '',
    empname: '',
    status: '',
    // password_confirm: '',
    valid: true,
    name: '',
    itemsemp: [],
    itemsstatus: [
      { id: 1, text: 'admin' },
      { id: 2, text: 'user' },
    ],
    nameRules: [
      (v) => !!v || 'Name is required',
      (v) => (v && v.length <= 10) || 'Name must be less than 10 characters',
    ],
    emailRules: [
      (v) => !!v || 'E-mail is required',
      (v) => /.+@.+\..+/.test(v) || 'E-mail must be valid',
    ],
    select: null,
    items: ['Item 1', 'Item 2', 'Item 3', 'Item 4'],
    checkbox: false,
  }),

  created() {
    // console.log("I'm created!")
    this.getDataEmployees()
  },

  watch: {
    email(value) {
      // binding this to the data value in the email input
      this.email = value
      this.validateEmail(value)
    },
  },

  methods: {
    async submit() {
      try {
        await axios
          .get(
            `http://localhost:8000/api/register?username=${this.username}&email=${this.email}&password=${this.password}&user_emp_id=${this.empname}&status=${this.status}`
          )
          .then((response) => {
            this.$router.push('/login')
          })
          .catch((error) => console.log(error))
      } catch (err) {
        console.log(err)
      }
    },

    async getDataEmployees() {
      try {
        await this.$axios.get('/employees').then((res) => {
          this.itemsemp = res.data
        })
      } catch (err) {
        console.log(err)
      }
    },

    // validate email
    validateEmail(value) {
      if (/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/.test(value)) {
        this.msg['email'] = ''
      } else {
        this.msg['email'] = 'Please Enter email: ex@mail.com!'
      }
    },
  },
}
</script>

<style scope>
* {
  margin: 0px;
  padding: 0px;
  box-sizing: border-box;
}

body,
html {
  height: 100%;
}

/*---------------------------------------------*/
a {
  font-family: NotoSerifLao-Regular, Roboto-Regular;
  font-size: 14px;
  line-height: 1.7;
  color: #666666;
  margin: 0px;
  transition: all 0.4s;
  -webkit-transition: all 0.4s;
  -o-transition: all 0.4s;
  -moz-transition: all 0.4s;
}

a:focus {
  outline: none !important;
}

a:hover {
  text-decoration: none;
  color: #57b846;
}

/*---------------------------------------------*/
h1,
h2,
h3,
h4,
h5,
h6 {
  margin: 0px;
}

p {
  font-size: 14px;
  line-height: 1.7;
  color: #666666;
  margin: 0px;
}

ul,
li {
  margin: 0px;
  list-style-type: none;
}

/*---------------------------------------------*/
input {
  outline: none;
  border: none;
}

input[type='number'] {
  -moz-appearance: textfield;
  appearance: none;
  -webkit-appearance: none;
}

input[type='number']::-webkit-outer-spin-button,
input[type='number']::-webkit-inner-spin-button {
  -webkit-appearance: none;
}

textarea {
  outline: none;
  border: none;
}

textarea:focus,
input:focus {
  border-color: transparent !important;
}

input:focus::-webkit-input-placeholder {
  color: transparent;
}
input:focus:-moz-placeholder {
  color: transparent;
}
input:focus::-moz-placeholder {
  color: transparent;
}
input:focus:-ms-input-placeholder {
  color: transparent;
}

textarea:focus::-webkit-input-placeholder {
  color: transparent;
}
textarea:focus:-moz-placeholder {
  color: transparent;
}
textarea:focus::-moz-placeholder {
  color: transparent;
}
textarea:focus:-ms-input-placeholder {
  color: transparent;
}

input::-webkit-input-placeholder {
  color: #999999;
}
input:-moz-placeholder {
  color: #999999;
}
input::-moz-placeholder {
  color: #999999;
}
input:-ms-input-placeholder {
  color: #999999;
}

textarea::-webkit-input-placeholder {
  color: #999999;
}
textarea:-moz-placeholder {
  color: #999999;
}
textarea::-moz-placeholder {
  color: #999999;
}
textarea:-ms-input-placeholder {
  color: #999999;
}

label {
  display: block;
  margin: 0;
}

/*---------------------------------------------*/
button {
  outline: none !important;
  border: none;
  background: transparent;
}

button:hover {
  cursor: pointer;
}

iframe {
  border: none !important;
}

/*//////////////////////////////////////////////////////////////////
[ Utility ]*/
.txt1 {
  font-size: 13px;
  line-height: 1.4;
  color: #999999;
}

/*//////////////////////////////////////////////////////////////////
[ login ]*/

.limiter {
  width: 100%;
  margin: 0 auto;
}

.container-login100 {
  width: 100%;
  min-height: 100vh;
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
  padding: 15px;
}

.wrap-login100 {
  width: 670px;
  background: #fff;
  border-radius: 10px;
  overflow: hidden;
  position: relative;
}

/*==================================================================
[ Title form ]*/
.login100-form-title {
  width: 100%;
  position: relative;
  z-index: 1;
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  flex-wrap: wrap;
  flex-direction: column;
  align-items: center;

  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;

  padding: 70px 15px 74px 15px;
}

.login100-form-title-1 {
  font-size: 30px;
  color: #fff;
  text-transform: uppercase;
  line-height: 1.2;
  text-align: center;
}

.login100-form-title::before {
  content: '';
  display: block;
  position: absolute;
  z-index: -1;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  background-color: rgba(54, 84, 99, 0.7);
}

/*==================================================================
[ Form ]*/

.login100-form {
  width: 100%;
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  padding: 43px 88px 93px 190px;
}

/*------------------------------------------------------------------
[ Input ]*/

.wrap-input100 {
  width: 100%;
  position: relative;
  border-bottom: 1px solid #b2b2b2;
}

.label-input100 {
  font-family: NotoSerifLao-Regular, Roboto-Regular;
  font-size: 15px;
  color: #808080;
  line-height: 1.2;
  text-align: right;

  position: absolute;
  top: 14px;
  left: -105px;
  width: 80px;
}

/*---------------------------------------------*/
.input100 {
  font-family: NotoSerifLao-Regular, Roboto-Regular;
  font-size: 15px;
  color: #555555;
  line-height: 1.2;

  display: block;
  width: 100%;
  background: transparent;
  padding: 0 5px;
}

.input100select {
  font-family: NotoSerifLao-Regular, Roboto-Regular;
  font-size: 15px;
  color: #555555;
  line-height: 2;

  display: block;
  width: 100%;
  background: transparent;
  padding: 0 5px;
}

.focus-input100 {
  position: absolute;
  display: block;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
}

.focus-input100::before {
  content: '';
  display: block;
  position: absolute;
  bottom: -1px;
  left: 0;
  width: 0;
  height: 1px;

  -webkit-transition: all 0.6s;
  -o-transition: all 0.6s;
  -moz-transition: all 0.6s;
  transition: all 0.6s;

  background: #57b846;
}

/*---------------------------------------------*/
input.input100 {
  height: 45px;
}

.input100:focus + .focus-input100::before {
  width: 100%;
}

.has-val.input100 + .focus-input100::before {
  width: 100%;
}

/*==================================================================
[ Restyle Checkbox ]*/

.input-checkbox100 {
  display: none;
}

.label-checkbox100 {
  font-family: NotoSerifLao-Regular, Roboto-Regular;
  font-size: 13px;
  color: #999999;
  line-height: 1.4;

  display: block;
  position: relative;
  padding-left: 26px;
  cursor: pointer;
}

.label-checkbox100::before {
  content: '\f00c';
  font-family: FontAwesome;
  font-size: 13px;
  color: transparent;

  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  width: 18px;
  height: 18px;
  border-radius: 2px;
  background: #fff;
  border: 1px solid #e6e6e6;
  left: 0;
  top: 50%;
  -webkit-transform: translateY(-50%);
  -moz-transform: translateY(-50%);
  -ms-transform: translateY(-50%);
  -o-transform: translateY(-50%);
  transform: translateY(-50%);
}

.input-checkbox100:checked + .label-checkbox100::before {
  color: #57b846;
}

/*------------------------------------------------------------------
[ Button ]*/
.container-login100-form-btn {
  width: 100%;
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  flex-wrap: wrap;
}

.login100-form-btn {
  display: -webkit-box;
  display: -webkit-flex;
  display: -moz-box;
  display: -ms-flexbox;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 20px;
  min-width: 160px;
  height: 50px;
  background-color: #57b846;
  border-radius: 25px;

  font-family: NotoSerifLao-Regular, Roboto-Regular;
  font-size: 16px;
  color: #fff;
  line-height: 1.2;

  -webkit-transition: all 0.4s;
  -o-transition: all 0.4s;
  -moz-transition: all 0.4s;
  transition: all 0.4s;
}

.login100-form-btn:hover {
  background-color: #333333;
}

/*------------------------------------------------------------------
[ Responsive ]*/

@media (max-width: 576px) {
  .login100-form {
    padding: 43px 15px 57px 117px;
  }
}

@media (max-width: 480px) {
  .login100-form {
    padding: 43px 15px 57px 15px;
  }

  .label-input100 {
    text-align: left;
    position: unset;
    top: unset;
    left: unset;
    width: 100%;
    padding: 0 5px;
  }
}

/*------------------------------------------------------------------
[ Alert validate ]*/

.validate-input {
  position: relative;
}

.alert-validate::before {
  content: attr(data-validate);
  position: absolute;
  max-width: 70%;
  background-color: #fff;
  border: 1px solid #c80000;
  border-radius: 2px;
  padding: 4px 25px 4px 10px;
  top: 50%;
  -webkit-transform: translateY(-50%);
  -moz-transform: translateY(-50%);
  -ms-transform: translateY(-50%);
  -o-transform: translateY(-50%);
  transform: translateY(-50%);
  right: 2px;
  pointer-events: none;

  font-family: NotoSerifLao-Regular, Roboto-Regular;
  color: #c80000;
  font-size: 13px;
  line-height: 1.4;
  text-align: left;

  visibility: hidden;
  opacity: 0;

  -webkit-transition: opacity 0.4s;
  -o-transition: opacity 0.4s;
  -moz-transition: opacity 0.4s;
  transition: opacity 0.4s;
}

.alert-validate::after {
  content: '\f06a';
  font-family: FontAwesome;
  display: block;
  position: absolute;
  color: #c80000;
  font-size: 15px;
  top: 50%;
  -webkit-transform: translateY(-50%);
  -moz-transform: translateY(-50%);
  -ms-transform: translateY(-50%);
  -o-transform: translateY(-50%);
  transform: translateY(-50%);
  right: 8px;
}

.alert-validate:hover:before {
  visibility: visible;
  opacity: 1;
}

@media (max-width: 992px) {
  .alert-validate::before {
    visibility: visible;
    opacity: 1;
  }
}
</style>
