<template>
  <div class="main">
      <DragBar></DragBar>
      <div class="title-container">
          <p class="title">Sign in to Trimm Desktop</p>
      </div>
      <div class="login-form">
          <form @submit.prevent="login">
              <p class="login-info">Email</p>
              <input placeholder="example@example.com" v-model="form.email" class="login-input hover-pointer">
              <p class="error">{{ errors.email }}</p>
              <p class="login-info">Password</p>
              <input type="password" placeholder="password" v-model="form.password" class="login-input hover-pointer">
              <p class="error">{{ errors.password }}</p>
              <button type="submit" class="login-submit hover-pointer">Sign in</button>
          </form>
      </div>
  </div>
</template>

<script>
import DragBar from '@/components/DragBar'
import os from 'os'
import storage from 'electron-json-storage'
import axios from 'axios'

export default {
    name: 'landing-page',
    components: {
        DragBar
    },
    data() {
        return {
            form: {
                email: '',
                password: '',
            },
            errors: {
                email: '',
                password: ''
            }
        }
    },
    created() {
        var self = this
        storage.get('auth-token', function(error, data) {
            if (data['token']) {
                axios.post(process.env.HOST_URL + 'api/check-authCode', {
                    params: {
                        code: data['token']
                    }
                }).then(function(response) {
                    console.log(response)
                    if (response.data['success']) {
                        // Redirect
                        self.$router.push({
                            path: 'home'
                        })
                    } else {
                        console.log('Key did not match')
                    }
                })
            } else {
                console.log('user does not have token saved, must log in')
            }
        })
    },
    methods: {
        login() {
            // Reset errors
            this.errors.email = ''
            this.errors.password = ''

            var self = this
            axios.post(process.env.HOST_URL + 'api/auth', {
                //headers: {"Access-Control-Allow-Origin": "*"},
                params: {
                    'username': this.form.email,
                    'password': this.form.password
                }
            }).then(function(response) {
                if (response.data['success']) {
                    // Auth code was successful, send back the exchange code
                    axios.post(process.env.HOST_URL + 'api/verify-code', {
                        headers: {"Access-Control-Allow-Origin": "*"},
                        params: {
                            'code': response.data['key'],
                            'userFirstFour': self.form.email.substring(0, 4)
                        }
                    }).then(function(response) {
                        // Final key returned
                        if (response.data['success']) {
                            self.processKey(response.data['final_key'])
                        } else {
                            self.errors.email = "Exchange code did not match"
                        }
                    })
                } else {
                    self.errors.email = 'Incorrect username/password'
                }
            })
        },
        processKey(key) {
            // Set key in local storage
            storage.set('auth-token', { token: key}, function(error) {
                if (error) {
                    throw error
                }
            })

            // Redirect
            this.$router.push({
                path: 'home/',
                code: key
            })
        },
        open (link) {
            this.$electron.shell.openExternal(link)
        },
    }
}
</script>

<style>
    @import url('https://fonts.googleapis.com/css?family=Nunito:300,400,600,700');

    body {
        background-color: #20176f;
        padding: none;
        margin: 0;
        font-family: 'Nunito', sans-serif;
    }

    .title-container {

    }

    .title {
        color: white;
        margin-top: 10px;
        margin-left: 5%;
        font-size: 20px;
        font-weight: 600;
    }

    .error {
        font-size: 10px;
        color: pink;
        margin-left: 5%;
        padding: 0;
    }

    .login-form {
        width: 100%;
        /* padding: 5px; */
    }

    .login-info {
        font-size: 16px;
        color: white;
        margin: 0;
        margin-left: 5%;
        margin-bottom: 5px;
        font-size: 19px;
        font-weight: 600;
    }

    .login-input {
        margin: auto;
        margin-bottom: 10px;
        display: block;
        width: calc(90% - 10px);
        height: 30px;
        background-color: #392c9f;
        border-radius: 4px;
        border:none;
        color: white;
        box-shadow: 0 0 0 1px rgba(49,49,93,.03), 0 2px 5px 0 rgba(49,49,93,.1), 0 1px 2px 0 rgba(0,0,0,.08);
        padding: 0px 5px;
        font-weight: 600;
        font-size: 13px;
    }

    .login-input::placeholder {

    }

    .login-submit {
        margin-left: 5%;
        margin-top: 10px;
        background: none;
        border: none;
        color: white;
        background-color: #614cdb;
        padding: 10px 15px;
        border-radius: 4px;
        font-size: 12px;
        font-weight: 600;
    }

    .hover-pointer {
        cursor: pointer;
    }


</style>
