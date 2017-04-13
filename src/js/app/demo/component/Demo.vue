<template>
    <div id="demo">
        <div class="container mt20">
            <checkbox-group :list="config.list"
                            :matchedList="config.matchedList"
                            :showKey="config.showKey"
                            :matchKey="config.matchKey"
                            @change="updateMatchedList"></checkbox-group>

            <hr>

            <pre>{{ JSON.stringify(config.matchedList, null, '\t') }}</pre>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    import CheckboxGroup from '../../../../component/ct-adc-checkbox-group.vue';

    export default {
        components: {
            CheckboxGroup
        },
        data() {
            return {
                config: {
                    showKey: 'name',
                    matchKey: 'id',
                    list: [
                        {
                            id: 1,
                            name: '小鹿男'
                        }, {
                            id: 2,
                            name: '两面佛'
                        }, {
                            id: 3,
                            name: '酒吞童子'
                        }, {
                            id: 4,
                            name: '茨木童子'
                        }, {
                            id: 5,
                            name: '青行灯'
                        }, {
                            id: 6,
                            name: '大天狗'
                        }, {
                            id: 7,
                            name: '荒川之主'
                        }, {
                            id: 8,
                            name: '妖刀姬'
                        }, {
                            id: 9,
                            name: '阎魔'
                        }
                    ],
                    matchedList: [
                        {
                            id: 8,
                            name: '妖刀姬'
                        }
                    ]
                }
            };
        },
        methods: {
            updateMatchedList(aList) {
                this.config.matchedList = aList;
            }
        }
    };
</script>

<style rel="stylesheet/scss" lang="scss">
    @import "../../../../scss/mixins";

    html {
        margin-left: calc(100vw - 100%);
        @include customScrollBar();
    }

    hr {
        border-style: dashed;
    }
</style>
