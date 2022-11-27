<script setup lang="ts">
import *  as THREE from 'three';
import { onMounted, ref } from 'vue';

document.addEventListener('mousemove', onDocumentMouseMove, false); 

const camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.01, 10);
camera.position.z = 1;

const scene = new THREE.Scene();
const renderer = renderScene();

//Add meshes
const boxMesh = addMeshToScene(new THREE.BoxGeometry(0.3, 0.3, 0.3), new THREE.MeshNormalMaterial());

onMounted(() => {
    document.getElementById("playground")?.appendChild(renderer.domElement);
});

let previousTime = 0;
let previousBoxMeshRotationX = 0;
let previousBoxMeshRotationY = 0;

function animation(time: number, positionX: number, positionY: number) {
	boxMesh.rotation.x = time / (positionX + 1);
	boxMesh.rotation.y = time / (positionY + 1);

    rotationX.value = Math.round(Math.abs(boxMesh.rotation.x - previousBoxMeshRotationX) / Math.abs(time / 1000 - previousTime / 1000) * 100) / 100;
    rotationY.value = Math.round(Math.abs(boxMesh.rotation.y - previousBoxMeshRotationY) / Math.abs(time / 1000 - previousTime / 1000) * 100) / 100;

	renderer.render(scene, camera);

    previousTime = time;
    previousBoxMeshRotationX = boxMesh.rotation.x;
    previousBoxMeshRotationY = boxMesh.rotation.y;
}

const rotationX = ref<number>(0);
const rotationY = ref<number>(0);

let timeout: ReturnType<typeof setTimeout> | null = null;

function onDocumentMouseMove(event: MouseEvent) {
    if (timeout != null) {
        clearTimeout(timeout);
    }

    timeout = setTimeout(() => {
        renderer.setAnimationLoop((time) => animation(time, event.pageX, event.pageY));
    }, 50);
}

function addMeshToScene(geometry: THREE.BufferGeometry, material: THREE.Material) { 
    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);
    return mesh;
}

function renderScene() {
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setAnimationLoop((time) => animation(time, 2000, 2000));
    return renderer;
}
</script>

<template>
    <span id="currentRotation">Rotation X: {{rotationX}}, Rotation Y: {{rotationY}}</span>
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