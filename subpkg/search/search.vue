<template>
    <view>
        <view class="search-box">
            <!-- 基本用法 -->
            <uni-search-bar @input="input" :radius="100" placeholder="请输入" cancelButton="none" bgColor="grey"
                :focus="true">
            </uni-search-bar>
        </view>


        <!-- 搜索建议列表 -->
        <view class="sugg-list" v-if="searchResults.length !== 0">
            <view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item)">
                <view class="goods-name">
                    {{item.goods_name}}
                </view>
                <uni-icons type="arrow-right" size="16"></uni-icons>
            </view>
        </view>


        <!-- 搜索历史 -->
        <view class="history-box" v-else>
            <!-- 标题区 -->
            <view class="history-title">
                <text>搜索历史</text>
                <uni-icons type="trash-filled" size="24" @click="clean"></uni-icons>
            </view>
            <!-- 列表区 -->
            <view class="history-list">
                <uni-tag :text="item" v-for="(item, i) in histories" :key="i" inverted="true"
                    @click="gotoGoodsList(item)">
                </uni-tag>
            </view>
        </view>



    </view>
</template>

<script>
    export default {
        data() {
            return {
                // 延时器的 timerID
                timer: null,
                // 搜索关键词
                keyword: "",
                // 搜索的结果列表
                searchResults: [],
                // 搜索关键词的历史记录 数组
                historyList: []
            };
        },

        onLoad() {
            uni.getst
            this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
        },

        methods: {
            // input 输入事件的处理函数
            input(e) {
                // console.log(e)
                clearTimeout(this.timer)
                this.timer = setTimeout(() => {
                    console.log(e)
                    this.keyword = e
                    this.getSearchList()
                }, 500)

                // clearTimeout(this.timer)
            },

            async getSearchList() {
                // 判断搜索关键词是否为空
                if (this.keyword.length === 0) {
                    this.searchResults = []
                    return
                }
                const {
                    data: res
                } =
                await uni.$http.get('/api/public/v1/goods/qsearch', {
                    query: this.keyword
                })
                if (res.meta.status !== 200) return uni.$showMsg()
                this.searchResults = res.message

                this.saveSearchHistory()
            },

            gotoDetail(item) {
                // console.log(item.goods_id)
                uni.navigateTo({
                    url: '/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id
                })
            },

            // 保存 搜索历史
            saveSearchHistory() {
                // this.historyList.push(this.keyword)

                const set = new Set(this.historyList)
                set.delete(this.keyword)
                set.add(this.keyword)
                this.historyList = Array.from(set)
                // console.log(set)
                // 对搜索历史数据，进行持久化的存储
                uni.setStorageSync('kw', JSON.stringify(this.historyList))
            },

            clean() {
                this.historyList = []
                uni.setStorageSync('kw', '[]')
            },

            gotoGoodsList(kw) {
                uni.navigateTo({
                    url: '/subpkg/goods_list/goods_list?query=' + kw
                })
            }

        },

        // 计算属性
        computed: {
            histories() {
                return [...this.historyList].reverse()
            }
        }

    }
</script>

<style lang="scss">
    .search-box {
        background-color: #e01010;
        position: sticky;
        top: 0;
        z-index: 999;
    }

    .sugg-list {
        padding: 0 5px;

        .sugg-item {
            display: flex;
            align-items: center;
            justify-content: space-between;
            font-size: 16px;
            padding: 13px 0;
            border-bottom: 1px solid #efefef;

            .goods-name {
                // 文字不允许换行
                white-space: nowrap;
                // 溢出部分隐藏
                overflow: hidden;
                // 文本溢出后，用 ... 代替
                text-overflow: ellipsis;
            }
        }
    }

    .history-box {
        padding: 0 5px;

        .history-title {
            // 左右分散对齐
            display: flex;
            // 两边贴边对齐
            justify-content: space-between;
            height: 40px;
            align-items: center;
            font-size: 18px;
            border-bottom: 1px solid black;
        }

        .history-list {
            display: flex;
            flex-wrap: wrap;
            // margin-top: 5px;


            .uni-tag {
                display: block;
                margin-top: 5px;

                margin-right: 5px;
                background-color: #4335d6;
                border-color: #4335d6;
            }
        }
    }
</style>
