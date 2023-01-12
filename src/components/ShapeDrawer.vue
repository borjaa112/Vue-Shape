<template>
    <div @mousedown="drawStart" @mouseup="drawEnd" @mousemove="continuousDivDrawer" class="container">
        <div ref="wrapper" class="wrapper">
            <slot></slot>
        </div>
    </div>
</template>

<script>
import { h, defineComponent } from "@vue/runtime-core";
import { ref } from "vue";

const startCoords = ref({})
const endCoords = ref({})
let currentDiv = null

export default defineComponent({
    setup() {
        const wrapper = ref()
        function drawStart(event) {
            const rect = wrapper.value.getBoundingClientRect();
            startCoords.value = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            }

            currentDiv = document.createElement("div")
            currentDiv.className = 'selection-area'
            currentDiv.style.position = 'absolute'
            currentDiv.style.top = startCoords.value.y + "px"
            currentDiv.style.left = startCoords.value.x + "px"

            const container = document.body.querySelector(".container")
            container.append(currentDiv)
        }

        function drawEnd(event) {
            const rect = wrapper.value.getBoundingClientRect();

            endCoords.value = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            }

            currentDiv = null;


            // drawDiv(startCoords.value, endCoords.value)
            // startCoords.value = {}
            // endCoords.value = {}
        }

        function drawDiv(startCoords, endCoords) {
            const div = document.createElement("div")
            div.className = 'selection-area'
            const minX = Math.min(startCoords.x, endCoords.x)
            const maxX = Math.max(startCoords.x, endCoords.x)

            const minY = Math.min(startCoords.y, endCoords.y)
            const maxY = Math.max(startCoords.y, endCoords.y)


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
            if (startCoords.value.x && currentDiv) {
                const rect = wrapper.value.getBoundingClientRect();
                const currentX = event.clientX - rect.left;
                const currentY = event.clientY - rect.top;

                console.log(event.target);
                console.log("Eje X: " + currentX + " Eje Y: " + currentY);

                // calculate minX, minY, maxX y maxY
                const minX = Math.min(startCoords.value.x, currentX);
                const maxX = Math.max(startCoords.value.x, currentX);
                const minY = Math.min(startCoords.value.y, currentY);
                const maxY = Math.max(startCoords.value.y, currentY);
                // Update position and size of the div
                currentDiv.style.top = minY + "px"
                currentDiv.style.left = minX + "px"
                currentDiv.style.width = maxX - minX + "px"
                currentDiv.style.height = maxY - minY + "px"
            }
        }

        return {
            drawStart,
            drawEnd,
            continuousDivDrawer,
            wrapper
        }
    }
})
</script>

<style>
.selection-area {
    border: 1px solid red;
    background-color: #FF000055;

}
</style>