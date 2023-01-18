<template>
    <vue-resizable :dragSelector="dragSelector" :fit-parent="false" ref="resizableComponent" :active="handlers"
        style="position: relative" :width="shapeState.width.value" :height="shapeState.height.value"
        :left="shapeState.left.value" :top="shapeState.top.value" @resize:move="eHandler" @resize:start="eHandler"
        @resize:end="eHandler" @drag:move="eHandler" @drag:start="eHandler" @drag:end="eHandler"
        class="resizable-content selection-area">
    </vue-resizable>

</template>

<script>
import { computed, defineComponent, watch } from "@vue/runtime-core";
import VueResizable from 'vue-resizable'

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
    setup(props) {


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

        function eHandler(data) {
            props.shapeState.width.value = data.width
            props.shapeState.height.value = data.height
            props.shapeState.left.value = data.left
            props.shapeState.top.value = data.top

        }

        const handlers = computed(() => {
            return props.modeSelected.value == 'move' ? ["r", "rb", "b", "lb", "l", "lt", "t", "rt"] : []
        })


        return {
            eHandler,
            dragSelector,
            handlers
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