<template>
  <el-select v-model="value" placeholder="请选择">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value">
    </el-option>
  </el-select>
</template>

<script>
export default {
  name: "single-select",
  data() {
    return {
      options: [{'label':'SZ-NS 深圳-南山','value':'1'},{'label':'SZ-GL 深圳-觀瀾','value':'2'},{'label':'SZ-BA 深圳-寶安','value':'4'},
        {'label':'SZ-LG 深圳-龍崗','value':'5'},{'label':'SZ-GM 深圳-光明','value':'3'},{'label':'SZ-YT 深圳-鹽田','value':'6'},
        {'label':'SZ-FT 深圳-福田','value':'7'},{'label':'SZ-PS 深圳-坪山','value':'8'},{'label':'SZ-LH 深圳-龍華','value':'9'}],
      value: ''
    }
  },
  watch:{
    value:function (){
      console.log(this.value);
      this.$emit('change',this.value);

    }
  }
}
</script>

<style scoped>

</style>
