<template lang="html">

  <div>
    <!-- <link rel="stylesheet" href="https://unpkg.com/bulmaswatch/darkly/bulmaswatch.min.css"> -->
        <div class="hero-body">
            <div class="container has-text-centered">
                <div class="column is-4 is-offset-4">
                    <h3 class="title has-text-grey">Sign up</h3>
                    <p class="subtitle has-text-grey">Join the army!</p>
                    <div class="box">
                        <figure class="avatar image is-96x96">
                            <img class="is-rounded" src="https://api.adorable.io/avatars/285/test.png">
                        </figure><br>
                        <form>

                            <div class="field">
                                <div class="control">
                                    <label class="label">Prénom</label>
                                    <input class="input" v-model="fiName" placeholder="John">
                                </div>
                            </div>

                            <div class="field">
                                <div class="control">
                                    <label class="label">Nom</label>
                                    <input class="input" v-model="faName" placeholder="Doe">
                                </div>
                            </div>

                            <div class="field">
                                <div class="control">
                                    <label class="label">Pseudo (optionnel)</label>
                                    <input class="input" v-model="pseudo" placeholder="xXx_DarkSasuke78_xXx" autofocus="">
                                </div>
                            </div>

                            <div class="field">
                                <div class="control">
                                    <label class="label">E-mail</label>
                                    <input class="input" v-model="email" placeholder="john.doe@arveto.io" autofocus="">
                                </div>
                            </div>

                            <div class="field">
                                <div class="control">
                                    <label class="label">Password</label>
                                    <input class="input" v-model="password1" type="password" placeholder="12345678">
                                </div>
                            </div>

                            <div class="field">
                                <div class="control">
                                    <label class="label">Password (confirm)</label>
                                    <input class="input" v-model="password2" type="password" placeholder="12345678">
                                </div>
                            </div>


                        </form>

                        <button v-on:click="submit" id="submitButton" class="button is-block is-link is-large is-fullwidth">Create account</button>

                    </div>
                    <p class="has-text-grey">
                        <a href="../"> Sign In </a>
                        <a href="../"> Need Help? </a>
                    </p>
                </div>
            </div>
        </div>

        <Help v-bind:tip="'Vous inscrire vous donnera accès à de nombreuses fonctionnalités supplémentaires'"/>
    </div>
  </div>

</template>



<script>

var data   = {
    fiName: '',
    faName: '',
    pseudo: '',
    email: '',
    password1: '',
    password2: '',
    password: ''   //Crypted version
};

import Help from '@/components/Help'

export default {

    head () {
      return {
        script: [
          { src: 'https://cdnjs.cloudflare.com/ajax/libs/jsSHA/2.3.1/sha.js' }
        ]
      }
    },

    data () {
        return data;
    },

    props: ['socket'],

    components: {Help},


    created(){
            //Events listeners
        this.socket.on('signUpSuccess', (userData) => {
            //Do your stuff
            console.log("Signup succesfull");
            this.$emit('signUp', userData);
        });

        this.socket.on('signUpFailure', () => {
            //Do your stuff
            console.log("Signup failed");
        });
    },

    methods: {
        submit: function(){

            let emailRegex =  /^([a-zA-Z0-9_\.\-])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,4})+$/;

            if(this.fiName != '' && this.faName != ''
            && this.email != '' && emailRegex.test(this.email)
            && this.password1 == this.password2 && this.password1 != ''){

                let shaObj = new jsSHA("SHA-512", "TEXT");
                shaObj.update(this.password1);
                var hash = shaObj.getHash("HEX");

                let user = {
                    faName : this.faName,
                    fiName : this.fiName,
                    pseudo : this.pseudo,
                    email : this.email,
                    password : hash,
                }

                this.socket.emit('signUp', user);
            }
            else{
                //Error management
            }

        }
    }

}

</script>



<style lang="css" scoped>

.avatar{
    margin-left: auto;
    margin-right: auto;
}

#submitButton{
    margin-top: 25px;
}

</style>
