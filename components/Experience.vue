
<script setup lang="ts">
import {
    Scene,
    PerspectiveCamera,
    Mesh,
    sRGBEncoding,
    SphereGeometry,
    RectAreaLight,
    MeshBasicMaterial,
    WebGLRenderer,
    BoxGeometry,
    ColorManagement,AmbientLight
} from 'three'
import {Ref} from "vue";
import {useWindowSize} from "@vueuse/core";
import {OrbitControls} from "three/examples/jsm/controls/OrbitControls";
import {GLTFLoader} from "three/examples/jsm/loaders/GLTFLoader.js";

const experience: Ref<HTMLCanvasElement | null> = ref(null)
const scene = new Scene()
const glftLoader = new GLTFLoader()
let renderer: WebGLRenderer
let camera: PerspectiveCamera
let controls: OrbitControls
ColorManagement.enabled = true

const {width, height} = useWindowSize()
const aspectRatio = computed(()=>width.value/(height.value-150))
camera = new PerspectiveCamera(75, aspectRatio.value, 0.1, 1000)
camera.position.set(8,6,8)

const sphere = new Mesh(
    new BoxGeometry(1,1,1,1,1,1),
    new MeshBasicMaterial({color: 0x008080})
)


//scene.add(camera)
try {
    glftLoader.load('/models/rbday.gltf', gltf => {
        console.log(gltf)
        scene.add(gltf.scene)
        scene.add(gltf.cameras[0])
        const width = 25;
        const height = 25;
        const intensity = 15;
        const rectLight = new RectAreaLight( '#FFFFFF', intensity,  width, height );
        const light = new AmbientLight( 0x404040,15); // soft white light
        rectLight.position.set( -10, 35, 14 );
        rectLight.lookAt(0,0,0)
        scene.add( light );
        scene.add( rectLight );

    })
} catch (e) {
    console.log('weird error')
}

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
        renderer.outputEncoding = sRGBEncoding;
        renderer.gammaOutput = true;
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