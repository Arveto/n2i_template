<template>

        <div>
            <Header v-bind:loggedIn="loggedIn" v-bind:userData="userData" @switchView="switchView" @logOff="logOff"/>
            <Home v-bind:view="view" v-show="view == 'Home'"/>
            <Signup v-show="view == 'Signup'" v-bind:socket="socket" @signUp="signUp"/>
            <Login v-show="view == 'Login'"  v-bind:socket="socket" @signIn="signIn"/>
            <Account v-show="view == 'Account'"  v-bind:socket="socket" v-bind:userData="userData"/>
            <Articles v-show="view == 'Articles'" v-bind:socket="socket" v-bind:articles="articles" @switchView="switchView"/>
            <Writer v-show="view == 'Writer'" v-bind:socket="socket" v-bind:userData="userData"/>
            <Reader v-show="view == 'Reader'" v-bind:article="article"/>
            <Messenger v-if="userData.email != ''" v-bind:socket="socket" v-bind:userData="userData"/>

        </div>

</template>



<script>

import io from 'socket.io-client'
let socket = io.connect('localhost:3000');

import Home from '@/components/home/Home'
import Header from '@/components/Header'
import Login from '@/components/Login'
import Signup from '@/components/Signup'
import Account from '@/components/Account'
import Articles from '@/components/articles/List'
import Writer from '@/components/articles/Writer'
import Reader from '@/components/articles/Reader'
import Messenger from '@/components/messenger/Messenger'


var data = {
    view : 'Home',
    socket : socket,
    loggedIn : false,
    userData : {
        fiName: '',
        faName: '',
        pseudo: '',
        email: '',
        admin: 0
    },
    articles: [],
    article: {}
};


export default {
    components: {
        Home, Header, Login, Signup, Account, Articles, Writer, Reader, Messenger
    },

    data () {
        return data;

    },

    mounted() {
            //Update main user data on account edit
        socket.on('faNameEditSuccess', (data) => {
            this.userData.faName = data.faName;
        });
        socket.on('fiNameEditSuccess', (data) => {
            this.userData.fiName = data.fiName;
        });
        socket.on('pseudoEditSuccess', (data) => {
            this.userData.pseudo = data.pseudo;
        });
        socket.on('emailEditSuccess', (data) => {
            this.userData.email = data.email;
        });
        socket.on('passwordEditSuccess', () => {
            console.log("Password updated !");
        });


            //Request articles
        socket.emit('articlesRequest');

        socket.on('articlesRequest', (data) =>{
            this.articles = data;
        });

        socket.on('viewArticle', (data) =>{
            for(let i=0; i< this.articles.length; i++){
                if(this.articles[i].id == data.id){
                    this.article = this.articles[i];
                    this.article.content = data.content;
                    console.log("Article found, updating");
                    break;
                }
            }
        });
    },

    methods : {
            //Methods triggered on components events
        switchView : function(component) {
            this.view = component;
        },

        signIn : function(userData){
            this.loggedIn = true;
            this.userData = userData;
            this.view = 'Home';
        },

        signUp : function(userData){
            this.loggedIn = true;
            this.userData = userData;
            this.view = 'Home';

            console.log(this.userData);
        },

        logOff : function() {
            this.loggedIn = false;

            this.userData = {
                fiName: '',
                faName: '',
                pseudo: '',
                email: ''
            };

            this.view = 'Home';
        }
    }
}

</script>



<style scoped>

.container {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
}

.title {
    font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
    display: block;
    font-weight: 300;
    font-size: 100px;
    color: #35495e;
    letter-spacing: 1px;
}

.subtitle {
    font-weight: 300;
    font-size: 42px;
    color: #526488;
    word-spacing: 5px;
    padding-bottom: 15px;
}

.links {
    padding-top: 15px;
}

</style>
