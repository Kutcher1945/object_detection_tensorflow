<template>
    <div class="w-full h-auto">
        <video nef="video" class="w-full h-full mx-auto" autoplay playsinline></video>

        <Selector v-model="camera">
            <option value="">Change Camera</option>
            <option v-for="(item, index) in devices" :key="index" :value="item.deviceId">{{ item.label }}</option>
        </Selector>

    </div>
</template>

<script lang="ts">
import { defineAsyncComponent, onMounted, ref } from 'vue';

const Selector = defineAsyncComponent(()=>import(/*webpackChunkName*/'../components/Selector.vue'))

const video = ref<HTMLVideoElement>()
const devices = ref<MediaDeviceInfo[]>([])
const camera = ref<string>("")
onMounted(async ()=>{
    if("mediaDevices" in navigator && "getUserMedia" in navigator.mediaDevices)
    {
        devices.value = await navigator.mediaDevices.enumerateDevices()
        devices.value = devices.value.filter(item => item.kind == 'videoinput')
        camera.value = devices.value[0].deviceId
        startStreaming()
    }
})

function startStreaming() : void
{
    navigator.mediaDevices.getUserMedia({
        video:{
            deviceId: (camera.value as string)
        }
    }).then((stream : MediaStream) =>{
        (video.value as HTMLVideoElement).srcObject = stream
    })  
}

</script>