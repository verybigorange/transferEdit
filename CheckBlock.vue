<template>
    <div class="block" v-if="!currentData.parent || currentData.parent.expand">
        <div>
            <span :style="{paddingLeft:20*(currentData.level-1)+'px'}"></span>
            <span class="arrow" v-if="currentData.leaf =='NO'" @click="toggle()">
                <span class="el-icon-caret-bottom" v-if="currentData.expand"></span>
                <span class="el-icon-caret-right" v-else></span>
            </span>
            <!-- 占位用的span -->
            <span v-else style="width:18px;height:12px;display:inline-block"></span>
            <el-checkbox v-model="currentData.checked" @change="handleChange(currentData)">
                <span v-if="!isEdit">{{currentData[showFlied]}}</span>
                <input type="text" v-model="inputVal" v-else class="edit-input" v-focus>
            </el-checkbox>
            <span class="editBtn" v-if="!isEdit" @click="edit(currentData)">编辑</span>
            <span class="saveBtn" v-if="isEdit" @click="save(currentData)">保存</span>
        </div>
    </div>
</template>

<script>

//递归收起展开的项
function handleExpand(item) {
    if (item.children) {
        for (let i of item.children) {
            i.expand = false;
            handleExpand(i)
        }


    }
}
// 处理单选和全选,参数tag决定走不走子的递归
function handleCheck(state, rowData, tag = false) {
    //全选与反选
    if (!tag) {
        if (rowData.children) {
            for (let i of rowData.children) {
                i.checked = state;
                handleCheck.call(this, state, i);
            }
        }
    }
    if (rowData.parent) {
        let parent = rowData.parent;
        let tag = true;
        for (let item of parent.children) {
            if (!item.checked) {
                tag = false
                break;
            }
        }
        if (tag) {
            parent.checked = true
        } else {
            parent.checked = false
        }
        handleCheck.call(this, parent.checked, parent, true);
    }
}

export default {
    //当前的数据，显示的字段名字，子字段的名字
    props: ['currentData', 'showFlied', 'childFlid'],

    data() {
        return {
            isEdit: false,
            inputVal: ""
        }
    },
    methods: {
        //选择
        handleChange(data) {
            handleCheck.call(this, data.checked, data);
        },
        //下拉展开
        toggle() {
            this.currentData.expand = !this.currentData.expand;
            if (!this.currentData.expand) {
                handleExpand(this.currentData)
            }
        },
        //编辑
        edit(data) {
            this.inputVal = data[this.showFlied];
            this.isEdit = true;

        },
        //编辑保存
        save(data) {
            data[this.showFlied];
            data[this.showFlied] = this.inputVal;
            this.isEdit = false;
            this.$emit('watchSave');
        }
    },
    directives: {
        focus(dom) {
            dom.focus();
        }
    }
}

</script>

<style  scoped>
.block {
    margin-top: 5px;
}

.el-checkbox {
    display: inline-block;
}

.el-checkbox+.el-checkbox {
    margin: 0px;
}

.childCheckbox {
    /* padding-left: 46px; */
    display: block;
}

.arrow {
    display: inline-block;
    position: relative;
    border: 1px solid #bfcbd9;
    box-sizing: border-box;
    width: 18px;
    height: 18px;
    color: #fff;
    background-color: #20a0ff;
    border-color: #0190fe;
    line-height: 18px;
    text-align: center;
    font-size: 12px;
    cursor: pointer;
}

.blank {
    display: inline-block;
    width: 18px;
    height: 18px;
}

.el-checkbox__label {
    padding: 0px;
}

.editBtn {
    color: #20a0ff;
    float: right;
    cursor: pointer;
}

.saveBtn {
    color: #ff0000;
    float: right;
    cursor: pointer;
}

.edit-input {
    height: 16px;
    width: 70px;
}
</style>