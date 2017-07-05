<template>
    <div>
        <div>
            <label class="checkbox-inline no-user-select" :class="{disabled: disabled || checkboxList.length === 0}">
                <input type="checkbox" value="true"
                       :disabled="disabled || checkboxList.length === 0"
                       v-model="isCheckedAll"
                       @change="checkAll($event.target.checked)"> 全选
            </label>
        </div>

        <hr class="split-line">

        <div class="matched-list">
            <span class="ml-10 text-muted" v-if="checkboxList.length === 0">暂无数据</span>
            <label class="checkbox-inline"
                   :class="{disabled: disabled}"
                   v-for="(item, index) in checkboxList">
                <input type="checkbox" value="item.Name"
                       :disabled="disabled"
                       v-model="item._checked"
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
            },

            // 是否禁用所有的 checkbox
            disabled: {
                type: Boolean,
                default: false
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

                this.$nextTick(() => {
                    this.checkedLen = this.matchedList.length;
                });

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
                // Q: 为什么是大于等于 ?
                // A: 因为我不能保证外部 list 先更新，还是 matchedList 先更新
                return this.checkedLen >= this.checkboxListLen;
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
        },
        watch: {
            // 每次列表改变，应该重置计数
            'list'() {
                this.checkedLen = this.matchedList.length;
            }
        }
    };
</script>

<style>
    .split-line {
        border-style: dashed;
    }

    .ml-10 {
        margin-left: 10px;
    }

    .no-user-select {
        user-select: none;
    }

    .matched-list {
        margin-left: -10px;
        margin-bottom: -10px;
    }

    .matched-list .checkbox-inline {
        margin-left: 10px;
        margin-bottom: 10px;
    }
</style>
