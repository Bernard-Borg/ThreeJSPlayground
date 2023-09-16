<script setup lang="ts">
import *  as THREE from 'three';
import { onMounted, onUnmounted, ref } from 'vue';
import { useMouse, watchDebounced } from "@vueuse/core";
import { watch } from "fs";

const { x, y } = useMouse();

const addMeshToScene = (scene: THREE.Scene, geometry: THREE.BufferGeometry, material: THREE.Material) => { 
    const axesHelper = new THREE.AxesHelper( 1 );
    scene.add( axesHelper );

    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);
    return mesh;
}

const renderScene = () => {
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setAnimationLoop(animation);
    return renderer;
}

const camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.01, 10);
camera.position.z = 1;

const scene = new THREE.Scene();
const renderer = renderScene();

const boxMesh = addMeshToScene(scene, new THREE.BoxGeometry(0.3, 0.3, 0.3), new THREE.MeshNormalMaterial());

const onWindowResize = () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
}

onMounted(() => {
    document.getElementById("playground")?.appendChild(renderer.domElement);
    window.addEventListener('resize', onWindowResize, false);
});

onUnmounted(() => {
    window.removeEventListener('resize', onWindowResize, false);
});

// const rotationX = ref<number>(0);
// const rotationY = ref<number>(0);

// let previousTime = 0;
// let previousBoxMeshRotationX = 0;
// let previousBoxMeshRotationY = 0;

function animation(time: number) {
    const speed = 0.03;
	// boxMesh.rotation.x = time / (x.value + 1);
	// boxMesh.rotation.y = time / (y.value + 1);
	// boxMesh.rotation.z = time / (1 + 1);

    // const centreX = window.innerWidth / 2;
    // const centerY = window.innerHeight / 2;

    boxMesh.rotateOnAxis(new THREE.Vector3(1, 0, 0), speed);

	renderer.render(scene, camera);
}

watchDebounced([x, y], (newValue) => {
    boxMesh.rotateZ(0.785398);
}, { debounce: 150 });
</script>

<template>
    <span id="currentRotation">Rotation X: {{x}}, Rotation Y: {{y}}</span>
    <div id="playground">
    </div>
</template>

<style>
#currentRotation {
    position: absolute;
    width: 100%;
    color: white;
    font-family: 'Inter', 'Segoe UI', sans-serif;
    text-align: center;
    top: 10px;
}
</style>