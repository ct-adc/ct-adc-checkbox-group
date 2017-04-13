<template>
    <div class="border-wrapper">
        <div>
            <label class="checkbox-inline">
                <input type="checkbox" value="true" v-model="isCheckedAll" @change="checkAll($event.target.checked)"> 全选
            </label>
        </div>

        <hr class="split-line">

        <div class="matched-list">
            <label class="checkbox-inline" v-for="(item, index) in checkboxList">
                <input type="checkbox" value="item.Name" v-model="item._checked"
                       @change="check(item, $event.target.checked)"> {{ item[showKey] }}
            </label>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    export default {
        props: {
            // 总列表
            list: {
                type: Array,
                default() {
                    return [];
                }
            },

            // 已选择的列表
            matchedList: {
                type: Array,
                default() {
                    return [];
                }
            },

            // 需要显示的字段
            // 目前只支持一个字段显示
            showKey: {
                type: String,
                required: true
            },

            // 匹配键值[必需唯一]
            matchKey: {
                type: String,
                required: true
            }
        },
        data() {
            return {
                checkedLen: 0
            };
        },
        computed: {
            checkedMap() {
                const map = {};

                this.checkedLen = this.matchedList.length;

                JSON.parse(JSON.stringify(this.matchedList)).forEach((item) => {
                    map[item[this.matchKey]] = true;
                });

                return map;
            },
            checkboxList() {
                const list = [];

                JSON.parse(JSON.stringify(this.list)).forEach((item) => {
                    item._checked = this.checkedMap[item[this.matchKey]] || false;

                    list.push(item);
                });

                return list;
            },
            checkboxListLen() {
                return this.checkboxList.length;
            },
            isCheckedAll() {
                return this.checkedLen === this.checkboxListLen;
            }
        },
        methods: {
            check(vItem, bChecked) {
                vItem._checked = bChecked;

                this.checkedLen += vItem._checked ? 1 : -1;

                this.emitChange();
            },
            checkAll(bChecked) {
                this.checkboxList.forEach((item) => {
                    item._checked = bChecked;
                });

                if (bChecked) {
                    this.checkedLen = this.checkboxListLen;
                } else {
                    this.checkedLen = 0;
                }

                this.emitChange();
            },
            emitChange() {
                this.$emit('change', this.getCheckedList());
            },
            getCheckedList() {
                return this.checkboxList.filter((item) => {
                    return item._checked;
                });
            }
        }
    };
</script>

<style rel="stylesheet/scss" lang="scss">
    .border-wrapper {
        padding: 6px 10px;
        height: auto;
        border: 1px solid #ccc;
        border-radius: 3px;
        box-shadow: inset 0 1px 1px rgba(0, 0, 0, .075);
    }

    .split-line {
        border-style: dashed;
    }

    .matched-list {
        margin-left: -10px;
        margin-bottom: -10px;

        .checkbox-inline {
            margin-left: 10px;
            margin-bottom: 10px;
        }
    }
</style>
