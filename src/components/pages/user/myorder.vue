<template>
  <div class="myorder-box">
	<div class="myorder-top-bd" v-if="orderList.length>0">
		 <span class="myorder-delete" @click="deleteRecord()" v-show="hideDiv">删除预订记录</span>
		<span class="myorder-deleteall" v-if="isTrue" @click="deleteAllRecord()">清空</span>
		<span class="myorder-complete" v-if="isTrue" @click="completeRecord()">完成</span>
	</div>
    <div class="myorderBar coll1">
      <ul>
        <li class="collArr1" v-for="(v, index) in orderList">
			<span class="order-close" @click="deleteDom(v,$event)"><i class="el-icon-close"></i></span>
			<!-- <router-link tag="div" :to="{name: 'detail', params: { id: v.programID }}" :key="v.id"> -->
			<div @click="urlDirect(v.programID, v.channelID)">
				<div class="pic-ri-top">
					<img v-lazy="item" alt="" v-for="item in v.imageUrl[0]">
					<span class="pic-mask">
						<span class="mask-time">
							<!-- {{v.createTime}} -->
							{{v.updateTime.substr(4,2)}}-{{v.updateTime.substr(6,2)}}
							{{v.updateTime.substr(8,2)}}:{{v.updateTime.substr(10,2)}}
						</span>
					</span>
				</div>
				<div class="pic-btm">
				{{v.programName}}
				</div>
			</div>
			<!-- </router-link> -->
        </li>
      </ul>
    </div>

  </div>
</template>

<script>
// 排行
import ucenterpic from 'components/common/ucenterpic'
import pagination from 'components/common/pagination'
import $ from 'jquery'
import { Message } from 'element-ui'
import { queryAppointFetch, paramFunction, delleteAppointUrl } from '@/axios/api'
export default {
	components: {
	    //ucenterpic,
	    pagination
	},
	data () {
		return {
			nowIndex:0,
			isTrue: false,
			hideDiv: true,
			isShow: false,
			puser:sessionStorage.getItem('user'),
			//ptoken:'',
			ptoken:sessionStorage.getItem('flag'),
			start:0,
			end:'',
			orderList: [],
		}
	},
  created(){
	  this._getReserve();
  },
  mounted(){ 
  },
	methods: {
	    _getReserve(){
			let self = this
			queryAppointFetch().then(res => {
				if(res.data.status == 0){
					this.orderList = res.data.data.reminds;
					// console.log(res.data.data.reminds);
					
					if( this.orderList.length == 0 ){
						this.hideDiv = false;
						$( 'myorderBar ' ).html( '您暂时没有预定数据' )
					}
				}
			}) 
	    },
		deleteDom(val,el){//删除按钮  单个
			this.delAllPlay1( val )	
			$(el.target).closest( 'li' ).remove()

		},
		deleteRecord ( ) {
			this.isTrue = true;
			this.hideDiv = false;
			$('.coll1').find('.order-close').show()
			

		},
		completeRecord () {
			this.isTrue = false
			this.hideDiv = true
			$('.order-close').hide()

		},
		deleteAllRecord () {

			this.isTrue = false;
			this.hideDiv = false;
			$('.collArr1').remove()

			this.orderList.forEach(function(item,index){
				this.delAllPlay1( item )
				console.log( item )
			}.bind( this ))


		},
		delAllPlay1( val ){ //点播预约清空
			var self = this;
			this.$http({
				method: 'post',
				url: delleteAppointUrl(),
				params: paramFunction(''),
				//post用data
				data:{
					channelID: val.channelID,
					remindTime: val.remindTime
				}
			})
			.then((res) => {
			if(res.data.status == 0) {
					// console.log( '取消预定' )
					this.$message( '成功删除预订' );
				}
			})
			.catch((res) => {
				alert(res.data.errorMessage)
			})
		},
	    urlDirect ($id, $channelId) {
	      // console.log($id)
	      this.$router.push({
	        name: 'livedetail',
	        params: {
	          id: this.$md5($id),
	          channelid: $channelId + '_channel'
	        }
	      })
	    },
	}
}
</script> 

<style scoped>
.myorder-tabs-bd { position: relative; height: auto; }
.myorder-tabs-bd .el-tabs__header { position: relative; z-index: 1; }
.myorder-tabs-bd .el-tabs__item { float: left; line-height: 33px; height: 33px; font-size: 18px; padding: 0 15px; margin: 0; cursor: pointer; }
.myorder-tabs-bd .is-active{ color: #ff9c01; }
.myorder-tabs-bd .el-tabs__active-bar { background: #ff9c01; height: 2px; }
.myorder-tabs-bd .rank-bd li { margin-bottom: 20px; }
.myorder-tabs-bd .live-crumb { margin-top: 10px; }
.myorder-top-bd { position: absolute; right: 25px; top: 11px; font-size: 14px; z-index: 2; }
.myorder-top-bd span { margin-left: 15px; cursor: pointer; }
.myorderBar .pic-mask { position: absolute; left: 0; bottom: 0; height: 30px; width: 100%; background: rgba(0,0,0,.7); color: #fff; line-height: 30px; }
.myorderBar .mask-time { position: absolute; right: 10px; top: 0; font-size: 14px; color: #fff; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.progress-bar { position: absolute; width: 100%; height: 4px; bottom: 0; left: 0; background: #ff9c01; }
.myorderBar { width: 100%; overflow: hidden; }
.myorderBar ul { padding: 50px 0 0 28px; }
.myorderBar li { cursor: pointer; position: relative; float: left; width: 165px; height: 280px; background: #f0f0f0; margin: 0 24px 24px 0; overflow: hidden; }
.myorderBar li:nth-child(5n+5) { margin: 0 0 24px 0; }
.myorderBar .pic-ri-top { position: relative; width: 100%; height: 245px; }
.myorderBar li img { width: 100%; height: 100%; }
.myorderBar .pic-title { padding-left: 8px; width: 140px; }
.myorderBar .progress-bar { height: 2px; }
.pic-btm { position: relative; height: 35px; line-height: 35px; font-size: 14px; color: #445560; padding: 0 8px; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; text-align: center; }
.movie-pos { position: absolute; right: 8px; top: 3px; }
.movie-num { font-size: 26px; font-style: italic; }
.myorderBar .icon-arror { color: #888; font-size: 18px; }
.myorderBar li:nth-child(4n+4) .rank-num, .rank-bd li:nth-child(5n+5) .rank-num { color: #888; }
.myorderBar .order-close { cursor: pointer; display: none; position: absolute; right: 0; top: 0; background: #eb5031; width: 30px; height: 30px; line-height: 30px; text-align: center; color: #fff; z-index: 2; }
.myorderBar .block { display: block; }

</style>
