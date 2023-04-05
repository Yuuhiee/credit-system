<script setup>
import { ref, computed } from 'vue';

defineProps({
    label: String,
    file: Object,
    buttonStyle: Object
})
const uploadRef = ref()
const dialogVisible = ref(false)
const dialogImageSrc = ref("")
const reader = new FileReader()
// const file = ref(null)
const imageSrc = computed(() => { })
reader.onload = (e) => {
    dialogImageSrc.value = e.target.result
}
const handleRemove = (file) => {
    console.log(file)
}
const handleExceed = (files) => {
    uploadRef.value.clearFiles()
    uploadRef.value.handleStart(files[0])
}
const handlePictureCardPreview = (file) => {
    reader.readAsDataURL(file.raw)
    dialogVisible.value = true
}
</script>
  
<template>
    <el-upload action="#" ref="uploadRef" :limit="1" :on-exceed="handleExceed" accept="image/png, image/jpeg, image/jpg"
        :auto-upload="false">

        <template #file="{ file }">
            <div class="el-upload-list__item-file-name image-info" @click="handlePictureCardPreview(file)">
                <el-icon><zoom-in /></el-icon>
                <span>{{ file.name }}</span>
            </div>
        </template>
        <template #trigger>
            <el-button :type="buttonStyle.type" :plain="buttonStyle.plain">{{ label }}</el-button>
        </template>
    </el-upload>
    <el-dialog v-model="dialogVisible">
        <img w-full :src="dialogImageSrc" alt="Preview Image" />
    </el-dialog>
</template>

<style lang="scss" >
.el-upload-list__item {
    cursor: pointer;
    transform: translate(110%, calc(-100% - 10px));
    margin-top: 0;

    &:hover {
        color: #409EFF;
    }
}

.image-info .el-icon {
    transform: translate(-5px, 2px);
}
</style>