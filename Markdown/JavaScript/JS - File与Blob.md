## new File/Blob

```js
const blob = new Blob(['123']);
//File至少要有两个参数
const file = new File(['123'],'123.text');
```

File的原型是Blob，所以File基本上包含了Blob的功能和属性。

## arrayBuffer

```js
blob.arrayBuffer();
file.arrayBuffer();
//以上两个返回的数据是一样的，返回的是一个Promise对象
```

## FileReader

```js
const fr = new FileReader()
fr.onload = (e) => {
    console.log(fr.result)
    console.log(e.target.result)
    //fr.result就是e.target.result
}
fr.readAsArrayBuffer(file)
```

## DataView

```js
const dv = new DataView(await blob.arrayBuffer());
console.log(dv);
//getUint8获取二进制数据
console.log(dv.getUint8(1));
//从二进制层面去修改数据
dv.setUnit8(1,65);
```

## 从本地获取文件

从本地获取常用的方式有三种：

通过input类型为file的标签

```vue
<template>
	<input type="file" @change="handleChange">
</template>
<script>
	export default{
		methods:{
			async handleChange(e){
                //通过input可以获取多个文件，存放到files数组中
                console.log(e.target.files);
                //files[0]是个File对象
                console.log(e.target.files[0]);
                //可以获取file[0]的ArrayBuffer
                console.log(await e.target.files[0].arrayBuffer());
            }
		}
	}
</script>	

```

通过拖拽的事件去获取

```vue
<template>
	<div  @drag="handleDragOver"
         @drop="handleDropFile" 
         style="width:100px;height:100px">
    </div>	
</template>
<script>
	export default{
		methods:{
            handleDragOver(e){
                //阻止拖拽的默认行为
                e.preventDefault();
            },
            handleDropFile(e){
                //阻止拖拽释放时的默认行为
                e.preventDefault();
                //通过事件获取files
                console.log(e.dataTransfer.files);
                console.log(e.dataTransfer.files[0]);
                console.log(await e.dataTransfer.files[0].arrayBuffer());
            }
		}
	}
</script>	

```

showDirectoryPicker API获取文件

```vue
<template>
  <button @click="handleClick"></button>
</template>
<script>
export default {
  methods: {
    handleClick() {
      const pickerOptions = {
        //限定选择文件的类型
        types: [
          {
            description: 'Images',
            accept: {'images/*': ['.png', '.gif', '.jpg']}
          }
        ],
        //是否排除上面types中的所有的accept文件类型
        excludeAcceptAllOption: true,
        //是否多选    
        multiple: false
      };
      //调用showDirectoryPicker开启文件选择窗口
      //注意：showDirectoryPicker是比较新的语法，有的浏览器存在兼容问题
      window.showDirectoryPicker(pickerOptions).then(([fileHandle]) => {
        console.log(fileHandle)
        fileHandle.getFile().then((file) => {
          console.log(file);
          file.arrayBuffer().then(ab => {
            console.log(ab)
          })
        })
      })
    }
  }
}
</script>	

```

## 从本地获取文件夹

```js
window.showDirectoryPicker().then(async dirHandle=>{
    //dirHandle.values()是个异步遍历器，具体语法参见ECMA6
    for await(const entry of dirHandle.values()){
        console.log(entry);
    }
})
```

```vue
<template>
  <div class="component-localFolderOptions">
    <div class="mb10">
      <el-button @click="onOpenDir">打开文件夹</el-button>
    </div>
    <ul v-if="itemList">
      <li v-for="(item,index) in itemList" :key="index" @click="onClickItemName(item)">{{item}}</li>
    </ul>
    <div style="width: 600px">
      <el-input :value="content" type="textarea"></el-input>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      itemList:null,
      dirHandle:null,
      content:null
    }
  },
  methods: {
    onOpenDir(){
      window.showDirectoryPicker().then(async fsfHandle=>{
        if(!fsfHandle) return;
        this.dirHandle = fsfHandle;
        this.itemList = [];
        for await(const entry of fsfHandle.values()){
          console.log(entry);
          this.itemList.push(`${entry.name}${(entry.kind==='file'? '' : '(文件夹)')}`)
        }
      })
    },
    onClickItemName(name){
      this.dirHandle.getFileHandle(name).then(fHandle=>{
        if(fHandle.kind === 'file'){
          fHandle.getFile().then(file=>{
            if(!!file){
              file.text().then(content=>{
                this.content = content
              })
            }
          })
        }
      })
    }
  },
}
</script>

```



## 获取远端的文件

```js
fetch('https://www.baidu.com/img/bd_logo1.png').then(res=>res.blob()).then(result=>{console.log(result)})
```

## 本地文本文件操作

```vue
<template>
  <div class="component-LocalFileOptions pa10">
    <div style="width: 600px;">
      <el-input v-model="content" type="textarea" placeholder="请输入"></el-input>
      <div class="flex-row pt10">
        <el-button @click="onCreate">新建</el-button>
        <el-button @click="onOpen">打开</el-button>
        <el-button @click="onSaveAs(true)">保存</el-button>
        <el-button @click="onSaveAs(false)">另存为</el-button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "LocalFileOptions",
  components: {},
  props: {},
  data() {
    return {
      opts:{
        types: [{
          description: 'Text file',
          accept: {'text/plain': ['.txt']},
        }],
      },
      content:'',
      fh:null
    }
  },
  created() {
  },
  mounted() {
  },
  filters: {},
  watch: {},
  computed: {},
  methods: {
    onCreate(){
      //如果没有指定options(opts),那么无法打开和保存带有中文的内容
      return window.showSaveFilePicker(this.opts).then((fileHandle)=>{
        this.setFileHandle(fileHandle)
      })
    },
    onOpen(){
      //如果没有指定options(opts),那么无法打开和保存带有中文的内容
      window.showOpenFilePicker(this.opts).then(([fileHandle])=>{
        this.setFileHandle(fileHandle);
        fileHandle.getFile().then(file=>{
          file.text().then(res=>{
            this.content = res;
          })
        })
      })
    },
    onSaveAs(isSave){
      if(isSave && !!this.fh){
        this.save(this.fh)
      }else{
        this.onCreate().then(fileHandle=>{
          this.save(fileHandle)
        })
      }
    },
    save(fileHandle){
      if(!fileHandle) return;
      fileHandle.createWritable().then(stream=>{
        stream.write(this.content);
        stream.close();
        this.$message.success('数据已保存');
      })
    },
    setFileHandle(fileHandle){
      this.fh = fileHandle
    }
  },
}
</script>

<style lang="scss" scoped>
.component-LocalFileOptions {
  /deep/ .el-textarea__inner{
    min-height: 300px !important;
  }
}
</style>
```

## 本地图片文件操作

本地图片利用canvas编辑后另存为新图片文件

```vue
<template>
  <div class="component-localImageOptions">
    <div class="flex-row" style="align-items: center">
      <div class="border1 flex-none">
        <el-image :src="imageUrl" style="height: 300px;width: 300px"></el-image>
      </div>
      <div class="flex-none">
        ==>
      </div>
      <div class="border1 flex-none">
        <canvas ref="canvas" width="300" height="300"></canvas>
      </div>
    </div>
    <div class="pt10">
      <el-button @click="onOpen">打开一张图片</el-button>
      <el-button @click="onSave">另存为图片</el-button>
    </div>
  </div>
</template>

<script>
export default {
  name: "localImageOptions",
  components: {},
  props: {},
  data() {
    return {
      imageUrl:'',
      context:null
    }
  },
  created() {
  },
  mounted() {
    const ctx = this.$refs.canvas.getContext("2d");
    if(!!ctx) this.context = ctx;
  },
  filters: {},
  watch: {},
  computed: {},
  methods: {
    onOpen(){
      window.showOpenFilePicker().then(([fileHandle])=>{
        fileHandle.getFile().then(file=>{
          this.imageUrl = URL.createObjectURL(file)
          const image = new Image()
          image.width = 300;
          image.onload = () =>{
            this.context.drawImage(image,0,0,300,300);
            const data = this.context.getImageData(0,0,300,300);
            this.color2Gray(data);
            this.context.putImageData(data,0,0,0,0,data.width,data.height);
          }
          image.src = this.imageUrl
        })
      })
    },
    color2Gray(imgData){
      for(let index = 0,w=0;w<imgData.width;w++){
        for (let h = 0;h<imgData.height;h++,index+=4){
          const r = imgData.data[index];
          const g = imgData.data[index+1];
          const b = imgData.data[index+2];
          // a为透明度，这里不设置
          // const a = imgData.data[index+3];

          const ave = (r+g+b)/3;
          imgData.data[index] = ave;
          imgData.data[index+1] = ave;
          imgData.data[index+2] = ave;
        }
      }
    },
    onSave(){
      window.showSaveFilePicker().then((fileHandle)=>{
        this.save(fileHandle)
      })
    },
    save(handle){
      if(!handle) return;
      handle.createWritable().then(stream=>{
        this.$refs.canvas.toBlob((blob)=>{
          if(!blob) return
          stream.write(blob);
          stream.close();
        })
      })
    }
  },
}
</script>
```

## Blob Url和Data Url

创建方式

```js
//blob url创建方式
URL.createObjectURL(blob/file);
//data url创建方式，出于安全性考虑，不能用在link上，会被禁止掉
fileReader.readAsDataURL(blobfile);
```

创建后的url的使用

```vue
<a :href="url"></a>
<img :src="url"/>
<iframe :scr="url"></iframe>
```

**示例：**

```js
onFetch(url){
    fetch(url).then(res=>{
        return res.blob()
    }).then(blobData=>{
        blobData = new Blob([blobData])
        if(blobData){
            //生成blob URL
            this.blobURL = URL.createObjectURL(blobData);
            //生成data URL
            const fileReader = new FileReader();
            fileReader.onload = (e=>{
                this.dataURL = e.target.result
            })
            fileReader.readAsDataURL(blobData);
        }
    })
}
```

