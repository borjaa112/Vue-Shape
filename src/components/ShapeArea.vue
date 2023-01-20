<template>
    <vue-resizable :dragSelector="dragSelector" :fit-parent="false" ref="resizableElement" :active="handlers"
        style="position: relative" :width="shapeState.width.value" :height="shapeState.height.value"
        :left="shapeState.left.value" :top="shapeState.top.value" @resize:move="moveHandler" @resize:start="moveHandler"
        @resize:end="moveHandler" @drag:move="moveHandler" @drag:start="moveHandler" @drag:end="moveHandler"
        class="resizable-content selection-area">
        <button @click="deleteHandler">Delete</button>
    </vue-resizable>

</template>

<script>
import { computed, defineComponent, onMounted, ref, watch } from "@vue/runtime-core";
import VueResizable from 'vue-resizable'
export default defineComponent({
    emits: ['delete'],
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
        const resizableElement = ref(null)

        onMounted(() => {
            if (props.modeSelected.value === 'move') {
                resizableElement.value.$el.classList.add('drag-el')
            }
        })
        watch(props.modeSelected, (newMode) => {
            if (newMode === 'move') {
                resizableElement.value.$el.classList.add('drag-el')
            }
            if (newMode === 'draw') {
                resizableElement.value.$el.classList.remove('drag-el')
            }
        })


        watch(props.shapeState.width, () => {
            if (props.modeSelected.value === 'move') {
                resizableElement.value.$el.classList.add('drag-el')

            }
            if (props.modeSelected.value === 'draw') {
                resizableElement.value.$el.classList.remove('drag-el')
            }
        })

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
            deleteHandler,
            resizableElement
        }
    }
})
</script>

<style scoped>
.selection-area {
    border: 1px solid red;
    background-color: #FF000055;
}
</style>