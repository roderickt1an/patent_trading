<template>
    <div>
        <van-nav-bar
            title="咨询"
            left-arrow
            @click-left="onClickLeft"/>

             <van-card
                :price="goods.price"
                :title="goods.title"
                style="background-color: #fff;"
                :thumb="goods.thumb">
                <div slot="desc" style="margin-top: 10px">
                    <span style="font-size: 10px; float: left">申请号：{{ goods.patentcode }}</span><br>
                    <span style="font-size: 10px; float: left">行业分类：{{ goods.industry }}</span><br>
                    <span style="font-size: 10px; float: left">专利类型：{{ goods.patenttype }}</span>
                </div>
            </van-card>

            <van-field
                v-model="message"
                type="textarea"
                placeholder="请输入留言"
                rows="1"
                style="bottom:0; right:0; left:0; position: fixed"
                autosize>
                <van-button slot="button" size="small" type="danger" @click="send">留言</van-button>    
            </van-field>

            <van-row style="margin-top: 20px" v-show="isFirst">
                <van-col>
                    <div class="msg" style="padding-top:15px;padding-bottom:15px;border-top-right-radius:20px;border-bottom-right-radius:20px">
                        <span class="msg_font">{{ customerMsg[0] }}</span>
                    </div>
                </van-col>
            </van-row>
            <van-row style="margin-top:20px;" v-show="isFirst">
                <van-col style="float: right;">
                    <div class="msg" style="padding-top:15px;padding-bottom:15px;border-top-left-radius:20px;border-bottom-left-radius:20px">
                        <span class="msg_font" >我们的管家会尽快与您联系！</span>
                    </div>
                </van-col>
            </van-row>
            <van-row style="margin-top: 20px" v-for="(item,index) in customerMsg2" :key=index>
                <van-col>
                    <div class="msg" style="padding-top:15px;padding-bottom:15px;border-top-right-radius:20px;border-bottom-right-radius:20px">
                        <span class="msg_font">{{ item }}</span>
                    </div>
                </van-col>
            </van-row>
    </div>
</template>

<script>
export default {
    data() {
        return{
            isFirst: false,
            customerMsg: [],
            customerMsg2: [],
            message: '',
            goods: {},
            count: 1,
            patdetid:""
        }
    },
    methods: {
        // 返回操作
        onClickLeft() {
            let _self = this
            // this.$router.go(-1)
            _self.$router.push({
                name: 'productDetail',
                params: {
                    patdetid: _self.patdetid
                }
            })
        },

        send() {
            let _self = this

            this.$http({
                url: '/patent/IWoaPatentsController.do?apiSavePatentsLeave',
                method: 'post',
                data: {
                    leavemsg: _self.message,
                    userid: localStorage.getItem('userId')
                },
                transformRequest: [function (data) {
                    let ret = ''
                    for (let it in data) {
                        ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
                    }
                    return ret
                }],
                headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                }
            })
                .then(function (response) {
                    if (response.data.data == '40000') {
                        if (_self.count == 1) {
                            _self.customerMsg.push(_self.message)
                            _self.isFirst = true
                            _self.count++
                        } else {
                            _self.customerMsg2.push(_self.message)
                        }
                        _self.message = ""
                        
                    }
                })
        }
    },
    mounted() {
        let _self = this
        this.goods = this.$route.params.goods
        this.patdetid = this.$route.params.patdetid
        // console.log(this.$route.params)
        // document.addEventListener('popstate',()=>{
        //     _self.onClickLeft()
        // })
    }
}
</script>

<style>
    .cell-group{
        left: 0; 
        right: 0;
        bottom: 0;
        display: flex;
        position: fixed;
    }
    .msg{
        background-color: #fff;
        width: 100%;
    }
    .msg_font{
        font-size: 14px;
        padding: 30px
    }
</style>
