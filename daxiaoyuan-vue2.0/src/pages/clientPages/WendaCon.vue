<template>
  <div class="body wrapper">
    <nav-header title="问答详情" :back="true"></nav-header>
    <div class="que-con-box wrapper main top-bar padding-10-10">
			<h4>{{que_title}}</h4>
	    <article class="ans_txt">
	    		{{que_con}}
	    </article>
    </div>
		<div class="padding-20" style="margin-top:1rem" v-if="ans_userId">最佳答案</div>
		<div class="padding-20" style="margin-top:1rem" v-if="!ans_userId">没有最佳答案</div>
    <div class="ans-con-box wrapper main" v-if="ans_userId">
    	<div class="logo-top">
	    	<router-link :to="{name:'darenmsg',params:{userId:ans_userId}}" class="user-logo">
	    		<img :src="ans_user_logo" alt="" class="fullsrc">
	    	</router-link>
	    	<div class="u-name float-left">{{ans_username}} 的回答</div>
	    </div>
	    <article class="ans_txt" v-if="!is_voice">
	    		{{ans_con}}
	    </article>
	    <voice-play :ans-time="ans_time" v-if="is_voice===1"></voice-play>
	    <div class="bottom">
				<span class="fav">{{fav_num}}人觉得很赞</span>
				<i class="iconfont fav-logo" :style="{color:favColor}" @click="fav">&#xe668;</i>
			</div>
    </div>
  </div>
</template>

<script>
import NavHeader from '../../components/NavHeader'
import VoicePlay from '../../components/VoicePlay'
export default {
  name: 'wendacon',
  components:{
  	NavHeader,VoicePlay
  },
  data () {
    return {
			que_title:'',
      que_con:'',
			ans_user_logo:'',
			ans_username:'',
      is_voice:'',
			voice_src:'',
			ans_con:'',
			ans_userId:'',
			favColor:'',
			fav_num:'',
			ans_id:''
    }
  },
  mounted(){
		this.getQue();
		this.getMsg();
  },
  methods:{
  	fav(){
			$.ajax({
				url:'/api/setfav',
				type:'post',
				dataType:'json',
				data:{
					token:window.localStorage.token,
					ans_id:this.ans_id,
					fav_type:this.is_fav?0:1,
					userId:window.localStorage.userId
				},
				success: data => {
					if(this.is_fav == 1){
						this.fav_num--;
						this.is_fav = 0;
						this.favColor="#000";
					}else{
						this.fav_num++;
						this.is_fav = 1;
						this.favColor="red";
					}
				},
				error: err => {
					console.error(err);
				}
			})
  	},
		getMsg(){
			$.ajax({
				url:'/api/getClassics/que',
				type:'get',
				dataType:'json',
				cache:'true',
				crossDomain:true,
				data:{
					que_id:this.$route.params.id,
					userId:window.localStorage.userId
				},
				success:(data) => {
					let datas = data.data;
					this.ans_user_logo = datas.user_logo;
					this.ans_username = datas.username;
					this.ans_userId = datas.userId;
					this.is_voice = datas.is_voice;
					this.voice_src = datas.voice_src;
					this.ans_con = datas.ans_con;
					this.fav_num = datas.fav_num;
					this.is_fav = datas.is_fav;
					this.ans_id = datas.ans_id;
					if(this.is_fav){
						this.favColor="red"
					}else{
						this.favColor="#000"
					}
					console.log(datas)
				}
			})
		},
		getQue(){
			$.ajax({
				url:'/api/getQuestion/queCon',
				type:'get',
				dataType:'json',
				data:{
					que_id:this.$route.params.id
				},
				success:(data) => {
					this.que_title = data.data.title;
					this.que_con = data.data.content;
				}
			})
		}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.u-name{
	margin-left: 1rem
}
.que-con-box h4{
  margin: 0
}
.ans-con-box{
	margin-top: 1.0rem;
	padding:0.5rem 0.5rem;
}
.ans_txt{
	margin-top: 1.0rem
}
.bottom{
	width: 100%;
	/*height: 100%;*/
	margin-top: 0.5rem
}
.fav{
	float:left;
	/*margin-left: 0.75rem;*/
}
.fav-logo{
	float:right;
	/*margin-right: 0.5rem;*/
	font-size: 0.8rem;
}
</style>
