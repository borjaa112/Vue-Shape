<template>
    <button :onclick="selectionMode" value="draw">Draw</button>
    <button :onclick="selectionMode" value="move">Move</button>
    <button :onclick="undo">Undo</button>
    <button :onclick="redo">Redo</button>
    <div @mousedown="drawStart" @mouseup="drawEnd" @mousemove="continuousDivDrawer" class="container">
        <div ref="wrapper" class="wrapper" style="position: relative; ">
            <div>
                <slot></slot>
            </div>
            <span v-for="(shape, index) in shapesArray" :key="index">
                <component :is="shape" @delete="deleteChild(index)" />
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
        const modeSelected = ref('draw')
        const shapesArray = ref([])
        const undoElements = []

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

        function undo() {
            if (shapesArray.value.length > 0) {
                undoElements.push(shapesArray.value.pop())
            }
        }

        function redo() {
            if (undoElements.length > 0) {
                shapesArray.value.push(undoElements.pop())
            }
        }

        function deleteChild(index) {
            shapesArray.value.splice(index, 1)
        }
        return {
            drawStart,
            drawEnd,
            continuousDivDrawer,
            wrapper,
            shapesArray,
            selectionMode,
            modeSelected,
            undo,
            redo,
            deleteChild
        }

    }
})
</script>

<style scooped>
.selection-area {
    border: 1px solid red;
    background-color: #FF000055;

}

span {
    position: absolute;
    top: 0;
    height: 0;
    width: 0;
}
</style>