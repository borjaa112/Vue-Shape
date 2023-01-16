<template>
    <vue-resizable :dragSelector="dragSelector" :fit-parent="fit">
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
        }
    },
    setup(props) {
        const dragSelector = '.resizable-content'
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
            console.log("hol");
            props.shapeState.width = data.width
            props.shapeState.height = data.height
            props.shapeState.left = data.left
            props.shapeState.top = data.top

        }


        return {
            shapeStyle,
            eHandler,
            dragSelector
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