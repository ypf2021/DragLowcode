<template>
    <label for="dropdown">
        <select class="v-dropdown">
            <option
                v-for="(item, index) in propValue.data"
                :key="index"
                :value="item"
            >
                <div>{{ item }}</div>
            </option>
        </select>
    </label>
</template>

<script>
import request from '@/utils/request'
import OnEvent from '../common/OnEvent'

export default {
    extends: OnEvent,
    props: {
        propValue: {
            type: Object,
            default: () => {},
        },
        request: {
            type: Object,
            default: () => {},
        },
        element: {
            type: Object,
            default: () => {},
        },
    },
    data() {
        return {
            cancelRequest: null,
        }
    },
    created() {
        if (this.request) {
            this.cancelRequest = request(this.request, this.propValue, 'data')
        }
    },
    beforeDestroy() {
        this.request && this.cancelRequest()
    },
}
</script>

<style lang="scss" scoped>
.v-dropdown {
    width: 100%;
    height: 100%;
}
</style>
