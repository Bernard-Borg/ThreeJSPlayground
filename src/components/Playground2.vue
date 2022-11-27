<script setup lang="ts">
import *  as THREE from 'three';
import { onMounted, ref } from 'vue';

document.addEventListener('click', onClick, false);
window.addEventListener('resize', onWindowResize, false);

function onWindowResize(){
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();

    renderer.setSize(window.innerWidth, window.innerHeight);
}

const ringInnerRadius = ref<number>(0.3);
const ringOuterRadius = ref<number>(0.5);
const segments: number = 200;

const camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.01, 10);
camera.position.z = 3;

const scene = new THREE.Scene();
const renderer = renderScene();

//Add meshes
const circleMesh = addMeshToScene(new THREE.RingGeometry(ringInnerRadius.value, ringOuterRadius.value, segments), new THREE.MeshBasicMaterial({ color: 0x00ffff, side: THREE.DoubleSide }));

onMounted(() => {
    document.getElementById("playground")?.appendChild(renderer.domElement);
});

function onClick(event: MouseEvent) {
    ringInnerRadius.value += 0.1;
    ringOuterRadius.value += 0.1;    
    circleMesh.geometry = new THREE.RingGeometry(ringInnerRadius.value, ringOuterRadius.value, segments);
}

const shrinkRate: number = 0.001;
let timesAnimated = 0;

function animation(time: number) {
    timesAnimated++;
    ringInnerRadius.value -= shrinkRate;
    ringOuterRadius.value -= shrinkRate;

    if (timesAnimated % 150 == 0) {
        ringInnerRadius.value -= 0.5;
        ringOuterRadius.value -= 0.5;
    }

    circleMesh.geometry = new THREE.RingGeometry(ringInnerRadius.value, ringOuterRadius.value, segments);

	renderer.render(scene, camera);
}

function addMeshToScene(geometry: THREE.BufferGeometry, material: THREE.Material) { 
    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);
    return mesh;
}

function renderScene() {
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setAnimationLoop((time) => animation(time));
    return renderer;
}
</script>

<template>
    <div id="playground">
    </div>
</template>