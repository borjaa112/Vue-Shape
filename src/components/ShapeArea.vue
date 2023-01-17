<template>
    <vue-resizable :dragSelector="dragSelector" :fit-parent="false" ref="resizableComponent" :active="handlers">
        <div :style="shapeStyle" class="resizable-content">
        </div>
    </vue-resizable>

</template>

<script>
import { computed, defineComponent } from "@vue/runtime-core";
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
        console.log(props.modeSelected.value);
        const dragSelector = '.resizable-content'
        // const active = computed(() => {
        //     props.modeSelected.value
        // })
        const shapeStyle = computed(() => ({
            position: "absolute",
            top: props.shapeState.top.value + "px",
            left: props.shapeState.left.value + "px",
            width: props.shapeState.width.value + "px",
            height: props.shapeState.height.value + "px",
            border: "1px solid red",
            backgroundColor: "#FF000055"

        }))

        function eHandler(data) {
            props.shapeState.width = data.width
            props.shapeState.height = data.height
            props.shapeState.left = data.left
            props.shapeState.top = data.top

        }

        const handlers = computed(() => {
            return props.modeSelected.value == 'move' ? ["r", "rb", "b", "lb", "l", "lt", "t", "rt"] : []
        })


        return {
            shapeStyle,
            eHandler,
            dragSelector,
            handlers
        }
    }
})
</script>

<style scoped>
.selection-area {
    position: absolute;
    border: 1px solid red;
    background-color: #FF000055;
}
</style>