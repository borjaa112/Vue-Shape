<template>
    <vue-resizable :dragSelector="dragSelector" :fit-parent="false" ref="resizableComponent" :active="handlers"
        style="position: relative" :width="shapeState.width.value" :height="shapeState.height.value"
        :left="shapeState.left.value" :top="shapeState.top.value" @resize:move="moveHandler" @resize:start="moveHandler"
        @resize:end="moveHandler" @drag:move="moveHandler" @drag:start="moveHandler" @drag:end="moveHandler"
        class="resizable-content selection-area">
        <button @click="deleteHandler">Delete</button>
    </vue-resizable>

</template>

<script>
import { computed, defineComponent, watch } from "@vue/runtime-core";
import { defineEmits } from "@vue/runtime-core";
import VueResizable from 'vue-resizable'
const emit = defineEmits(['delete'])
export default defineComponent({
    components: {
        VueResizable
    },
    props: {
        shapeState: {
            type: Object
        },
        modeSelected: {
            type: Object
        }
    },
    setup(props, { emit }) {


        const dragSelector = '.resizable-content'
        // const active = computed(() => {
        //     props.modeSelected.value
        // })

        watch(props.modeSelected, (newMode) => {
            if (newMode === 'move') {
                const elements = document.querySelectorAll(".resizable-content")
                for (let element of elements.entries()) {
                    element[1].classList.add('drag-el')
                }
            }
            if (newMode === 'draw') {
                const elements = document.querySelectorAll(".resizable-content")
                for (let element of elements.entries()) {
                    element[1].classList.remove('drag-el')
                }
            }
        }, { immediate: true })


        watch(props.shapeState.width, () => {
            if (props.modeSelected.value === 'move') {
                const elements = document.querySelectorAll(".resizable-content")
                for (let element of elements.entries()) {
                    element[1].classList.add('drag-el')
                }
            }
            if (props.modeSelected.value === 'draw') {
                const elements = document.querySelectorAll(".resizable-content")
                for (let element of elements.entries()) {
                    element[1].classList.remove('drag-el')
                }
            }
        }, { immediate: true })

        function moveHandler(data) {
            props.shapeState.width.value = data.width
            props.shapeState.height.value = data.height
            props.shapeState.left.value = data.left
            props.shapeState.top.value = data.top

        }

        const handlers = computed(() => {
            return props.modeSelected.value == 'move' ? ["r", "rb", "b", "lb", "l", "lt", "t", "rt"] : []
        })
        function deleteHandler() {
            emit('delete')
        }


        return {
            moveHandler,
            dragSelector,
            handlers,
            deleteHandler
        }
    }
})
</script>

<style scoped>
.selection-area {
    /* position: absolute; */
    border: 1px solid red;
    background-color: #FF000055;
}
</style>