<template>
    <div class="attr-list">
        <CommonAttr></CommonAttr>
        <EditDropDown></EditDropDown>
    </div>
</template>

<script>
import CommonAttr from '@/custom-component/common/CommonAttr.vue'
import EditDropDown from './EditDropDown'

export default {
    components: { EditDropDown, CommonAttr },
    computed: {
        curComponent() {
            return this.$store.state.curComponent
        },
    },
}
</script>
