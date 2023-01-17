<template>
    <button :onclick="selectionMode" value="draw">Draw</button>
    <button :onclick="selectionMode" value="move">Move</button>
    <div @mousedown="drawStart" @mouseup="drawEnd" @mousemove="continuousDivDrawer" class="container">
        <div ref="wrapper" class="wrapper" style="position: relative; ">
            <div>
                <slot></slot>
            </div>
            <span v-for="(shape, index) in shapesArray" :key="index" style="position: absolute; top: 0;">
                <component :is="shape" />
            </span>
        </div>
    </div>
</template>

<script>
import { h, defineComponent } from "@vue/runtime-core";
import { ref } from "vue";
import ShapeArea from "./ShapeArea.vue";
const startCoords = ref({})
const endCoords = ref({})

export default defineComponent({
    components: {
        ShapeArea
    },
    setup() {
        const modeSelected = ref()
        const shapesArray = ref([])

        function createShapeState() {
            return {
                top: ref(),
                left: ref(),
                width: ref(),
                height: ref()
            }
        }
        let shapeState = null
        const wrapper = ref()
        let currentShape = null
        function drawStart(event) {
            if (modeSelected.value !== 'draw') {
                return
            }
            const rect = wrapper.value.getBoundingClientRect();
            startCoords.value = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            }

            shapeState = createShapeState()

            shapeState.top.value = startCoords.value.y
            shapeState.left.value = startCoords.value.x
            currentShape = h(ShapeArea, { shapeState, modeSelected })
            shapesArray.value.push(currentShape)

        }

        function drawEnd(event) {
            const rect = wrapper.value.getBoundingClientRect();

            endCoords.value = {
                x: event.clientX - rect.left,
                y: event.clientY - rect.top
            }
            currentShape = null;
            shapeState = null


            // drawDiv(startCoords.value, endCoords.value)
            // startCoords.value = {}
            // endCoords.value = {}
        }


        function continuousDivDrawer(event) {
            if (startCoords.value.x && shapeState !== null) {
                const rect = wrapper.value.getBoundingClientRect();
                const currentX = event.clientX - rect.left;
                const currentY = event.clientY - rect.top;


                // calculate minX, minY, maxX y maxY
                const minX = Math.min(startCoords.value.x, currentX);
                const maxX = Math.max(startCoords.value.x, currentX);
                const minY = Math.min(startCoords.value.y, currentY);
                const maxY = Math.max(startCoords.value.y, currentY);
                // Update position and size of the div

                shapeState.top.value = minY
                shapeState.left.value = minX
                shapeState.width.value = maxX - minX
                shapeState.height.value = maxY - minY
            }
        }

        function selectionMode(event) {
            modeSelected.value = event.target.value
        }

        return {
            drawStart,
            drawEnd,
            continuousDivDrawer,
            wrapper,
            shapesArray,
            selectionMode,
            modeSelected
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