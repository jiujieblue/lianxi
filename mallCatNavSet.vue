<template>
  <div class="mallCatNavSet">
    <oHeader></oHeader>
    <div class="mallCatNavSet-box">
      <oSide v-bind:id="menuId"></oSide>
      <div class="mallCatNavSet-box-content">
        <div class="mallCatNavSet-box-content-title">
          <span>首页管理/</span>
          <span>商城类目导航设置</span>
        </div>
        <div class="mallCatNavSet-box-content-show">
        	<span>商品展示分类</span>
        	<button @click="_openModal('addNewTowCla', 1)">添加商品分类</button>
        </div>
        <div class="mallCatNavSet-box-content-main">
        	<ul class="main-nav">
      			<li>分类名称</li>
      			<li>是否展示在首页</li>
      			<li>所属分类</li>
      			<li>导航栏排序值</li>
      			<li>操作</li>
        	</ul>
        	<div class="main-list" v-for="(cat, catI) in showCats">
        		<div>
        			{{ _add_mainList() }}
        			<span v-show="cat.childs.length != 0" @click="_mainList(catI)">{{ mainList[catI] ? '-' : '+' }}</span>
        		</div>

						

        		<div>
	        		<ul class="main-list-a1">
	        			<li>
	        				{{ _add_editorName(catI) }}
	        				<span v-show="!editorName['inpName' + catI]">{{ cat.parent.name }}</span>
	        				<input :ref="'a' + catI" type="text" :value="cat.parent.name"  v-show="editorName['inpName' + catI]">
	        			</li>
	        			<li>
	        				<span>
		        				<img src="../assets/images/checkbox-false.png" alt="未选择" v-show="!cat.parent.display">
		        				<img src="../assets/images/checkbox-true.png" alt="已选择"  v-show="cat.parent.display">
		        				<input :ref="'oneClaDis' + catI" type="checkbox" v-show="editorName['inpName' + catI]" v-model="cat.parent.display">
	        				</span>
	        			</li>
	        			<li>一级分类</li>
	        			<li>
	        				<span v-show="!editorName['inpName' + catI]">{{ cat.parent.weight || 0 }}</span>
	        				<input :ref="'oneClaWei' + catI" type="text"  v-show="editorName['inpName' + catI]" :value="cat.parent.weight">
	        			</li>
	        			<li>
	        				<p v-if="!editorName['inpName' + catI]">
	        					编辑
	        					<span @click="_editor('inpName'+catI, cat.parent.id)" v-if="!isEditor">编辑</span>
	        				</p>
			        		<span @click="_save('inpName'+catI, catI)" v-else>保存</span>
	        				<span @click="_openModal('del', 1, cat.id)">删除</span>
	        				<span @click="_openModal('addNewTowCla', 2, cat.parent.id)">添加二级分类</span>
	        			</li>
	        		</ul>
	        		<!-- 二级分类 -->
	        		<ul  class="main-list-a2" v-if="true">
	        			<li v-for="(child,childI) in cat.childs">
	        				<ul>
			        			<li>
			        				{{ _add_editorName(catI, childI) }}
			        				<span v-if="!editorName['inpName' + catI + childI]">{{ child.name }}</span>
			        				<input type="text" v-else :value="child.name">
			        			</li>
			        			<li>
			        				<span>
				        				<img src="../assets/images/checkbox-false.png" alt="未选择" v-if="!child.display">
				        				<img src="../assets/images/checkbox-true.png" alt="已选择" v-else>
				        				<input type="checkbox" v-if="editorName['inpName' + catI + childI]" v-model="child.display">
			        				</span>
			        			</li>
			        			<li>二级分类</li>
			        			<li>
			        				<span v-if="!editorName['inpName' + catI + childI]">{{ child.weight || 0 }}</span>
			        				<input type="text" :value="child.weight" v-else>
			        			</li>
			        			<li>
			        				<p v-if="!editorName['inpName' + catI + childI]">
			        					编辑
			        					<span @click="_editor('inpName'+catI+childI, child.id)" v-if="!isEditor">编辑</span>
			        				</p>
			        				<span @click="_save('inpName'+catI+childI)" v-else>保存</span>
			        				<span @click="_openModal('del', 2, child.id)">删除</span>
			        				<span @click="_openModal('setTrCla', 0, child.id)">设置三级分类</span>
			        			</li>
	        				</ul>
	        			</li>
	        		</ul>
        		</div>
        	</div>
        </div>
        <div class="mallCatNavSet-box-content-sub">
        	<button>发布更新</button>
        </div>
      </div>
    </div>


	<!-- 设置三级分类 -->
  <div class="modal-mask" v-if="modal.setTrCla">
  	<div class="modal-wrapper">
      <div class="modal-container modal-container-setTrCla">
        <div class="modal-header">
        	<slot name="header">
          	设置三级分类
          	<link rel="stylesheet" class="icon-cha" @click="_closeModal('setTrCla')">
        	</slot>
        </div>

        <div class="modal-body">
          <slot name="body">
          	<ul class="modal-body-setTrCla">
          		<li>
          			<span>所属上级分类：</span>
          			女装-裤装
          		</li>
          		<li>
          			<span>所属分类：</span>
          			三级商品分类
          		</li>
          		<li>
          			<span>三级分类明细：</span>
          			<ul>
          				<li v-for="(threeCat, threeCatI) in threeCats">
          					{{ threeCat.alias }}
          					<img src="../assets/images/del.png" height="11" width="11" alt="删除" @click="_openModal('del', 3, threeCat.cid)">
          				</li>
          			</ul>
          			<button @click="_openModal('addTrCla'),_closeModal('setTrCla')">+添加分类</button>
          		</li>
          	</ul>
          </slot>
        </div>

        <div class="modal-footer modal-footer-setTrCla">
          <slot name="footer">
          </slot>
        </div>
      </div>
    </div>
  </div>

	<!-- 添加三级分类 -->
  <div class="modal-mask" v-if="modal.addTrCla">
  	<div class="modal-wrapper">
      <div class="modal-container modal-container-addTrCla">
        <div class="modal-header">
        	<slot name="header">
          	添加分类
          	<link rel="stylesheet" class="icon-cha" @click="_closeModal('addTrCla')">
        	</slot>
        </div>

        <div class="modal-body">
          <slot name="body">
          	<ul class="modal-body-addCla">
          		<li>
          			<p>一级分类</p>
          			<ul>
          				<li v-for="(oneCategorie, oneCategorieI) in oneCategories" @click="_showCla(oneCategorie.name, oneCategorie.cid, 1)" :class="{ active : oneCategorie.cid == claObj.one.cid}">{{ oneCategorie.name }}</li>
          			</ul>
          		</li>
          		<li v-if="towCategories.length != 0">
          			<p>二级分类</p>
          			<ul>
          				<li v-for="(towCategorie, towCategorieI) in towCategories" @click="_showCla(towCategorie.name, towCategorie.cid, 2)" :class="{ active : towCategorie.cid == claObj.tow.cid}">{{ towCategorie.name }}</li>
          			</ul>
          		</li>
          		<li v-if="threeCategories.length != 0">
          			<p>三级分类</p>
          			<ul>
          				<li v-for="(threeCategorie, threeCategorieI) in threeCategories" @click="_showCla(threeCategorie.name, threeCategorie.cid, 3)" :class="{ active : threeCategorie.cid == claObj.three.cid}">{{ threeCategorie.name }}</li>
          			</ul>
          		</li>
          	</ul>
          </slot>
        </div>

        <div class="modal-footer modal-footer-addTrCla">
          <slot name="footer">
          	<button @click="_openModal('deteTrcla'),_closeModal('addTrCla')">提交</button>
          	<button @click="_closeModal('addTrCla')">取消</button>
          </slot>
        </div>
      </div>
    </div>
  </div>

	<!-- 确定三级分类 -->
  <div class="modal-mask" v-if="modal.deteTrcla">
  	<div class="modal-wrapper">
      <div class="modal-container">
        <div class="modal-header">
        	<slot name="header">
          	确定三级分类
          	<link rel="stylesheet" class="icon-cha" @click="_closeModal('deteTrcla')">
        	</slot>
        </div>

        <div class="modal-body">
          <slot name="body">
          	<ul class="modal-body-addNewTrCla">
          		<li>
          			<label for="productName">是否自定义分类名称：</label>
          			<div>
          				<input type="text" id="productName" ref='productName' @blur="_validation($event, 'productName')" v-model="threeName">
          				<p v-if="isproductName">分类名称不能为空！</p>
          			</div>
          		</li>
          		<li>
          			<span>所属分类：</span>
          			三级分类
          		</li>
          	</ul>
          </slot>
        </div>

        <div class="modal-footer">
          <slot name="footer">
          	<button @click="_subThreeData">确定</button>
          	<button @click="_closeModal('deteTrcla')">取消</button>
          </slot>
        </div>
      </div>
    </div>
  </div>

	<!-- '添加商品新分类' 或者 '添加二级分类' -->
  <div class="modal-mask" v-if="modal.addNewTowCla">
  	<div class="modal-wrapper">
      <div class="modal-container">
        <div class="modal-header">
        	<slot name="header">
          	{{ addNewTowCla == 1 ? '添加商品新分类' : '添加二级分类'}}
          	<link rel="stylesheet" class="icon-cha" @click="_closeModal('addNewTowCla')">
        	</slot>
        </div>

        <div class="modal-body">
          <slot name="body">
          	<ul class="modal-body-addNewTrCla modal-body-addTowCla">
          		<li>
          			<label for="fenleiName">分类名称：</label>
          			<div>
	          			<input type="text" id="fenleiName" ref="fenleiName" @blur="_validation($event, 'fenleiName')" >
	          			<p v-show="isfenleiName">分类名称不能为空！</p>
          			</div>
          		</li>
          		<li>
          			<span>所属分类：</span>
          			{{ addNewTowCla == 1 ? '一' : '二'}}级分类
          		</li>
          		<li>
          			<label for="sorting">排序：</label>
          			<div>
	          			<input type="text" id="sorting" :placeholder="addNewTowCla == 1 ? '1-10' : '1.1-1.9'" ref="sorting" @blur="_validation($event, 'sorting')">
	          			<p v-show="issorting">排序值不能是负数！</p>
          			</div>
          		</li>
          	</ul>
          </slot>
        </div>

        <div class="modal-footer">
          <slot name="footer">
          	<button @click="_add_newOrTowCla">确定</button>
          	<button @click="_closeModal('addNewTowCla')">取消</button>
          </slot>
        </div>
      </div>
    </div>
	</div>

	<!-- 确定三级分类 -->
  <div class="modal-mask" v-if="modal.del">
  	<div class="modal-wrapper">
      <div class="modal-container modal-container-del">
        <div class="modal-header">
        	<slot name="header">
          	删除确认
          	<link rel="stylesheet" class="icon-cha" @click="_closeModal('del')">
        	</slot>
        </div>

        <div class="modal-body modal-body-del">
          <slot name="body">
          	<p>您确定要删除这条广告吗？</p>
          </slot>
        </div>

        <div class="modal-footer modal-footer-del">
          <slot name="footer">
          	<button class="del-btn" @click="_delCla">确定</button>
          </slot>
        </div>
      </div>
    </div>
  </div>
    <!-- <oFooter></oFooter> -->
  </div>
</template>

<script>
  import Vue from 'vue'
  import oHeader from './pub/o-header.vue'
  import oFooter from './pub/o-footer.vue'
  import oSide from './pub/o-side.vue'
  import interceptors from './pub/interceptors.vue'
  import page from './pub/CkPagination.vue'
  const VueResource = require('vue-resource')
  Vue.use(VueResource)
  const fto = require('form_to_object')

export default {
  components:{
    oHeader,
    oFooter,
    oSide,
    page
  },
  data () {
    return {
    	showCats: '',
    	threeCats: '',
    	oneCategories: [],
    	towCategories: [],
    	threeCategories: [],
    	claObj: {
    		one: {},
    		tow: {},
    		three: {}
    	},

    	oneUrlCid: '',
    	towUrlCid: '',
    	threeUrlCid: '',
    	editorName: {

    	},
      menuId: '',
      checked: {
      	checked1: false,
      	checked11: false,
      	checked12: false
      },
      mainList: [
      	
      ],
      modal:{
      	addTrCla: false,
      	addNewTowCla: false,
      	deteTrcla: false,
      	setTrCla: false,
      	del: false
      },
      addNewTowCla: 1,
    	parentId: '',
      isSubClaInfo: false,
      isfenleiName: false,
      issorting: false,
      isproductName: false,

      threeName: '',
      threeCid: '',

      delCla: 0,
     	delId: '',

     	isEditor: false
    }
  },
  methods: {
  	_save (key, catI) {
  		console.log('oneClaName'+catI)
  		var a = 'a'+catI
  		console.log(this.$refs[a].value)
  		console.log(this.$refs['oneClaDis'+catI].checked)
  		console.log(this.$refs['oneClaWei'+catI]._value)

  		//this._editor(key)
  		var editorData = {"name": 1}
  		this.$http.post('/operate/update_category', editorData)
      .then(function(res){
      	console.log(res)
      },function(err){
        console.log(err)
      })
  	},
  	_delCla () {
  		var subStr = ''
  		if(this.delCla == 1 || this.delCla == 2){
  			subStr = 'category?id=' + this.parentId
  		}else{
  			subStr = 'link?cid=' + this.parentId
  		}

			this.$http.get('/operate/delete_'+subStr)
	    .then(function(res){
	    	console.log(res)
	    },function(err){
	      console.log(err)
	    })
  	},
  	_subThreeData () {
  		if(!this.threeName){
  			this.isproductName = true
  			return
  		}
  		var threeClaData = {"name": this.threeName, "cid": this.threeCid, "pid": this.parentId}
  		this.$http.post('/operate/add_category', threeClaData)
      .then(function(res){
      	console.log(res)
      },function(err){
        console.log(err)
      })
  	},
  	_showCla (name, cid, n) {
  		this.$http.get('/operate/list_categories?pid='+cid)
	    .then(function(res){
	    	if(n == 1){
	    		this.towCategories = res.data.data.categories
	    		this.claObj.one = {name: name, cid: cid}
	    	}else if(n == 2){
	    		this.threeCategories = res.data.data.categories
	    		this.claObj.tow = {name: name, cid: cid}
	    	}else{
	    		this.threeActiveCid = cid
	    		this.claObj.three = {name: name, cid: cid}
	    	}
	    },function(err){
	      console.log(err)
	    })
  	},
  	_add_mainList () {
  		this.mainList[this.mainList.length] = false
  		// this.mainList.push(false)  这里的push是有问题的
  	},
  	_validation (e, key) {
  		var val = e.target.value
  		if( key == 'sorting' ){
  			val < 0 ? (this['is'+key] = true) : (this['is'+key] = false)
  		}else{
  			!val ? (this['is'+key] = true) : (this['is'+key] = false)
  		}
  	},
  	_add_newOrTowCla () {
  		if(!this.$refs.fenleiName.value || this.isfenleiName || this.issorting){
  			if(!this.$refs.fenleiName.value){
  				this.isfenleiName = true
  			}
  			return
  		}

  		var claData = {"name": this.$refs.fenleiName.value, "weight": this.$refs.sorting.value}
  		if(this.addNewTowCla == 2){
  			claData.pid = this.parentId
  		}
  		
  		this.$http.post('/operate/add_category', claData)
      .then(function(res){
      	console.log(res)
      },function(err){
        console.log(err)
      })
  	},
  	_editor (key, id) {
  		this.$set(this.editorName, key, !this.editorName[key])
  		id ? ( this.parentId = id ) : (this.parentId = '')
  		console.log(this.parentId)
  		console.log(id)
  		this.isEditor = !this.isEditor
  	},
  	_add_editorName (catI, childI) {
  		if(childI !== undefined){
  			this.editorName[ 'inp' + catI + childI] = false
  		}else{
  			this.editorName[ 'inp' + catI] = false
  		}
  	},
  	_mainList (n) {
  		this.$set(this.mainList, n , !this.mainList[n])
  	},
  	_openModal (key, n, pid) {
  		this.$set(this.modal, key , true)
  		pid && (this.parentId = pid)

  		if( key == 'addNewTowCla'){
  			this.addNewTowCla = n
  			this.isfenleiName = false
  			this.issorting = false
  		}

  		if( key == 'setTrCla'){
  			this.threeCats = ''
  			this.$http.get('/operate/list_links?pid='+this.parentId)
		    .then(function(res){
		    	this.threeCats = res.data.data.cats
		    	console.log(this.threeCats)
		    },function(err){
		      console.log(err)
		    })
  		}

  		if( key == 'addTrCla'){
  			this.oneCategories = ''
  			this.towCategories = ''
  			this.threeCategories =''
  			for(var i in this.claObj){
  				this.claObj[i] = {}
  			}
  			this.$http.get('/operate/list_categories?pid=0')
		    .then(function(res){
		    	this.oneCategories = res.data.data.categories
		    },function(err){
		      console.log(err)
		    })
  		}

  		if( key == 'deteTrcla'){
  			for(var i in this.claObj){
  				if(!this.claObj[i].name && !this.claObj[i].cid){
  					return
  				}else{
  					this.threeName = this.claObj[i].name
  					this.threeCid = this.claObj[i].cid
  				}
  			}
  		}

  		if( key == 'del'){
  			this.delCla = n
  		}
  	},
  	_closeModal (key) {
  		if( key == 'addNewTowCla'){
  			this.addNewTowCla = null
  		}
  		this.$set(this.modal, key , false)
  	}
  },
  mounted () {
  	this.$http.get('/operate/list_display')
    .then(function(res){
      this.showCats = res.data.data.cats
    },function(err){
      console.log(err)
    })
  }
}
</script>

<style lang="less">
  @import '../assets/css/icons.css';
  @import '../less/mallCatNavSet.less';
</style>
