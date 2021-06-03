<template>
  <div class="container pt-7 pb-5">
    <div class="row justify-content-center">
      <div class="col-lg-6 col-md-7">
        <div class="card bg-secondary border-0 mb-0">
          <div class="card-body px-lg-5 py-lg-5">
            <div class="navbar-brand text-center ml-6 mb-3">
              <img src="../../public/assets/img/brand/logo3.png" width="300">
            </div>
            <div class="text-muted mb-4">
              <small>Masukan username dan password anda!</small>
            </div>

            <form v-on:submit.prevent="Login">
              <div class="form-group mb-3">
                <div
                  class="input-group input-group-merge input-group-alternative"
                >
                  <div class="input-group-prepend">
                    <span class="input-group-text"
                      ><i class="ni ni-email-83"></i
                    ></span>
                  </div>
                  <input
                    class="form-control"
                    placeholder="Username"
                    type="text"
                    v-model="username"
                  />
                </div>
              </div>
              <div class="form-group">
                <div
                  class="input-group input-group-merge input-group-alternative"
                >
                  <div class="input-group-prepend">
                    <span class="input-group-text"
                      ><i class="ni ni-lock-circle-open"></i
                    ></span>
                  </div>
                  <input
                    class="form-control"
                    placeholder="Password"
                    type="password"
                    v-model="password"
                  />
                </div>
              </div>
              <div class="text-left">
                <small class="text-red">{{ message }}</small>
              </div>

              <div class="text-center">
                <button type="submit" class="btn btn-success btn-block my-4">
                  Login
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
    data() {
      return {
        username: '',
        password: '',
        message: '',
      }
    },
    methods: {
        Login: function(){
            this.message = "Mohon tunggu..."
            let username = this.username 
            let password = this.password
            this.$store.dispatch('login', { username, password })
            .then((response) => {
                this.message = response.data.message
                if(response.data.success == true){
                    this.$router.push('/petugas')
                }
            })
            .catch(err => console.log(err))
        }
    },
    mounted(){
        //jika posisi sudah login tidak bisa menampilkan halaman login lagi
        window.onpopstate = event => {
        if (
            window.localStorage.getItem("Authorization") !== null &&
            this.$route.path == "/login"
        ) {
            this.$router.push("/petugas"); // redirect to home, for example
        }
        };
    }
}
</script>