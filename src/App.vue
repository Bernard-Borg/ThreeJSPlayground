<script setup lang="ts">
import SpinningCircle from "./components/SpinningCircle.vue";
import Playground2 from "./components/Playground2.vue";
import { ref } from "vue";
import type { Component } from "vue";

const options: Record<string, Component | undefined> = {
    "SpinningCircle": SpinningCircle,
    "Playground2": Playground2,
    "Playground3": undefined
}

const optionsMap = new Map(Object.entries(options));
const playgrounds = Object.keys(options);

let timeout: ReturnType<typeof setTimeout> | null = null;
const displayPlaygrounds = ref<boolean>(false);

const currentPlayground = ref<string>("SpinningCircle");

function setCurrentPlayground(playground: string) {
    currentPlayground.value = playground;
}

function hidePlaygrounds() {
    timeout = setTimeout(() => {
        displayPlaygrounds.value = false
    }, 200)
}

function clearHidePlaygroundsTimeout() {
    if (timeout != null) {
        clearTimeout(timeout);
    }
}

let tooltip: HTMLSpanElement | null = null;

function showTooltip(event: MouseEvent) {
    tooltip = document.createElement("span");
    tooltip.id = "temp-tooltip";
    tooltip.innerText = "Coming soon!";
    tooltip.style.top = `${event.pageY - 50}px`;
    tooltip.style.left = `${event.pageX -  70}px`;
    
    document.body.appendChild(tooltip);
}

function clearTooltip() {
    tooltip?.remove();
}
</script>

<template>
    <div id="playground-overlay">
        <h1 id="playground-title">{{ currentPlayground }}</h1>
        <div id="playgrounds-dropdown">
            <button id="playground-chooser" @mouseover="displayPlaygrounds = true" @mouseleave="hidePlaygrounds">Change Playground  <i class="fa-solid fa-angle-down"></i></button>
            <div id="playgrounds-options" v-if="displayPlaygrounds" @mouseover="clearHidePlaygroundsTimeout" @mouseleave="displayPlaygrounds = false">
                <div v-for="playground in playgrounds">
                    <button class="active-playground" v-if="optionsMap.has(playground) && optionsMap.get(playground)" @click="setCurrentPlayground(playground)">
                        {{ playground }}
                    </button>
                    <button class="unpublished-playground" v-else @mouseover="showTooltip" @mouseleave="clearTooltip" style="color: lightgrey">
                        {{ playground }}
                    </button>
                </div>
            </div>
        </div>
    </div>
    <component :is="optionsMap.has(currentPlayground) ? optionsMap.get(currentPlayground) : ''" />
</template>

<style>
@import "../node_modules/@fortawesome/fontawesome-free/css/all.min.css";

#playground-overlay {
    position: absolute;
    color: white;
    margin: 20px;
    font-family: 'Inter', 'Segoe UI', sans-serif;
    display: flex;
}

#playground-chooser {
    background-color: white;
    color: black;
    border: none;
    border-radius: 5px;
    padding: 10px;
    margin-left: 20px;
    margin-top: 20px;
    margin-bottom: 10px;
    margin-right: 20px;
    font-family: 'Inter', 'Segoe UI', sans-serif;
    font-size: 20px;
}

#playgrounds-dropdown {
    display: flex;
    align-items: center;
    flex-direction: column;
}

#playgrounds-options {
    background-color: white;
    display: flex;
    flex-direction: column;
    width: 200px;
    border-radius: 5px;
}

#playgrounds-options button {
    border: none;
    padding-top: 12px;
    padding-bottom: 12px;
    font-family: 'Inter', 'Segoe UI', sans-serif;
    font-size: 18px;
    border-radius: 5px;
    width: 100%;
}

#playgrounds-options .active-playground:hover, #playground-chooser:hover {
    background-color: lightgrey;
    cursor: pointer;
}

#temp-tooltip {
    position: absolute;
    background-color: white;
    border-radius: 5px;
    padding: 15px;
    width: 140px;
    text-align: center;
    color: black;
    border: 1px solid black;
    font-family: 'Inter', 'Segoe UI', sans-serif;
    box-sizing: border-box;
    pointer-events: none;
}

#temp-tooltip:before {
    content: "";
    position: absolute;
    top: 100%;
    left: 50%;
    margin-left: -8px;
    border: 8px solid transparent;
    border-top: 8px solid white;
    z-index: 2;
}

#temp-tooltip:after {
    content: "";
    position: absolute;
    top: 100%;
    left: 50%;
    margin-left: -8px;
    margin-top: 1px;
    border: 8px solid transparent;
    border-top: 8px solid black;
    z-index: 1;
}
</style>