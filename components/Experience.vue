
<script setup lang="ts">
import {Scene, PerspectiveCamera, Mesh,sRGBEncoding, SphereGeometry,RectAreaLight, MeshBasicMaterial, WebGLRenderer, BoxGeometry} from 'three'
import {Ref} from "vue";
import {useWindowSize} from "@vueuse/core";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";
import {GLTFLoader} from "three/examples/jsm/loaders/GLTFLoader";

const experience: Ref<HTMLCanvasElement | null> = ref(null)
const scene = new Scene()
const glftLoader = new GLTFLoader()
let renderer: WebGLRenderer
let camera: PerspectiveCamera
let controls: OrbitControls

const {width, height} = useWindowSize()
const aspectRatio = computed(()=>width.value/(height.value-150))
camera = new PerspectiveCamera(75, aspectRatio.value, 0.1, 1000)
camera.position.set(8,6,8)

const sphere = new Mesh(
    new BoxGeometry(1,1,1,1,1,1),
    new MeshBasicMaterial({color: 0x008080})
)


//scene.add(camera)
glftLoader.load('/models/rbday.gltf', gltf => {
    console.log(gltf)
    scene.add(gltf.scene)
    scene.add(gltf.cameras[0])
    const width = 100;
    const height = 100;
    const intensity = 4;
    const rectLight = new RectAreaLight( '#FFFFFF', intensity,  width, height );
    rectLight.position.set( -10, 35, 14 );
    rectLight.lookAt( 0, 0, 0 );
    scene.add(rectLight)

})
const updateRenderer = () => {
    renderer.setSize(width.value, height.value-150)
    renderer.render(scene, camera)
}
const updateCamera = () => {
    camera.aspect = aspectRatio.value
    camera.updateProjectionMatrix()
}
const setupRenderer = () => {
    if(experience.value){
        renderer = new WebGLRenderer({
            canvas: experience.value, alpha: true,
        })
        renderer.outputEncoding = sRGBEncoding
        controls = new  OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true
        updateRenderer()
    }
}
watch(aspectRatio, ()=>{
    updateRenderer()
    updateCamera()
})

const eventLoop = () => {
    controls.update()
    updateRenderer()
    //console.log(camera.position)
    requestAnimationFrame(eventLoop)
}

onMounted(()=> {
    setupRenderer()
    eventLoop()
})

</script>
<template>
    <div>
        <canvas ref="experience" />
    </div>
</template>
<style scoped>

</style>