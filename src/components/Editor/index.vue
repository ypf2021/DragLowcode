<template>
    <div
        id="editor"
        class="editor"
        :class="{ edit: isEdit }"
        :style="{
            ...getCanvasStyle(canvasStyleData),
            width: changeStyleWithScale(canvasStyleData.width) + 'px',
            height: changeStyleWithScale(canvasStyleData.height) + 'px',
        }"
        @contextmenu="handleContextMenu"
    >
        <!-- 网格线 -->
        <Grid />

        <!--页面组件列表展示-->
        <Shape
            v-for="(item, index) in componentData"
            :key="item.id"
            :default-style="item.style"
            :style="getShapeStyle(item.style)"
            :active="item.id === (curComponent || {}).id"
            :element="item"
            :index="index"
        >
            <component
                :is="item.component"
                v-if="item.component.startsWith('SVG')"
                :id="'component' + item.id"
                :style="getSVGStyle(item.style)"
                class="component"
                :prop-value="item.propValue"
                :element="item"
                :request="item.request"
            />

            <component
                :is="item.component"
                v-else-if="item.component != 'VText'"
                :id="'component' + item.id"
                class="component"
                :style="getComponentStyle(item.style)"
                :prop-value="item.propValue"
                :element="item"
                :request="item.request"
            />

            <component
                :is="item.component"
                v-else
                :id="'component' + item.id"
                class="component"
                :style="getComponentStyle(item.style)"
                :prop-value="item.propValue"
                :element="item"
                :request="item.request"
                @input="handleInput"
            />
        </Shape>
        <!-- 右击菜单 -->
        <ContextMenu />
    </div>
</template>

<script>
import { mapState } from 'vuex'
import Shape from './Shape'
import { getStyle, getShapeStyle, getSVGStyle, getCanvasStyle } from '@/utils/style'
import ContextMenu from './ContextMenu'
import Grid from './Grid'
import { $ } from '@/utils/utils'
import { changeStyleWithScale } from '@/utils/translate'

export default {
    components: { Shape, ContextMenu, Grid },
    props: {
        isEdit: {
            type: Boolean,
            default: true,
        },
    },
    data() {
        return {
            editorX: 0,
            editorY: 0,
            start: { // 选中区域的起点
                x: 0,
                y: 0,
            },
            width: 0,
            height: 0,
            isShowArea: false,
            svgFilterAttrs: ['width', 'height', 'top', 'left', 'rotate'],
        }
    },
    computed: mapState([
        'componentData',
        'curComponent',
        'canvasStyleData',
        'editor',
    ]),
    mounted() {
        // 获取编辑器元素
        this.$store.commit('getEditor')
    },
    methods: {
        getShapeStyle,
        getCanvasStyle,
        changeStyleWithScale,

        handleContextMenu(e) {
            e.stopPropagation()
            e.preventDefault()

            // 计算菜单相对于编辑器的位移
            let target = e.target
            let top = e.offsetY
            let left = e.offsetX
            while (target instanceof SVGElement) {
                target = target.parentNode
            }

            while (!target.className.includes('editor')) {
                left += target.offsetLeft
                top += target.offsetTop
                target = target.parentNode
            }

            this.$store.commit('showContextMenu', { top, left })
        },

        getComponentStyle(style) {
            return getStyle(style, this.svgFilterAttrs)
        },

        getEditor(state) {
            state.editor = $('#editor')
        },

        getSVGStyle(style) {
            return getSVGStyle(style, this.svgFilterAttrs)
        },

        handleInput(element, value) {
            // 根据文本组件高度调整 shape 高度
            this.$store.commit('setShapeStyle', { height: this.getTextareaHeight(element, value) })
        },

        getTextareaHeight(element, text) {
            let { lineHeight, fontSize, height } = element.style
            if (lineHeight === '') {
                lineHeight = 1.5
            }

            const newHeight = (text.split('<br>').length - 1) * lineHeight * (fontSize || this.canvasStyleData.fontSize)
            return height > newHeight ? height : newHeight
        },
    },
}
</script>

<style lang="scss" scoped>
.editor {
    position: relative;
    background: #fff;
    margin: auto;

    .lock {
        opacity: .5;

        &:hover {
            cursor: not-allowed;
        }
    }
}

.edit {
    .component {
        outline: none;
        width: 100%;
        height: 100%;
    }
}
</style>
