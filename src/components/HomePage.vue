<template>
  <div class="hello" >
    <ul class="userInput">
      <li><label>chosen-select</label></li>
      <li><label>多選設置值<input v-model="multiSelectVal" /></label></li>
      <li>
        <label>多選
          <chosen-select place-holder="請選擇區域" v-bind:option-data="multiSelectOptData" ref="fabList"  multiple="true" v-on:itemChange="getChosenList" v-bind:size="{'width':'400px','max-height':'87px'}"></chosen-select>
        </label>
      </li>
      <li><label>多選選中值：{{chosenResult}}</label></li>
      <li style="border-top: 1px dashed #999999;margin-top: 20px;"><label>單選設置值<input v-model="singleSelectVal"></label></li>
      <li>
        <label>單選
          <chosen-select  v-bind:option-data="selectOptData" place-holder="請選擇區域"  ref="role"  v-on:itemChange="getChosenValue" v-bind:size="{'width':'400px','max-height':'87px'}"></chosen-select>
        </label>
      </li>
      <li><label>單選選中值：{{chosenVal}}</label></li>
    </ul>
    <div class="userInput">
      <el-row>ElementUI Select</el-row>
      <label>多選</label><multi-select v-on:change="getElementMultiSelect"></multi-select><br/>
      <label>多選值{{eleMultiSelectedVal}}</label><br/>
      <label>單選</label><single-select v-on:change="getElementSingleSelect"></single-select><br/>
      <label>單選值{{eleSingleSelectedVal}}</label><br/>

      <el-button type="primary" v-bind:disabled="btnActive" v-on:click="adapterWindow">窗口自適應</el-button>
    </div>

  </div>
</template>

<script>
import constant from "../assets/constant";
import ChosenSelect from './chosen-select';
import SingleSelect from './element-ui-comp/single-select';
import MultiSelect from './element-ui-comp/multi-select';

export default {
  name: 'HelloWorld',
  components:{MultiSelect, SingleSelect, ChosenSelect},
  data () {
    return {
      value:'',
      btnActive:false,//按鈕激活狀態
      tempVal:"",
      chosenResult:[],//多選值列表
      chosenVal:'',//單選值
      baseInputVal:'',
      customInputVal:'',
      multiSelectVal:'',//多選初始化
      singleSelectVal:'',
      eleMultiSelectedVal:[],
      eleSingleSelectedVal:'',
      selectOptData:[{'label':'SZ-NS 深圳-南山','value':'1'},{'label':'SZ-GL 深圳-觀瀾','value':'2'},{'label':'SZ-BA 深圳-寶安','value':'4'},
                        {'label':'SZ-LG 深圳-龍崗','value':'5'},{'label':'SZ-GM 深圳-光明','value':'3'},{'label':'SZ-YT 深圳-鹽田','value':'6'},
                        {'label':'SZ-FT 深圳-福田','value':'7'},{'label':'SZ-PS 深圳-坪山','value':'8'},{'label':'SZ-LH 深圳-龍華','value':'9'}],
      multiSelectOptData:[{'label':'SZ-NS 深圳-南山','value':'1','is_active':'1'},{'label':'SZ-GL 深圳-觀瀾','value':'2','is_active':'1'},{'label':'SZ-BA 深圳-寶安','value':'4','is_active':'1'},
        {'label':'SZ-LG 深圳-龍崗','value':'5','is_active':'1'},{'label':'SZ-GM 深圳-光明','value':'3','is_active':'1'},{'label':'SZ-YT 深圳-鹽田','value':'6','is_active':'1'},
        {'label':'SZ-FT 深圳-福田','value':'7','is_active':'1'},{'label':'SZ-PS 深圳-坪山','value':'8','is_active':'1'},{'label':'SZ-LH 深圳-龍華','value':'9','is_active':'1'}],
    };
  },
  mounted() {
    this.singleSelectVal='0';//初始化單選選擇器
    this.multiSelectVal='1,4';
  },
  watch:{
    singleSelectVal:function (newVal,oldVal){
      this.$refs.role.iniSingleSelect(newVal);
    },
    multiSelectVal:function (newVal,oldVal){
      let list=newVal.trim(',').split(',');
      this.$refs.fabList.iniMultiSelect(list);
    }
  },
  methods: {


    getChosenList:function (val){
      //this.chosenResult=val;
      console.log(val);
      let temp=[];
      for( let i=0;i<val.length;i++){

        temp.push(val[i].itm.value)


      }
      this.chosenResult=temp;
      console.log(temp);
    },
    getChosenValue:function (val){
      this.chosenVal=val;
    },
    setSelectVal:function (val){
      this.$refs.fabList.curSelected=val;
    },
    getElementMultiSelect:function (val){
      this.eleMultiSelectedVal=val;
    },
    getElementSingleSelect:function (val){
      this.eleSingleSelectedVal=val;
    },
    adapterWindow:function (){
      this.btnActive=true;
      this.zoom();
      window.onresize = () => {
        this.zoom();
      };
    }
    ,
    zoom:function (){
      let x = window.innerWidth / constant.constant.PAGE_WIDTH;
      let y = window.innerHeight / constant.constant.PAGE_HEIGHT;
      let oBody = document.getElementsByTagName('body')[0];
      oBody.style.transform = `scale(${x}, ${y})`;
      oBody.style.webkitTransform = `scale(${x}, ${y})`;/* for Chrome || Safari */
      oBody.style.msTransform = `scale(${x}, ${y})`;/* for Firefox */
      oBody.style.mozTransform = `scale(${x}, ${y})`;/* for IE */
      oBody.style.oTransform = `scale(${x}, ${y})`;/* for Opera */
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}

a {
  color: #42b983;
}
.hello{text-align: left;}
.userInput {
  width: 500px;  height:400px;margin: 0 0 0 150px;  display: inline-block;float: left;border:1px dashed whitesmoke;
}
.userInput div{color: whitesmoke;}
.userInput>li{margin-bottom: 20px;}
.userInput label{color: whitesmoke;}
</style>
