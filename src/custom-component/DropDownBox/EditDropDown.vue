<template>
    <div class="edit-dropdown">
        <div @dblclick="onDblclick">
            <div
                v-for="(item, row) in tableData"
                :key="row"
                :class="{ selected: curTd === row }"
                class="input"
                @click="onClick(row)"
            >
                <el-input
                    v-if="canEdit && curTd === row "
                    v-model="tableData[row]"
                    v-focus
                    @blur="onBlur"
                ></el-input>
                <div v-else>{{ item }}</div>
            </div>
        </div>
        <div>
            <button @click="addRow">添加新行</button>
            <button @click="deleteRow">删除行</button>
        </div>
    </div>
</template>

<script>
export default {
    directives: {
        focus: {
            inserted(el) {
                el.querySelector('input').focus()
            },
        },
    },
    data() {
        return {
            curProperty: '',
            curTd: '',
            canEdit: false,
            preCurTd: '',
        }
    },
    computed: {
        tableData() {
            return this.$store.state.curComponent.propValue.data
        },
    },
    methods: {
        onDblclick() {
            this.canEdit = true
        },

        onClick(row) {
            this.curTd = row
            this.preCurTd = this.curTd
        },

        onBlur() {
            this.canEdit = false
            this.curTd = ''
        },

        deleteRow() {
            if (this.preCurTd === 0) {
                this.$message.error('不能删除初始选项')
                return
            }
            if (this.preCurTd === '') {
                this.$message.error('请先选择要删除的选项')
                return
            }
            
            this.tableData.splice(this.preCurTd, 1)
        },

        addRow() {
            this.tableData.push('选项')
        },
    },
}
</script>

<style lang="scss" scoped>
.edit-dropdown {
    overflow: auto;
    margin-bottom: 8px;

    & > div {
        margin-top: 18px;
        display: flex;
        flex-flow: column wrap;
        align-content: center;

        button {
            cursor: pointer;
            background: #fff;
            border: 1px solid #dcdfe6;
            color: #606266;
            text-align: center;
            box-sizing: border-box;
            outline: 0;
            margin: 0;
            font-weight: 500;
            padding: 4px 10px;
            font-size: 14px;
            border-radius: 4px;
            margin-bottom: 10px;

            &:hover {
                background: #ecf5ff;
                color: #409eff;
            }
        }
    }

    table {
        border-collapse: collapse;
        word-break: break-all;
        word-wrap: break-word;
        text-align: center;
        font-size: 12px;

        td {
            border: 1px solid #ebeef5;
            height: 40px;
            min-width: 60px;
            max-width: 80px;
            padding: 10px;
        }
    }

    .selected {
        background: #ecf5ff;
        color: #409eff;
    }

    .input {
        margin-top: 20px;
        font-size: 13px;
    }
}

</style>
