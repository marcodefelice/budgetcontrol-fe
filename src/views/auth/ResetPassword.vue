<template>
  <div class="container mx-auto px-4 h-full">
    <loading :show="show">
    </loading>
    <div class="flex content-center items-center justify-center h-full">
      <div class="w-full lg:w-6/12 px-4">
        <div class="relative flex flex-col min-w-0 break-words w-full mb-6 shadow-lg rounded-lg bg-slate-200 border-0">
          <div class="rounded-t mb-0 px-6 py-6">
            <div class="text-center mb-3">
              <h6 class="text-slate-500 text-sm font-bold">
                {{ $t('labels.set_new_password') }}
              </h6>
            </div>
            <hr class="mt-6 border-b-1 border-slate-300" />
          </div>
          <div class="flex-auto px-4 lg:px-10 py-10 pt-0">
            <div class="text-slate-400 text-center mb-3 font-bold">
              <form @submit="submit" action="javascript:void(0)">

                <div role="alert" v-if="error">
                  <div class="bg-red-500 text-white font-bold rounded-t px-4 py-2">
                    {{ $t('labels.generic_error') }}
                  </div>
                  <div class="border border-t-0 border-red-400 rounded-b bg-red-100 px-4 py-3 text-red-700">
                    {{ error_message }}
                  </div>
                </div>

                <div class="relative w-full mb-3">
                  <label class="block uppercase text-slate-600 text-xs font-bold mb-2" htmlFor="grid-password">
                    {{ $t('labels.password') }}
                  </label>
                  <input v-model="password" type="password"
                    class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150"
                    placeholder="Password" />
                </div>

                <div class="relative w-full mb-3">
                  <label class="block uppercase text-slate-600 text-xs font-bold mb-2" htmlFor="grid-password">
                    {{ $t('labels.confirm_password') }}
                  </label>
                  <input v-model="confirm_password" type="password"
                    class="border-0 px-3 py-3 placeholder-slate-300 text-slate-600 bg-white rounded text-sm shadow focus:outline-none focus:ring w-full ease-linear transition-all duration-150"
                    placeholder="Confirm Password" />
                    <PasswordStrengthMeter :password="password" ref="passwordStreight" />
                </div>

                <div class="text-center mt-6">
                  <button
                    class="bg-slate-800 text-white active:bg-slate-600 text-sm font-bold uppercase px-6 py-3 rounded shadow hover:shadow-lg outline-none focus:outline-none mr-1 mb-1 w-full ease-linear transition-all duration-150"
                    type="submit">
                    {{ $t('labels.set_new_password') }}
                  </button>

                  <span class="text-slate-400 text-center mt-3 font-bold lowercase">
                    <router-link to="/app/auth/login">
                      {{ $t('labels.back_to_login') }}
                    </router-link>
                  </span>
                  
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import AuthService from "../../services/auth.service";
import loading from 'vue-full-loading'
import PasswordStrengthMeter from "../../components/Auth/PasswordStrengthMeter.vue";

export default {
  components: {
    loading,
    PasswordStrengthMeter,
  },
  data() {
    return {
      show: false,
      error: false,
      error_message: null,
      password: null,
      confirm_password: null
    };
  },
  methods: {
    async submit() {
      const password = this.password;
      const confirm_password = this.confirm_password;
      const token = this.$route.params.token
      const _this = this
      const authService = new AuthService()

      this.show = false
      this.error = false

      authService.resetPassword(token, password, confirm_password).then(() => {
        _this.$router.push({ path: '/app/login' })
      }).catch((err) => {
        _this.show = false
        _this.error = true
        _this.message = false

        _this.error_message = err.response.data.error

        console.debug(err)
      })
    }
  }
};
</script>
