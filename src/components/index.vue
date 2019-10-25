<template>
  <el-container>
    <el-header>Request Controller</el-header>
    <el-main>
      <div class="select">
        <span>
          Method ：
        </span>
        
        <el-select v-model="methodsValue" placeholder="请选择" @change="methodsSelect">
          <el-option
            v-for="item in methods"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
      </el-select>
      </div>
      
      <div class="ipt">
        <span>didmd5 ：</span>
        <el-input v-model="fixedOpt.didmd5" placeholder="请输入内容"></el-input>
      </div>
      <div class="ipt">
        <span>IP ：</span>
        <el-input v-model="fixedOpt.IP" placeholder="请输入内容"></el-input>
      </div>
      <div class="ipt">
        <span>TagID ：</span>
        <el-input v-model="fixedOpt.TagID" placeholder="请输入内容"></el-input>
      </div>
      <div class="ipt">
        <span>ImpType ：</span>
        <el-input v-model="fixedOpt.ImpType" placeholder="请输入内容"></el-input>
      </div>
      <div class="select">
        <span>
          Agreement：
        </span>
        <el-select v-model="selectValue" placeholder="请选择" @change="typeSelect">
        <el-option
          v-for="item in selectType"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
      </div>
      <div class="context">
          <el-radio-group v-model="radio" >
            <el-radio v-for="item in typeRadio" :key="item.id" :label="item.id" @change="radioChange">{{item.msg}}</el-radio>
          </el-radio-group>
      </div>
      <div class="arguments">
        <div v-show="defaultParams" class="params">
          <h4>params:</h4>
          <el-button type="primary" circle @click="add(1)">+</el-button>
          <el-button type="primary" circle @click="sub(1)">-</el-button>
            <p v-for="(item,index) in userSendParams" :key="index">
                <el-input v-model="item.key" placeholder="key"></el-input>
                <el-input v-model="item.val" placeholder="val"></el-input>
            </p>
        </div>
        <div v-show="defaultBody" class="body">
          <h4>body:</h4>
          <el-button type="primary" circle @click="add(0)">+</el-button>
          <el-button type="primary" circle @click="sub(0)">-</el-button>
              <p v-for="(item,index) in userSendBody" :key="index">
                <el-input v-model="item.key" placeholder="key"></el-input>
                <el-input v-model="item.val" placeholder="val"></el-input>
              </p>
              
        </div>
        <el-button type="success" @click="send(methodsValue)">send</el-button>
      </div>
    </el-main>
  </el-container>
</template>

<script>
import axios from 'axios';
let opt={
  OPPO:[
    {
      id:1,
      msg:"oppo浏览器信息流-信息流大图"
    },
    {
      id:2,
      msg:"oppo浏览器信息流-信息流小图"
    },{
      id:9,
      msg:"横幅图文"
    },{
      id:10,
      msg:"横幅图片"
    },{
      id:11,
      msg:"插屏图片"
    },{
      id:14,
      msg:"插屏图文"
    },{
      id:15,
      msg:"激励视频"
    },{
      id:26,
      msg:"横屏开屏"
    },{
      id:27,
      msg:"竖屏开屏"
    },{
      id:28,
      msg:"联盟信息流大图"
    },{
      id:29,
      msg:"联盟信息流小图"
    },{
      id:30,
      msg:"联盟信息流组图"
    }
  ],
  VIVO:[{
      id:1001,
      msg:"信息流小图"
    },{
      id:1002,
      msg:"信息流组图"
    },{
      id:1003,
      msg:"信息流大图"
    },{
      id:3001,
      msg:"banner"
    },{
      id:4011,
      msg:"开屏图片"
    },{
      id:2001,
      msg:"广告联盟插屏"
    },{
      id:4009,
      msg:"广告联盟开屏"
    },{
      id:3005,
      msg:"广告联盟banner"
    },{
      id:9001,
      msg:"广告联盟激励视频"
    },{
      id:5002,
      msg:"广告联盟信息流大图"
    }],
    "小米":[{
      id:1.1,
      msg:"banner网页"
    },{
      id:1.3,
      msg:"banner下载"
    },{
      id:1.5,
      msg:"焦点轮播图-跳转类"
    },{
      id:1.6,
      msg:"焦点轮播图-下载类"
    },{
      id:2.1,
      msg:"信息流小图(普通网址)"
    },{
      id:2.2,
      msg:"信息流大图(普通网址)"
    },{
      id:2.3,
      msg:"信息流组图(普通网址)"
    },{
      id:2.4,
      msg:"信息流小图(应用下载)"
    },{
      id:2.5,
      msg:"信息流大图(应用下载)"
    },{
      id:2.6,
      msg:"信息流组图(应用下载)"
    },{
      id:2.7,
      msg:"视频内容信息流（横版）-普通网址"
    },{
      id:2.14,
      msg:"视频内容信息流（横版）-应用下载"
    },{
      id:2.25,
      msg:"视频内容信息流（竖版）-普通网址"
    },{
      id:2.26,
      msg:"视频内容信息流（竖版）-应用下载"
    },{
      id:5.1,
      msg:"开屏广告"
    },{
      id:2.71,
      msg:"激励视频（普通网址）-横版"
    },{
      id:2.72,
      msg:"激励视频（应用下载）-横板"
    },{
      id:2.73,
      msg:"激励视频（应用下载）-竖版"
    },{
      id:2.74,
      msg:"激励视频（普通网址）-竖版"
    }]
}
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data(){
    return {
      port:'http://localhost:9999',
      option:{},//所有数据
      type:"VIVO",//默认类型
      typeRadio:[],//默认类型数据
      radio:1001,//默认单选
      defaultParams:0,//控制params 显示
      defaultBody:1,//控制body显示
      //请求方式的下拉框内容
      methods: [{
          value: 'GET',
          label: 'GET'
        }, {
          value: 'POST',
          label: 'POST'
        }],
        //固定的输入框内容
        fixedOpt:{
          didmd5:'',
          IP:'',
          TagID:'',
          ImpType:''
        },
        //用户传入的键、值通过params
        userSendParams:[{
            key:'',
            val:''
        }],
        //用户传入的键、值通过body
        userSendBody:[{
            key:'',
            val:''
        }],
        //Agreement下拉框内容
      selectType:[{
          value: 'OPPO',
          label: 'OPPO'
        }, {
          value: 'VIVO',
          label: 'VIVO'
        }, {
          value: '小米',
          label: '小米'
        }],
        methodsValue: 'POST',//请求下拉框默认字段
        selectValue:'VIVO'//类型下拉框默认字段
    }
  },
  created(){
    this.option=opt;
    this.typeRadio=this.option[this.type];
  },
  methods:{
    //点击选择GET或POST
    methodsSelect(val){
      if(this.methodsValue==="GET"){
        this.defaultParams=1;
        this. defaultBody=0;
      }else{
        this.defaultParams=0;
        this. defaultBody=1;
      }
    },
    //点击选择类型VIVO、OPPO 、小米
    typeSelect(val){
      
      this.type=val;
      this.typeRadio=this.option[val];
      this.radio=this.typeRadio[0].id
    },
    //点击改变单选的内容
    radioChange(val){
      this.radio=val
    },
    //添加键值输入框
    add(flag){
      let opt={
        key:'',
        val:''
      }
      if(flag){
        this.userSendParams.push(opt);
      }else{
        this.userSendBody.push(opt);
      }
    },
    //删除健值输入框
    sub(flag){
      if(flag){
        let len=this.userSendParams.length-1;
        this.userSendParams.splice(len,1)
      }else{
        let len=this.userSendBody.length-1;
        this.userSendBody.splice(len,1)
      }
    },
    //点击发送按钮
    send(methodsValue){
      // console.log(methodsValue);
      //固定传的参数
      let params={
          gp:this.methodsValue,
          ag:this.radio,
          didmd5:this.fixedOpt.didmd5,
          ip:this.fixedOpt.IP,
          tagid:this.fixedOpt.TagID,
          imptype:this.fixedOpt.ImpType,
        }
      if(methodsValue==="POST"){
        params=this.format(params,this.userSendBody)
      }else{
        params=this.format(params,this.userSendParams)
      }
      console.log(params)
      axios.get(this.port,{
          params
        })
    },
    //将固定的参数和输入的参数合并方法
   
    format(params,opt){
        let obj=opt.reduce((prev,next)=>{
          if(next.key){
            return {
            ...prev,
            [next.key]:next.val
          }
          }else{
            prev
          }
        },{});
        params={
          ...params,
          ...obj
        }
        return params
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.el-header, .el-footer {
    background-color: rgb(34, 117, 226);
    color: #333;
    text-align: center;
    line-height: 60px;
    color: #fff;
    font-size: 25px;;
  }
  .ipt span{
    width: 100px;
    display: inline-block;
    text-align: right;
  }

  
  .el-main {
    background-color: rgb(248, 250, 252);
    color: #333;
    padding:50px 300px;
  }
  .el-select{
    width: 100px;
  }
  .el-container{
    width:100%;
    margin: 0 auto;
    height: 100%;
  }
  .el-input{
    width: 150px;
    margin: 10px 5px;
    border: 2px;
  }
  .el-input__inner{
    display: inline-block;
  }
  .select span{
    display: inline-block;
    width: 105px;
    text-align: right;
  }
  .context{
    margin: 20px 0;
    width: 740px;
    display: flex;
    .el-radio {
      width: 225px;
      margin: 5px 10px;
    }
  }
  .params,.body{
    h4{
      line-height: 40px;
    }
    .el-button{
      padding: 0;
      width: 25px;
      height: 25px;
      line-height: 25px;
      font-size: 30px;
      text-align: center;
      
    }
  }
  .el-button{
    background: #409EFF;
    border: #409EFF
  }
  .el-button:hover{
    background: rgb(51, 141, 194);
  }
</style>
