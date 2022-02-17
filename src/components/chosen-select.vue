<template>
  <div class="chosen-container "
       v-bind:title="defaultVal"
       v-bind:class="{'chosen-with-drop chosen-container-active':chosenDrop,'chosen-container-multi':multiple,'chosen-container-single':!multiple}" title=""

       v-bind:style="size"
       v-on:click="chosenClick"
       v-on:mouseover="mouseOnTag=true" v-on:mouseout="mouseOnTag=false">
    <template v-if="multiple">
      <ul class="chosen-choices" v-bind:style="{'max-height':size['max-height']}">

        <li class="search-choice" v-for="(rs,idx) in resultSelected">
          <span>{{rs.itm.label}}</span>
          <a class="search-choice-close" v-bind:data-option-array-index="idx" v-on:click="unSelect(rs,idx)"></a>
        </li>
        <li class="search-field">
          <input ref="input" v-on:focusout="focusOut" v-bind:placeholder="placeHolder" v-model="searchVal" v-on:keyup.delete="deleteSelect" class="chosen-search-input" type="text" autocomplete="off" style="width: 75px;">
        </li>
      </ul>
      <div class="chosen-drop" v-on:click="chosenClick">
        <ul class="chosen-results" >
             <li v-for="(opItem,index) in searchList"
                v-bind:data-option-array-index="opItem.value"
                :key="opItem.value"
                v-bind:class="[opItem.is_active==='1'?'active-result':'result-selected']"
                v-on:click="chosenDropClick(opItem,index)"
            >{{opItem.label}}</li>


        </ul>
      </div>
    </template>
    <template v-else>
      <a class="chosen-single chosen-default">
        <span>{{curSelected}}</span>
        <div><b></b></div>
      </a>
      <div class="chosen-drop">
        <div class="chosen-search">
          <input ref="input" v-on:focusout="focusOut" v-model="searchVal" v-on:keyup.delete="deleteSelect" class="chosen-search-input" type="text" autocomplete="off"
                 v-bind:style="{'width':(size.width.trimEnd('px')-9)+'px'}">
        </div>
        <ul class="chosen-results">
          <li v-for="(opItem,index) in searchList"
              v-bind:data-option-array-index="opItem.value"
              :key="opItem.value"
              v-bind:class="['active-result']"
              v-on:click="chosenSingleDropClick(opItem,index)"
          >{{opItem.label}}</li>

        </ul>
      </div>
    </template>
  </div>
</template>

<script>
import "../../styles/chosen.css";
import _ from 'lodash';
export default {
  name: "chosen-select",
  props:['size','multiple','placeHolder','defaultVal','defaultList','optionData'],//組件寬度，是否為多選組件
  data:function (){
    return {
      mouseOnTag:false,//鼠標在下拉選項上
      chosenDrop:false,//默認為假，焦點在input裡邊時顯示下拉選項，失焦時下拉選項隱藏
      searchVal:'-1',//輸入框字符串
      searchLen:0,//搜索框字符串長度，長度為1時再按刪除不刪除已選擇的項
      // optionData:[
      //   {'optionGroup':'GL','optionGroupLabel':'觀瀾廠區','optionList':[{'WORKSHOP_ID':'2','FLOOR_ID':'A01-1F','WORKSHOP_NAME':'Protoline廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'30','FLOOR_ID':'A01-4F','WORKSHOP_NAME':'Protoline廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'1','FLOOR_ID':'A02-3F','WORKSHOP_NAME':'Protoline廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'10','FLOOR_ID':'B11-1F','WORKSHOP_NAME':'金加一/二廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'11','FLOOR_ID':'B12-1F','WORKSHOP_NAME':'Protoline廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'9','FLOOR_ID':'B12-4F','WORKSHOP_NAME':'金加一廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'31','FLOOR_ID':'B13-1F','WORKSHOP_NAME':'Protoline廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'5','FLOOR_ID':'B13-3F','WORKSHOP_NAME':'Protoline廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'6','FLOOR_ID':'C04-1F','WORKSHOP_NAME':'金加一廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'33','FLOOR_ID':'C06-2F','WORKSHOP_NAME':'金加二廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'32','FLOOR_ID':'C06-4F','WORKSHOP_NAME':'金加二廠','AREA_ID':'GL','AREA_NAME':'觀瀾廠區','IS_ACTIVE':'1'}]},
      //   {'optionGroup':'JY','optionGroupLabel':'濟源廠區','optionList':[{'WORKSHOP_ID':'14','FLOOR_ID':'A03-1F','WORKSHOP_NAME':'金加一廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'15','FLOOR_ID':'A05-1F','WORKSHOP_NAME':'金加二廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'21','FLOOR_ID':'A08-1F','WORKSHOP_NAME':'金加三廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'16','FLOOR_ID':'B01-1F','WORKSHOP_NAME':'金加二廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'22','FLOOR_ID':'B02-1F','WORKSHOP_NAME':'金加三廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'17','FLOOR_ID':'B03-1F','WORKSHOP_NAME':'金加二廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'23','FLOOR_ID':'B06-1F','WORKSHOP_NAME':'金加三廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'24','FLOOR_ID':'B07-1F','WORKSHOP_NAME':'金加三廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'18','FLOOR_ID':'B08-1F','WORKSHOP_NAME':'金加二廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'19','FLOOR_ID':'G01-1F','WORKSHOP_NAME':'金加二廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'},{'WORKSHOP_ID':'20','FLOOR_ID':'G01-2F','WORKSHOP_NAME':'金加二廠','AREA_ID':'JY','AREA_NAME':'濟源廠區','IS_ACTIVE':'1'}]}
      //   ],
      resultSelected:[],//多選結果
      resultActive:[],//待選選項
      searchList:[],//搜索到的選項
      curSelected:''//單選選項當前的值

    }
  },

  created:function() {
    this.debouncedGetSearchList = _.debounce(this.inputSearch, 500)

  }
  ,
  mounted() {
    this.searchVal='';

    if(this.placeHolder===undefined){
      this.placeHolder='請選擇';
    }
    if(!this.multiple)//單選時設置初始值選中的值
    {
      if(this.defaultVal!=='')
      {
        this.iniSingleSelect(this.defaultVal);
      }

    }



  },
  computed:{

  },
  watch:{
    searchVal:function (newVal,oldVal){

      this.debouncedGetSearchList();
    }
  }
  ,
  methods:{
    chosenClick:function (){
      if(this.mouseOnTag){

          this.$refs.input.focus();
          this.chosenDrop=true;
      }

    },
    chosenDropClick:function (item,itemPos){//多選選項點擊事件
      if (item.is_active==='1'){
        item.is_active='0';
        let tempItem={'index':itemPos,'itm':item};
        this.resultSelected.push(tempItem);
        this.$emit('itemChange',this.resultSelected);
      }
    },
    chosenSingleDropClick:function (item,itemPos){//單選選項點擊事件

      this.curSelected=item.label;//item.WORKSHOP_ID;
      console.log(item.value);

      this.mouseOnTag=false;
      this.chosenDrop=false;
      this.$emit('itemChange',item);
    },
    iniMultiSelect:function (vList){
      if(vList.length===0) return;

      for (let  val of vList){
        let item=this.optionData.find(function (ele){return ele.value===val;});
        if(item!==undefined){
          let itemPos=this.optionData.indexOf(item);
          this.chosenDropClick(item,itemPos);
        }
      }
    }
    ,
    iniSingleSelect:function (val){//初始化單選select
      if(val==='')return;
      let item=this.optionData.find(function (ele){return ele.value===val;});
        if(item!==undefined){
          this.curSelected=item.value;//item.WORKSHOP_ID;
          this.chosenSingleDropClick(item,0);
        }else{
        this.curSelected=this.placeHolder;
      }
    }
    ,
    unSelect:function (item,index){
      item.itm.is_active='1';
      this.resultSelected.splice(index,1);
      this.$emit('itemChange',this.resultSelected);
    },
    deleteSelect:function (){//按刪除鍵刪除選中的項
      let inputStr=this.$refs.input.value;//獲取輸入框內容

      if(inputStr!==''){
        this.searchLen=inputStr.length;
        return;
      }
      if(this.searchLen===1)
      {
        this.searchLen=inputStr.length;
        return;
      }

      let rltLen=this.resultSelected.length;
      if(rltLen>0){
        let idx=rltLen-1;
        this.resultSelected[idx].itm.is_active='1';
        this.resultSelected.splice(idx,1);
        this.$emit('itemChange',this.resultSelected);
      }

    },
    inputSearch:function (){

        console.log('進入搜索方法1。。。');
        let val = this.searchVal.toUpperCase();
        this.searchList=this.optionData.filter(e=>(e.label).toUpperCase().indexOf(val)>=0);
        //console.log(tempArr);

    }
    ,
    focusOut:function (){
      if(!this.mouseOnTag)//鼠標離開
      {
        this.chosenDrop=false;
      }
    }
  }

}
</script>

<style scoped>
.chosen-drop li.active-result:hover{
  background-color: #3875d7;
  background-image: -webkit-gradient(linear, left top, left bottom, color-stop(20%, #3875d7), color-stop(90%, #2a62bc));
  background-image: linear-gradient(#3875d7 20%, #2a62bc 90%);
  color: #fff;
}
.chosen-container-multi .chosen-choices{overflow-y: auto;}



.chosen-results::-webkit-scrollbar { width: 1px; height: 1px; }
.chosen-results::-webkit-scrollbar-thumb { border-radius: 4px; background: #E0E3E7; }
.chosen-results::-webkit-scrollbar-track { background-color: transparent; }

.chosen-choices::-webkit-scrollbar { width: 1px; height: 1px; }
.chosen-choices::-webkit-scrollbar-thumb { border-radius: 4px; background: #E0E3E7; }
.chosen-choices::-webkit-scrollbar-track { background-color: transparent; }

</style>
