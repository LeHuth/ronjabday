
<script setup lang="ts">
import {Scene, PerspectiveCamera, Mesh, SphereGeometry, MeshBasicMaterial, WebGLRenderer} from 'three'
import {Ref} from "vue";
import {useWindowSize} from "@vueuse/core";
const experience: Ref<HTMLCanvasElement | null> = ref(null)
const scene = new Scene()
const {width, height} = useWindowSize()
const aspectRatio = computed(()=>width.value/height.value)
const camera = new PerspectiveCamera(75, aspectRatio.value, 0.1, 1000)
camera.position.set(0,0,3)
const sphere = new Mesh(
    new SphereGeometry(1,32,32),
    new MeshBasicMaterial({color: 0x008080})
)



scene.add(camera)
scene.add(sphere)


onMounted(()=> {
    if(experience.value){
        const renderer = new WebGLRenderer({
            canvas: experience.value
        })
        renderer.setSize(width.value, height.value)
        renderer.render(scene, camera)
    }
})

</script>
<template>
    <div>
        <canvas ref="experience" />
    </div>
</template>
<style scoped>

</style>