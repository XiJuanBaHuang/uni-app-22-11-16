<template>
    <view class="my-search-container" :style="{'background-color': bgcolor}" @click="searchBoxHandler">
        <!-- 这是自定义的搜索组件 -->
        <!-- 模拟搜索框 -->
        <view class="my-search-box" :style="{'border-radius': radius + 'px'}">
            <!-- 使用 uni-ui 提供的图表组件 -->
            <uni-icons type="search" size="17"></uni-icons>
            <text class="placeholder">搜索</text>

        </view>
    </view>
</template>

<script>
    export default {
        name: "my-search",
        data() {
            return {

            };
        },

        props: {
            // 背景颜色
            bgcolor: {
                type: String,
                default: '#C00000'
            },
            // 圆角尺寸
            radius: {
                type: Number,
                default: 18 // px
            }
        },

        methods: {
            searchBoxHandler() {
                // console.log('~~~~~~~~~~~')
                this.$emit('click')
            }
        }
    }
</script>

<style lang="scss">
    .my-search-container {
        height: 50px;
        // props 
        // background-color: #c00000;
        display: flex;
        align-items: center;
        padding: 0 10px;

        // 
        .my-search-box {
            height: 36px;
            width: 100%;
            background-color: #ffffff;
            // border-radius: 18px;
            display: flex;
            justify-content: center;
            align-items: center;

            .placeholder {
                font-size: 15px;
                margin-left: 5px;
            }
        }
    }
</style>
