<template>
  <div>
    <v-app-bar
      color="amber"
      height="100px"
    >
      <router-link class="routerLink" to="/main">
        <v-btn icon width="auto" height="auto"  class="ma-1">
          <v-avatar size="90">
          <img src="../assets/logo.png">
          </v-avatar>
        </v-btn>  
      </router-link>
      <v-img src="../assets/comparewise.png" width="100px" height="auto"></v-img>

      <v-spacer></v-spacer>
      
            <!-- <router-link 
              class="routerLink" to="/wishlist"
              v-if="this.$store.state.isLogin"
            >
              <v-btn color="primary" dark v-on="on"  icon class="ma-2" >
                <v-icon x-large>mdi-star-outline</v-icon>
              </v-btn>
            </router-link> -->

      
      <router-link 
        class="routerLink" to="/mypage"
        v-if="this.$store.state.isLogin"
      >
        <v-btn icon class="ma-2">
          <v-icon x-large>mdi-account-outline</v-icon>
        </v-btn>
      </router-link>
      
      <v-btn @click="logout()" v-if="this.$store.state.isLogin" class="ma-2" outlined>
        Logout
      </v-btn>
          
      <router-link 
        class="routerLink" to="/login"
        v-if="!this.$store.state.isLogin"
      >
        <v-btn class="ma-2" outlined>
          Login
        </v-btn>
      </router-link>

      <router-link 
        class="routerLink" to="/signup"
        v-if="!this.$store.state.isLogin"
      >
        <v-btn class="ma-2" outlined>
          Signup
        </v-btn>
      </router-link>
    </v-app-bar>

    <v-container>
      <div class = "block" id = "first">
        <div style = "float: left; width: 20%; text-align: center;">
          <span class="iconify" data-icon="mdi:account-circle-outline" data-inline="false" style="color: rgb(85, 161, 219);" data-width="100px" data-height="100px"></span>
        </div>
        <div style = "font-size: 40px;">{{nick}} </div>
        <div>{{name}} 님, 안녕하세요.
          <div class="my-2" style="float:right;">
            <v-btn small v-on:click="edit">내 정보 수정</v-btn>
          </div>
        </div>
      </div>

      <div class = "block" id = "edit" style = "width: 50%; margin: auto; margin-bottom: 20px; display: none;">
        내 정보 수정<hr><br>
        <form style="padding-left: 100px;">
          <p>이름 : {{name}}</p>
          <p>별명 : {{nick}}</p>
          <p>현재 비밀번호 : <input type="password" v-model="npassword" style="width: 200px; border: 1px solid blue;"></p>
          <p>새 비밀번호 : <input type="password" v-model="form.password" style="width: 200px; border: 1px solid blue;"></p>
        </form>
        
          <div class="my-2" style="margin: auto; width: 15%;">
            <v-btn small v-on:click.prevent="post" >수정하기</v-btn>
          </div>
      </div>

      <div class = "block" id = "second">
        <div style = "float: left; width: 49%;">
          <div><router-link class="routerLink" to="/review">{{review}}</router-link></div><!-- 리뷰 보기 페이지 만들기 -->
          <div>내 리뷰</div>
        </div>
        <div style = "width: 1px; background-color: black; height: 45px; float: left;"></div>
        <div style = "float: left; width: 49%;">
          <div><router-link class="routerLink" to="/wishlist">{{wish_list}}</router-link></div>
          <div>위시리스트</div>
        </div>
      </div>
    </v-container>

    <v-footer class="pa-3">
      <v-spacer></v-spacer>
      <div>&copy; {{ new Date().getFullYear() }}</div>
    </v-footer>
  </div>
</template>

<script>
import firebase from 'firebase';
import 'expose-loader?$!expose-loader?jQuery!jquery'
import { eventBus } from "../main"
export default {
  
  data() {
    return {
          form: {
                password: '',
          },
          password:"",
          npassword:"",
          email: "",
          nick: "",
          name: "",
          wish_list: 0,
          review: 0,
          userkey: '',
        }
         
  },
  
  methods: {
    getCookie(name)
        {
          var value = document.cookie.match('(^|;) ?' + name + '=([^;]*)(;|$)');
          return value? value[2] : null;
        },
        logout () {
          this.deleteCookie("name")
          this.deleteCookie("pw")
          this.deleteCookie("nick")
          this.deleteCookie("email")
          this.$store.commit('loginFalse')
        },
        post: function() {
            console.log(this.form)
            if (!this.npassword || !this.form.password){
              alert('Error: You have some missing input. Please check again.');
            }
            else if (this.npassword != this.password){
              alert('현재 비밀번호가 일치하지 않습니다.');
            }
            else {
                console.log(this.userkey)
                this.$http.put('https://comparewise.firebaseio.com/user/'+this.userkey+'.json', {"email": this.email, "name": this.name, "nickname": this.nick, "password": this.form.password});
                alert('비밀번호가 성공적으로 변경되었습니다.\n변경된 비밀번호로 로그인 해주세요.');
                // logout();
            }
          },
          edit(){
            var now = document.getElementById("edit");
            if (now.style.display == "block"){
              document.getElementById("edit").style.display = "none"
            }
            else if (now.style.display == "none"){
              document.getElementById("edit").style.display = "block"
            }
          },
  },
  mounted() {
      let recaptchaScript = document.createElement('script')
      recaptchaScript.setAttribute('src', 'https://code.iconify.design/1/1.0.6/iconify.min.js')
      document.head.appendChild(recaptchaScript)
    },
  created() {
    eventBus.$on('loginTrue', isTrue => {
      this.$store.commit('loginTrue')
    })
    var email = this.getCookie("email")//함수 보완해서 수정 중
    var nick = this.getCookie("nick")//함수 보완해서 수정 중
    var name = this.getCookie("name")//함수 보완해서 수정 중
    var password = this.getCookie("pw")//함수 보완해서 수정 중
    console.log(email)
    this.name=name;
    this.email=email;
    this.password=password; 
    this.nick=nick; 
    console.log(name)
    this.$http.get('https://comparewise.firebaseio.com/user.json').then(function(data){
                return data.json();
            }).then(function(data){
                for (let key in data) {
                  if (data[key]==null)
                  {
                    continue;
                  }
                  if(data[key].email==this.email)
                  {
                    this.userkey = key;
                    break;
                  }
                }
            });
    this.$http.get('https://comparewise.firebaseio.com/WishList.json').then(function(data){
                return data.json();
            }).then(function(data){
                for (let key in data) {
                  if (data[key]==null)
                  {
                    continue;
                  }
                  if(data[key].user_id==this.email)
                  {
                    this.wish_list=this.wish_list+1;
                  }
                }
            });
            this.$http.get('https://comparewise.firebaseio.com/Review.json').then(function(data){
                return data.json();
            }).then(function(data){
                for (let key in data) {
                  if (data[key]==null)
                  {
                    continue;
                  }
                  if(data[key].user_id==this.email)
                  {
                    this.review=this.review+1;
                  }
                }
            });
  }
 /*admin.auth().verifyIdToken(idToken)
  .then(function(decodedToken) {
    let uid = decodedToken.uid;
    // ...
  }).catch(function(error) {
    // Handle error
  });*/
}
</script>

<style scoped>
  .block{
    width: 100%;
    background-color:#E8EAF6;
    margin-top:10px;
    margin-bottom:20px;
    padding:10px;
    height: auto;
    overflow: hidden;
  }
  .block#second{
    text-align: center;
  }
</style>