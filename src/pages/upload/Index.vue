<template>
  <page-wraper>
    <!-- #ifdef MP-WEIXIN -->
    <wd-privacy-popup></wd-privacy-popup>
    <!-- #endif -->
    <wd-message-box></wd-message-box>
    <wd-toast></wd-toast>
    <demo-block title="基本用法">
      <wd-upload accept="image" v-model:file-list="fileList" image-mode="aspectFill" :action="action"></wd-upload>
    </demo-block>
    <demo-block title="最大上传数限制">
      <wd-upload :file-list="fileList3" :limit="3" :action="action" @change="handleChange3"></wd-upload>
    </demo-block>
    <demo-block title="覆盖上传">
      <!-- #ifdef MP-WEIXIN || H5  -->
      <wd-upload accept="all" reupload v-model:file-list="fileList17" image-mode="aspectFill" :action="action"></wd-upload>
      <!-- #endif -->
      <!-- #ifndef MP-WEIXIN -->
      <!-- #ifndef H5 -->
      <wd-upload accept="image" reupload v-model:file-list="fileList17" image-mode="aspectFill" :action="action"></wd-upload>
      <!-- #endif -->
      <!-- #endif -->
    </demo-block>
    <demo-block title="拦截预览图片操作">
      <wd-upload :file-list="fileList4" :action="action" @change="handleChange4" :before-preview="beforePreview"></wd-upload>
    </demo-block>
    <demo-block title="上传前置处理">
      <wd-upload :file-list="fileList5" :action="action" @change="handleChange5" :before-upload="beforeUpload"></wd-upload>
    </demo-block>
    <demo-block title="移除图片前置处理">
      <wd-upload :file-list="fileList6" :action="action" @change="handleChange6" :before-remove="beforeRemove"></wd-upload>
    </demo-block>
    <demo-block title="上传状态钩子">
      <wd-upload
        :file-list="fileList7"
        :action="action"
        @change="handleChange7"
        @success="handleSuccess"
        @fail="handleFail"
        @progress="handleProgess"
      ></wd-upload>
    </demo-block>
    <demo-block title="禁用">
      <wd-upload :file-list="fileList8" disabled :action="action" @change="handleChange8"></wd-upload>
    </demo-block>
    <demo-block title="自定义唤起上传样式并限制上传5张">
      <wd-upload :file-list="fileList9" :action="action" @change="handleChange9" :limit="5">
        <wd-button>自定义唤起样式</wd-button>
      </wd-upload>
    </demo-block>
    <demo-block title="选择文件前置处理">
      <wd-upload :file-list="fileList10" :action="action" @change="handleChange10" :before-choose="beforeChoose"></wd-upload>
    </demo-block>

    <!-- <demo-block title="上传至oss">
      <wd-upload :file-list="fileList11" action="https://xxx.aliyuncs.com" :build-form-data="buildFormData" @change="handleChange11"></wd-upload>
    </demo-block> -->

    <demo-block title="上传视频">
      <wd-upload accept="video" multiple :file-list="fileList1" :action="action" @change="handleChange1"></wd-upload>
    </demo-block>

    <!-- #ifdef MP-WEIXIN -->
    <demo-block title="上传视频和图片">
      <wd-upload accept="media" multiple :file-list="fileList11" :action="action" @change="handleChange11"></wd-upload>
    </demo-block>
    <demo-block title="仅上传文件">
      <wd-upload accept="file" multiple :file-list="fileList12" :action="action" @change="handleChange12"></wd-upload>
    </demo-block>
    <!-- #endif -->

    <!-- #ifdef MP-WEIXIN || H5  -->
    <demo-block title="上传视频图片和文件">
      <wd-upload accept="all" multiple :file-list="fileList13" :action="action" @change="handleChange13"></wd-upload>
    </demo-block>
    <!-- #endif -->

    <demo-block title="手动触发上传">
      <wd-upload ref="upload14" :auto-upload="false" :file-list="fileList14" :action="action" @change="handleChange14"></wd-upload>
      <wd-button @click="upload14?.submit()">开始上传</wd-button>
    </demo-block>

    <demo-block title="自定义上传方法">
      <wd-upload v-model:file-list="fileList15" :upload-method="customUpload"></wd-upload>
    </demo-block>

    <demo-block title="自定义预览样式">
      <wd-upload v-model:file-list="fileList16" accept="image" image-mode="aspectFill" :action="action">
        <template #preview-cover="{ file, index }">
          <!-- 小程序拿不到文件 -->
          <view class="preview-cover">{{ file.name || `文件${index}` }}</view>
        </template>
      </wd-upload>
    </demo-block>
    <!-- #ifdef H5 || MP-WEIXIN -->
    <demo-block title="根据扩展名过滤">
      <!-- #ifdef H5 -->
      <wd-upload v-model:file-list="fileList18" :extension="['.jpg', '.png']" :action="action"></wd-upload>
      <view style="margin: 10px 0">选择视频时过滤mp4</view>
      <wd-upload accept="video" v-model:file-list="fileList19" :extension="['.mp4']" :action="action"></wd-upload>
      <view style="margin: 10px 0">上传所有类型文件时过滤.pdf和.docx</view>
      <wd-upload accept="all" v-model:file-list="fileList21" :extension="['.pdf', '.docx']" :action="action"></wd-upload>
      <!-- #endif -->
      <!-- #ifdef MP-WEIXIN -->
      <view style="margin-bottom: 10px">选择文件时过滤.txt文件</view>
      <wd-upload accept="file" v-model:file-list="fileList19" :extension="['.txt']" :action="action"></wd-upload>
      <view style="margin: 10px 0">选择所有文件时过滤.jpg和.mp4</view>
      <wd-upload accept="all" v-model:file-list="fileList20" :extension="['.jpg', '.mp4']" :action="action"></wd-upload>
      <!-- #endif -->
    </demo-block>
    <!-- #endif -->
  </page-wraper>
</template>
<script lang="ts" setup>
import { useToast, useMessage } from '@/uni_modules/wot-design-uni'
import type { UploadFile, UploadInstance, UploadMethod } from '@/uni_modules/wot-design-uni/components/wd-upload/types'
import { ref } from 'vue'

const action: string = 'https://mockapi.eolink.com/zhTuw2P8c29bc981a741931bdd86eb04dc1e8fd64865cb5/upload'
const fileList = ref<UploadFile[]>([
  {
    url: 'https://registry.npmmirror.com/wot-design-uni-assets/*/files/panda.jpg'
  }
])

const fileList1 = ref<UploadFile[]>([])
const fileList2 = ref<UploadFile[]>([
  {
    url: 'https://img12.360buyimg.com//n0/jfs/t1/29118/6/4823/55969/5c35c16bE7c262192/c9fdecec4b419355.jpg'
  }
])
const fileList3 = ref<UploadFile[]>([])
const fileList4 = ref<UploadFile[]>([])
const fileList5 = ref<UploadFile[]>([])
const fileList6 = ref<UploadFile[]>([])
const fileList7 = ref<UploadFile[]>([])
const fileList8 = ref<UploadFile[]>([])
const fileList9 = ref<UploadFile[]>([])
const fileList10 = ref<UploadFile[]>([])
const fileList11 = ref<UploadFile[]>([])
const fileList12 = ref<UploadFile[]>([])
const fileList13 = ref<UploadFile[]>([])
const fileList14 = ref<UploadFile[]>([])
const fileList15 = ref<UploadFile[]>([])
const fileList16 = ref<UploadFile[]>([
  {
    url: 'https://registry.npmmirror.com/wot-design-uni-assets/*/files/panda.jpg',
    name: 'panda'
  }
])
const fileList17 = ref<UploadFile[]>([
  {
    url: 'https://registry.npmmirror.com/wot-design-uni-assets/*/files/panda.jpg'
  }
])
const fileList18 = ref<UploadFile[]>([])
const fileList19 = ref<UploadFile[]>([])
const fileList20 = ref<UploadFile[]>([])
const fileList21 = ref<UploadFile[]>([])

const upload14 = ref<UploadInstance>()

const messageBox = useMessage()
const toast = useToast()

const beforeChoose = ({ file, resolve }: any) => {
  messageBox
    .confirm({
      msg: '是否选择',
      title: '提示'
    })
    .then(() => {
      resolve(true)
    })
    .catch(() => {
      toast.show('取消选择操作')
    })
}

const beforePreview = ({ resolve, file }: any) => {
  messageBox
    .confirm({
      msg: '是否预览图片',
      title: '提示'
    })
    .then(() => {
      resolve(true)
    })
    .catch(() => {
      toast.show('取消预览操作')
    })
}
const beforeUpload = ({ files, resolve }: any) => {
  messageBox
    .confirm({
      msg: '是否上传',
      title: '提示'
    })
    .then(() => {
      console.log(files, 'files')
      resolve(true)
    })
    .catch(() => {
      toast.show('取消上传操作')
    })
}
const beforeRemove = ({ file, fileList, resolve }: any) => {
  messageBox
    .confirm({
      msg: '是否删除',
      title: '提示'
    })
    .then(() => {
      toast.success('删除成功')
      resolve(true)
    })
    .catch(() => {
      toast.show('取消删除操作')
    })
}

const buildFormData = ({ file, formData, resolve }: any) => {
  let imageName = file.url.substring(file.url.lastIndexOf('/') + 1)
  // #ifdef H5
  // h5端url中不包含扩展名，可以拼接一下name
  imageName = imageName + file.name
  // #endif
  const signature = 'your <signatureString>'
  const ossAccessKeyId = 'your ossAccessKeyId'
  const policy = 'your <policyBase64Str>'
  const key = `20231120/${imageName}`
  const success_action_status = '200' // 将上传成功状态码设置为200，默认状态码为204

  formData = {
    ...formData,
    key: key,
    OSSAccessKeyId: ossAccessKeyId,
    policy: policy,
    signature: signature,
    success_action_status: success_action_status
  }
  resolve(formData)
}

const handleSuccess1 = (res: any) => {
  console.log('成功', res)
}
const handleFail1 = (res: any) => {
  console.log('失败', res)
}
const handleProgess1 = (res: any) => {
  console.log('加载中', res)
}

function handleSuccess(event: any) {
  console.log('成功', event)
}
function handleFail(event: any) {
  console.log('失败', event)
}
function handleProgess(event: any) {
  console.log('加载中', event)
}

function handleChange({ fileList: list }: any) {
  fileList.value = list
}

function handleChange1({ fileList }: { fileList: UploadFile[] }) {
  // fileList.forEach((item) => {
  //   if (!item.thumb) {
  //     item.thumb = 'https://registry.npmmirror.com/wot-design-uni-assets/*/files/redpanda.jpg'
  //   }
  // })
  fileList1.value = fileList
}
function handleChange2({ fileList }: any) {
  fileList2.value = fileList
}
function handleChange3({ fileList }: any) {
  fileList3.value = fileList
}
function handleChange4({ fileList }: any) {
  fileList4.value = fileList
}
function handleChange5({ fileList }: any) {
  fileList5.value = fileList
}
function handleChange6({ fileList }: any) {
  fileList6.value = fileList
}
function handleChange7({ fileList }: any) {
  fileList7.value = fileList
}
function handleChange8({ fileList }: any) {
  fileList8.value = fileList
}
function handleChange9({ fileList }: any) {
  fileList9.value = fileList
}
function handleChange10({ fileList }: any) {
  fileList10.value = fileList
}
function handleChange11({ fileList }: any) {
  fileList11.value = fileList
}
function handleChange12({ fileList }: any) {
  fileList12.value = fileList
}
function handleChange13({ fileList }: any) {
  fileList13.value = fileList
}
function handleChange14({ fileList }: any) {
  fileList14.value = fileList
}

const customUpload: UploadMethod = (file, formData, options) => {
  const uploadTask = uni.uploadFile({
    url: action,
    header: options.header,
    name: options.name,
    fileName: options.name,
    fileType: options.fileType,
    formData,
    filePath: file.url,
    success(res) {
      if (res.statusCode === options.statusCode) {
        // 上传成功
        options.onSuccess(res, file, formData)
      } else {
        // 上传失败
        options.onError({ ...res, errMsg: res.errMsg || '' }, file, formData)
      }
    },
    fail(err) {
      // 上传失败
      options.onError(err, file, formData)
    }
  })
  // 获取当前文件加载的百分比
  uploadTask.onProgressUpdate((res) => {
    options.onProgress(res, file)
  })
}
</script>
<style lang="scss" scoped>
.preview-cover {
  margin-top: 10rpx;
  text-align: center;
}
</style>
