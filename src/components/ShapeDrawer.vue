<template>
    <div @mousedown="drawStart" @mouseup="drawEnd" @mousemove="continuousDivDrawer" class="container">
        <slot></slot>
    </div>
</template>

<script>
import { h, defineComponent } from "@vue/runtime-core";
import { ref } from "vue";

const startCoords = ref({})
const endCoords = ref({})

export default defineComponent({
    setup() {
        function drawStart(event) {

            const rect = event.target.getBoundingClientRect();
            startCoords.value = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            }
        }

        function drawEnd(event) {
            const rect = event.target.getBoundingClientRect();

            endCoords.value = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            }

            drawDiv(startCoords.value, endCoords.value)
        }

        function drawDiv(startCoords, endCoords) {
            const minX = Math.min(startCoords.x, endCoords.x)
            const maxX = Math.max(startCoords.x, endCoords.x)

            const minY = Math.min(startCoords.y, endCoords.y)
            const maxY = Math.max(startCoords.y, endCoords.y)

            const div = document.createElement("div",)
            div.className = 'selection-zone'
            div.style.position = 'absolute'
            div.style.top = minY + "px"
            div.style.left = minX + "px"
            div.style.right = maxX + "px"


            div.style.width = maxX - minX + "px"
            div.style.height = maxY - minY + "px"

            const container = document.body.querySelector(".container")
            container.append(div)
        }

        function continuousDivDrawer(event) {
            console.log(event);
        }

        return {
            drawStart,
            drawEnd,
            continuousDivDrawer
        }
    }
})
</script>

<style>
.selection-zone {
    border: 1px solid red;
    background-color: #FF000055;

}
</style>