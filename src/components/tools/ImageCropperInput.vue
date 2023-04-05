<script setup>
import { ref, computed } from 'vue'
import 'vue-cropper/dist/index.css'
import { VueCropper } from 'vue-cropper'

const props = defineProps({
    modelValue: String,
    label: String,
    size: {
        type: Array,
        default: [100, 100]
    }
})
const emit = defineEmits(["update:modelValue"])
console.log(props);
const imageStyle = computed(() => {
    return {
        width: props.size[0] + 'px',
        height: props.size[1] + 'px',
    }
})
const visible = ref(false)
const cropper = ref()
const source = ref()
const reader = new FileReader()
reader.onload = (e) => {
    source.value = e.target.result
    visible.value = true
}
const selected = computed(() => props.modelValue != "")
const selectImage = (file) => {
    console.log(file);
    reader.readAsDataURL(file.raw)
}
const getCrop = () => {
    cropper.value.getCropData((data) => {
        emit("update:modelValue", data)
        visible.value = false
    });
    // 获取截图的 blob 数据
    //console.log(cropper.value);
    // cropper.value.getCropBlob(data => {
    //     // console.log(111);
    //     let formData = new FormData();
    //     //第三个参数是规定以什么为后缀，接口是根据后缀来返回地址格式的
    //     formData.append("file", data, 'chris.jpg');
    //     //上传接口
    //     // uploadApi(formData).then(res => {

    //     // })
    // })
}
const clearImage = () => {
    source.value = ""
    emit("update:modelValue", "")
}
const cancel = () => {
    clearImage()
    visible.value = false
}
</script>
  
<template>
    <div>
        <div class="selector-container">
            <input type="checkbox" class="selected" :checked="selected">
            <div class="selector">
                <el-upload action="#" :on-change="selectImage" accept="image/png, image/jpeg, image/jpg"
                    :show-file-list="false" :auto-upload="false">
                    <el-avatar class="image-preview" shape="square" fit="fill" :src="modelValue" :style="imageStyle">
                        <template #default>
                            <div class="plus-icon">
                                <el-icon :size="25" color="#409eff">
                                    <Plus />
                                </el-icon>
                                <span class="text">{{ label }}</span>
                            </div>
                        </template>
                    </el-avatar>
                </el-upload>
                <el-icon class="close" :size="20" color="#fff" @click="clearImage">
                    <CircleClose />
                </el-icon>
            </div>
        </div>
        <el-dialog v-model="visible" :open-delay="200" :close-on-click-modal="false" :show-close="false">
            <div class="cropper-contaienr">
                <VueCropper class="cropper" ref="cropper" :img="source" :autoCrop="true" :centerBox="true" :fixed="true"
                    :fixedNumber="size" mode="contain" />
            </div>
            <template #footer>
                <el-button @click="cancel">取 消</el-button>
                <el-button @click="getCrop" type="primary">剪裁图片</el-button>
            </template>
        </el-dialog>
    </div>
</template>

<style lang="scss" scoped>
.selector-container {

    .selected {
        display: none;

        // opacity: 0;
        &:checked {
            +.selector:hover {
                .image-preview {
                    filter: brightness(50%);
                }

                .close {
                    display: block;
                    cursor: pointer;
                }
            }

        }
    }
}

.close {
    display: none;
    position: absolute;
    top: 10px;
    right: 10px;
}


.plus-icon {
    position: relative;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #fff;
    border: 1px dashed #409eff;
    border-radius: 4px;

    .text {
        position: absolute;
        color: #409eff;
        top: 70%;
        font-size: 12px;
    }
}


.cropper-contaienr {
    width: 100%;
    display: flex;
    justify-content: center;

    .cropper {
        width: 600px;
        height: 300px;
    }
}
</style>