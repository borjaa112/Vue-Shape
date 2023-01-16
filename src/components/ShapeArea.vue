<template>
    <div :style="shapeStyle">
    </div>

</template>

<script>
import { computed, defineComponent } from "@vue/runtime-core";

export default defineComponent({
    props: {
        shapeState: {
            type: Object
        }
    },
    setup(props) {
        const shapeStyle = computed(() => ({
            position: "absolute",
            top: props.shapeState.top.value + "px",
            left: props.shapeState.left.value + "px",
            width: props.shapeState.width.value + "px",
            height: props.shapeState.height.value + "px",
            border: "1px solid red",
            backgroundColor: "#FF000055"

        }))


        return {
            shapeStyle
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