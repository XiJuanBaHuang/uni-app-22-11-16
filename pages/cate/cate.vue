<template>
    <view>
        <!-- 使用 自定义的搜索组件 -->
        <!-- <my-search :bgcolor="'pink'" :radius="3"></my-search> -->
        <my-search @click="gotoSearch"></my-search>

        <view class="scroll-view-container">
            <!-- 左侧的滑动区  :style="{height: wh + 'px'}"  -->
            <scroll-view class="left-scroll-view" scroll-y="true" :style="{'height': wh + 'px'}">
                <block v-for="(item, i) in cateList" :key="i">
                    <view :class="['left-scroll-view-item', i === active ? 'active' : '']" @click="activeChange(i)">
                        {{item.cat_name}}
                    </view>
                </block>
            </scroll-view>
            <!-- 右侧的滑动区 -->
            <scroll-view scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop">
                <view class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
                    <!-- 二级分类的标题 -->
                    <view class="cate-lv2-title">/ {{item2.cat_name}} /</view>
                    <!-- 当前二级分类下的三级分类列表 -->
                    <view class="cate-lv3-list">
                        <!-- 三级分类的 item 项 -->
                        <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3"
                            @click="gotoGoodList(item3)">
                            <!-- 三级分类的 图片 -->
                            <!-- .replace('dev', 'web') -->
                            <image :src="item3.cat_icon.replace('dev', 'web')"></image>
                            <!-- 三级分类的 文本 -->
                            <text>{{item3.cat_name}}</text>
                        </view>

                    </view>

                </view>

            </scroll-view>
        </view>
    </view>
</template>

<script>
    export default {
        data() {
            return {
                // 当前设备可用的高度 WindowHeight
                wh: 0,
                cateList: [],
                active: 0,
                // 二级分类的列表
                cateLevel2: [],
                // 滚动条距离顶部的距离
                scrollTop: 0
            };
        },

        onLoad() {
            const sysInfo = uni.getSystemInfoSync()
            // console.log((sysInfo))
            this.wh = sysInfo.windowHeight - 50

            this.getCateList()
        },

        methods: {
            async getCateList() {
                const {
                    data: res
                } = await uni.$http.get('/api/public/v1/categories')
                if (res.meta.status !== 200) return uni.$showMsg()
                this.cateList = res.message

                // 接下来为二级分类赋值 初次
                this.cateLevel2 = res.message[0].children
            },
            activeChange(i) {
                this.active = i
                // 这是重新为 二级分类 赋值
                this.cateLevel2 = this.cateList[i].children
                this.scrollTop = this.scrollTop === 0 ? 1 : 0
            },
            gotoGoodList(item3) {
                uni.navigateTo({
                    url: '/subpkg/goods_list/goods_list?cid=' + item3.cat_id
                })
            },
            gotoSearch() {
                // console.log('ok')
                uni.navigateTo({
                    url: '/subpkg/search/search'
                })
            }
        }
    }
</script>

<style lang="scss">
    .scroll-view-container {
        // 左右布局   父容器
        display: flex;

        .left-scroll-view {
            width: 120px;

            .left-scroll-view-item {
                background-color: #F7F7F7;
                line-height: 60px;
                text-align: center;
                font-size: 12px;

                // 激活项
                &.active {
                    background-color: #FFFFFF;
                    position: relative;

                    // 伪元素
                    &::before {
                        content: ' ';
                        display: block;
                        width: 3px;
                        height: 30px;
                        background-color: red;
                        position: absolute;
                        top: 50%;
                        left: 0;
                        transform: translateY(-50%);
                    }
                }
            }
        }
    }

    .cate-lv2-title {
        font-size: 12px;
        font-weight: bold;
        text-align: center;
        padding: 15px 0;
    }

    .cate-lv3-list {
        display: flex;
        flex-wrap: wrap; // 如果一行放不下 则自动换行

        .cate-lv3-item {
            width: 33.33%;
            display: flex; // 分散对齐
            flex-flow: column; // 
            justify-content: center; // 纵向居中
            align-items: center; // 横向居中
            margin-bottom: 10px;

            image {
                width: 60px;
                height: 60px;
            }

            text {
                font-size: 12px;
            }
        }
    }
</style>
