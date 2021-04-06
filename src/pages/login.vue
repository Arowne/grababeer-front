<template>
  <q-layout>
    <q-page-container>
      <q-page class="flex flex-center" style="font-family: Lato;">
<!--        <div id="particles-js"></div>-->
        <q-card :style="$q.platform.is.desktop ? 'width:55%;' : ''" class="row my-card items-center q-pa-none q-ma-none shadow-24">
          <q-card-section v-if="$q.platform.is.desktop" class="col-md-4 col-lg-4 col-sm-12 sol-xs-12 items-center float-left" style="background-color: #08a04b"
                          :style="{'height':(win_height-270)+'px'}" horizontal>
            <div class="text-center full-width">
              <div><img src="images/logo.png" style="width: 33%"/></div>
              <div class="text-weight-bolder text-white text-h6"> It's Friday</div>
              <div class="text-caption text-white"> It's Friday - My beer choosing app</div>
            </div>
          </q-card-section>
          <q-card-section class="col-md-8 col-lg-8 col-sm-12 sol-xs-12 float-left">
            <q-card-section class="items-center">
              <div>
                <div v-if="!$q.platform.is.desktop" class="text-weight-bolder text-center q-mb-md text-primary text-h6"> It's Friday</div>
                <q-form :style="$q.platform.is.desktop ? 'width:55%;margin: auto;' : 'margin: auto;'" class="q-gutter-md">
                  <span class="text-subtitle1 text-weight-bold text-grey-7">Welcome Aboard</span>
                  <div class="w-100 d-none pl-0 mt-4" :class="[errors.detail[0] != '' ? 'd-block' : '']">
                    <div class="alert alert-danger pl-0 text-center">{{errors.detail[0]}}</div>
                  </div>
                  <div class="w-100 d-none pl-0 mt-4"
                    :class="[errors.username[0] != '' ? 'd-block' : '']">
                    <div class="alert alert-danger pl-0 text-center">{{errors.username[0]}}</div>
                  </div>
                  <q-input
                    dense
                    outlined
                    v-model="username"
                    label="Username"
                    lazy-rules
                  />
                  <div class="w-100 d-none pl-0 mt-4"
                    :class="[errors.password[0] != '' ? 'd-block' : '']">
                    <div class="alert alert-danger pl-0 text-center">{{errors.password[0]}}</div>
                  </div>
                  <q-input
                    dense
                    type="password"
                    outlined
                    v-model="password"
                    label="Password"
                    lazy-rules
                  />
                  <div>
                    <q-btn class="text-capitalize" size="sm" style="width:75px" dense label="Login" @click="login" type="button"
                           color="primary"/>
                    <q-btn class="float-right text-blue-9 text-capitalize" size="sm" style="width:75px;border: 1px solid #36669e;" dense label="Sign Up"
                           type="button"/>
                  </div>
                </q-form>

              </div>
            </q-card-section>
          </q-card-section>

        </q-card>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script>
    import axios from 'axios'
    export default {
        data() {
            return {
                username: '',
                password: '',
                errors: {
                  username: [],
                  password: [],
                  detail: [],
                }
            }
        },
        mounted() {
        },
        methods: {
          clearErrorMessage () {
            for (const key in this.errors) {
              this.errors[key] = [""];
            }
          },
          login() {
              this.clearErrorMessage();
              const user = new FormData();
              const key = [
                "username",
                "password"
              ];
              const value = [
                this.username,
                this.password
              ];
              for (let i = 0; i < value.length; i++) {
                user.append(key[i], value[i]);
              }
              // Post value
              axios
                .post("http://localhost:9000/token", user)
                .then(res => {
                  localStorage.setItem('aic-session-token', res.data.access);
                  localStorage.setItem('aic-refresh-token', res.data.refresh);
                  this.$router.push('/home');
                })
                .catch(error => {
                  console.log(error);
                  // Clear old message
                  for (const key in error.response.data) {
                    if (key == "detail") {
                      this.errors['detail'] = [error.response.data[key]];
                    }
                    this.errors[key] = error.response.data[key];
                  }
                  console.log(this.errors.username)
                });
          }
        },
        computed: {
            win_width() {
                return this.$q.screen.width - 59;
            },
            win_height() {
                return this.$q.screen.height - 0;
            }
        }
    }
</script>
<style>
  .my-card {
    /*width: 55%;*/
    height: 30%;
  }

  #particles-js {
    position: absolute;
    width: 100%;
    height: 100%;
    /*background: linear-gradient(145deg, #abbaab 15%, #ffffff 70%);*/
    /*background: linear-gradient(145deg,#f7f8f8 11%, #627e79 75%);*/
    background-repeat: no-repeat;
    background-size: cover;
    background-position: 50% 50%;
  }

  .login-form {
    position: absolute;
  }
  .alert-danger {
    color: 'red'
  }
</style>
