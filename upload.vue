<template>
    <div>
        <div class="upload-wrap">
            <div class="img-box" v-for="(item, index) in imgUrl" v-show="imgUrl.length != 0">
                <div class="delete-box" @click="deleteImg(index)">×</div>
                <img width="80" height="80" :src="imgHost + item.url" />
            </div>
            <div>
                <form id='form' enctype="multipart/form-data">
                    <label for="upload">
                        <span class="loading-box" v-show="loading"><img src="../assets/images/load.png" /></span>
                        上传图片
                        <input type="file" id="upload" name="file" multiple accept="image/*" @change="beforeUpload" />
                    </label>
                </form>
            </div>
        </div>
    </div>
</template>
<style>
    .upload-wrap {
        display: flex;
    }
    .upload-wrap label {
        position: relative;
        display: block;
        width: 80px;
        height: 80px;
        background-color: white;
        line-height: 80px;
        text-align: center;
        border: 1px dashed #d9d9d9;
    }
    .upload-wrap label input {
        display: none;
    }
    .upload-wrap .loading-box {
        position: absolute;
        left: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        width: 80px;
        height: 80px;
        background-color: rgba(0, 0, 0, 0.7);
    }
    .upload-wrap .loading-box img {
        animation: rotate 1s linear infinite;
    }
    .upload-wrap .img-box {
        position: relative;
        margin-right: 10px;
    }
    .upload-wrap .img-box .delete-box {
        position: absolute;
        top: 5px;
        right: 5px;
        width: 20px;
        height: 20px;
        line-height: 20px;
        text-align: center;
        color: #ffffff;
        font-size: 22px;
        border-radius: 50%;
        background-color: rgba(0,0,0,0.7);
    }
    @keyframes rotate {
        0% {
            transform: rotate(0deg);
        }
        50% {
            transform: rotate(180deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>
<script>
    import { MessageBox } from 'mint-ui'
    import { Toast } from 'mint-ui'

    export default {
        props: ['URL'],
        data() {
            return {
                imgUrl: [],
                loading: false
            }
        },
        computed: {
            imgHost() {
                return window.imgUrl
            }
        },
        methods: {
            init(url) {
                this.imgUrl.push({
                    name: 'img',
                    url: url
                })
            },
            beforeUpload() {
                let reg = /.*?(gif|png|bmp|jpg)/
                let img = document.getElementById('upload')
                let result = reg.test(img.value)
                let length = this.imgUrl.length
                if (length < 1) {
                    if (!result) {
                        Toast('只能上传格式为gif,png,bmp的图片文件')
                        return false
                    } else {
                        this.upload()
                    }
                } else {
                    Toast('最多允许上传一张')
                }
            },
            upload() {
                this.loading = true
                let form = document.getElementById('form')
                let formData = new FormData(form)
                this.apiUpload(this.URL, formData).then((res) => {
                    this.loading = false
                    if (res.code == 200) {
                        this.imgUrl.push({
                            name: 'img',
                            url: res.data
                        })
                        this.sendUrl(res.data)
                    } else {
                        this.dealError(res)
                    }
                })
            },
            deleteImg(index) {
                MessageBox({
                    title: '提示',
                    message: '确定删除图片?',
                    showCancelButton: true
                }).then(action => {
                    if (action == 'confirm') {
                        Toast({
                            message: '删除成功',
//							iconClass: 'icon icon-success',
                            duration: 1000
                        })
                        this.imgUrl.splice(index, 1)
                    }
                })
            },
            sendUrl(url) {
                this.$emit('callback', url)
            }
        }
    }
</script>
