<template>
  <div id="app">
    <h1>vue2-uploader</h1>
    <p>
      一个简单的，基于<a href="https://github.com/axios/axios">axios</a>的文件上传组件。<br>
    </p>
    <vue-uploader ref="uploader"
                  :multiple="true"
                  url="/file"
                  @change="onChange"
                  @add="onAdd"
                  @item-progress="itemProgressChanged"
                  @item-success="itemSuccess"
                  @item-complete="itemComplete"
                  @item-before-upload="beforeUpload"
                  @item-cancel="onItemCancel"
                  @complete="complete"
                  @item-error="onItemError"
                  @progress="onProgress"
                  :filter="filter"
                  :chunk-size="200000"
                  :auto-upload="false"/>
    <div>待上传的文件的列表</div>
    <a href="javascript:;" @click="uploadAll">全部上传</a>
    <div>总进度{{ totalProgress }}</div>
    <table>
      <tr v-for="file in files" :key="file.id">
        <td>{{ file.id }}</td>
        <td>{{ file.file.name }}</td>
        <td>{{ file.state }}</td>
        <td>{{ file.progress }}</td>
        <td><a href="javascript:void(0);" @click="file.cancel()">取消</a></td>
        <td><a href="javascript:void(0);" @click="file.uploadItem()">上传</a></td>
      </tr>
    </table>
    <div>上传成功的文件的列表</div>
    <table>
      <tr v-for="file in rspFiles" :key="file.id">
        <td>{{ file }}</td>
      </tr>
    </table>
  </div>
</template>

<script>
  import Vue from 'vue'
  import { Component } from 'vue-property-decorator'
  import VueUploader from './components'

  @Component({
    components: {
      VueUploader
    }
  })
  export default class App extends Vue {
    name = 'App'
    files = []
    rspFiles = []
    totalProgress = 0

    uploadAll () {
      this.$refs.uploader.uploadAll()
    }

    onAdd (fileItem) {
      console.debug('添加文件', fileItem)
    }

    itemProgressChanged (fileItem, p) {
      console.debug('文件进度改变', fileItem, p)
    }

    onProgress (p) {
      this.totalProgress = p
    }

    itemSuccess (fileItem, rsp) {
      this.rspFiles.push(rsp.data)
      console.debug('文件上传完成', fileItem)
    }

    itemComplete (fileItem) {
      console.debug('文件处理完成', fileItem)
    }

    complete () {
      console.debug('所有文件上传完成')
    }

    onChange (files) {
      this.files = files
    }

    onItemCancel (fileItem) {
      console.debug('取消了上传', fileItem)
    }

    beforeUpload (fileItem) {
      console.debug('准备上传文件', fileItem)
    }

    onItemError (fileItem, e) {
      console.debug('文件上传错误', fileItem, e)
    }

    filter (file) {
      return file.name.length > 5
    }
  }
</script>
<style lang="less">
  .vue-uploader {
    display: inline-block;
    position: relative;
    overflow: hidden;
    border: solid 1px black;

    input[type='file'] {
      display: inline-block;
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      position: absolute;
      opacity: 0;
      font-size: 0;
      cursor: pointer;
    }
  }
</style>
