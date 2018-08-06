<template>
    <div class="cuspage" @mousemove="testmove($event)" @mouseup="testup($event)">
        <div class="topsqu">页面拖拽demo</div>
        <div class="consqu">
            <div class="moudlesqu">
                <div class="name">可添加插件</div>
                <div class="con">
                    <ul>
                        <li v-for="item in moudles" @mousedown="testdown($event,item.id,'moudle')">{{item.name}}</li>
                    </ul>
                </div>
                <div class="fixmove" v-show="moudles_state.move"
                     :style="'left:'+moudles_state.x+'px;top:'+moudles_state.y+'px;'"
                     v-html="moudles_state.ming.name"></div>
            </div>
            <div class="dissqu">
                <div class="name">页面编辑区域</div>
                <div class="con" id="dissqu">
                    <div class="layer" v-for="item in moudles_edit" :id="item.lsid" :class="{'moving':item.moving}"
                         @mousedown="testdown($event,item.id,'edit')">
                        <div class="m">
                            <div class="squ" v-html="item.default_dom"></div>
                            <div class="border"></div>
                        </div>
                    </div>
                    <div class="fixmove_e" v-show="moudles_state.move_e"
                         :style="'left:'+moudles_state.x+'px;top:'+moudles_state.y+'px;width:'+moudles_state.move_e_style.width+'px;height:'+moudles_state.move_e_style.height+'px;'"
                         v-html="moudles_state.move_e_style.edithtml"></div>
                </div>
            </div>
            <div class="funsqu">
                <div class="name">配置</div>
                <div class="con">

                </div>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'cuspage',
        data() {
            return {
                basecoor: {//- 编辑区域的值
                    disx: 0,
                    disy: 0,
                    disx_e: 0,
                    diry: 0,
                    movedir: 0
                },
                moudles: [//- 组件值
                    {
                        id: 'test0',
                        name: '测试组件0',
                        default_dom: '<div class="adddom">我是要添加的数据0</div>',

                    },
                    {
                        id: 'test1',
                        name: '测试组件1',
                        default_dom: '<div class="adddom adddom1">我是要添加的数据1</div>',

                    },
                    {
                        id: 'test2',
                        name: '测试组件2',
                        default_dom: '<div class="adddom adddom2">我是要添加的数据2</div>',

                    }
                ],
                moudles_state: {//- 拖动状态值
                    ming: {},
                    move: false,
                    move_e: false,
                    move_e_style: {
                        width: 0,
                        height: 0,
                        edithtml: ''
                    },
                    x: 0,
                    y: 0,
                    madd: false,
                    setTime: true,
                },
                moudles_default_dom: '<div class="default_place"></div>',
                moudles_edit: []
            }
        },
        methods: {//- 方法
            init() {
                let that = this;

            },
            testdown(e, id, sty) {//- 鼠标按下拖拽模块上

                let that = this;
                let cx = e.clientX;
                let cy = e.clientY;

                that.moudles_state.x = cx;
                that.moudles_state.y = cy;

                if (sty == 'moudle') {//- 左侧模块

                    that.moudles_state.move = true;
                    that.moudles.forEach(function (value, index) {
                        let v = value;
                        v.lsid = v.id + new Date().getTime();
                        if (value.id == id) that.moudles_state.ming = v;
                    })

                } else if (sty == 'edit') {//- 中部编辑区模块

                    that.moudles_state.move_e = true;

                    that.moudles_state.move_e_style.width = e.target.offsetWidth;
                    that.moudles_state.move_e_style.height = e.target.offsetHeight;

                    that.moudles_edit.forEach(function (value, index) {
                        let v = value;
                        if (value.id == id) {
                            v.moving=true;
                            that.moudles_state.ming = v;
                            that.moudles_state.move_e_style.edithtml = e.target.parentNode.parentNode.outerHTML;
                        }
                    })

                }

                that.resetdiscoor();//- 初始化展示区的x y

            },
            testmove(e) {//- 全局move
                let that = this;

                if (that.moudles_state.move || that.moudles_state.move_e) {//- 存在模块拖动

                    let cx = e.clientX;
                    let cy = e.clientY;

                    that.moudles_state.x = cx;
                    that.moudles_state.y = cy;

                    if (that.moudles_state.setTime) {//- 每60ms改变一次

                        that.moudles_state.setTime = false;
                        setTimeout(function () {
                            that.moudles_state.setTime = true;
                        }, 60)

                        that.basecoor.movedir = cy - that.basecoor.diry;//- 判断方向值
                        that.basecoor.diry = cy;

                        if (cx != 0 && cy != 0 && Math.abs(that.basecoor.movedir) > 0) {

                            let bx = that.basecoor.disx;
                            let by = that.basecoor.disy;
                            let bx_e = that.basecoor.disx_e;

                            let ming = that.moudles_state.ming;

                            if (cx > bx && cx < bx_e && cy > by) {//- 在编辑区内

                                let melist = that.moudles_edit;
                                let nobj = {};
                                if (that.moudles_state.move) {
                                    nobj = {
                                        id: ming.id,
                                        lsid: ming.lsid,
                                        name: ming.name,
                                        moving:false,
                                        default_dom: that.moudles_default_dom
                                    }
                                } else if (that.moudles_state.move_e) {
                                    nobj = ming;
                                }

                                if (!that.moudles_state.madd && that.moudles_state.move) {

                                    if (melist.length < 1) {

                                        that.moudles_edit.push(nobj)
                                        that.moudles_state.madd = true;

                                    } else {

                                        let con = true;
                                        for (let i = 0; i < melist.length; i++) {
                                            let mi = melist[i];
                                            if (con) {
                                                if (cy <= mi.middlevalue) {
                                                    that.moudles_edit.splice(i, 0, nobj);
                                                    that.moudles_state.madd = true;
                                                    con = false;
                                                }
                                                if (i == melist.length - 1) {
                                                    that.moudles_edit.push(nobj);
                                                    that.moudles_state.madd = true;
                                                    con = false;
                                                }

                                            } else {
                                                break;
                                            }
                                        }

                                    }

                                    that.$nextTick(function () {
                                        that.resetval_mw();
                                    })

                                } else {

                                    let dir = that.basecoor.movedir;

                                    let con = true;

                                    let melistlength = melist.length;

                                    for (let i = 0; i < melist.length; i++) {
                                        let mi = melist[i];
                                        if (con) {
                                            if (mi.lsid == ming.lsid) {

                                                let j = 0;
                                                let judge = false;

                                                if (dir > 0) {

                                                    j = i + 1;
                                                    judge = j <= melistlength - 1 && cy >= melist[j].middlevalue;

                                                } else if (dir < 0) {

                                                    j = i - 1;
                                                    judge = j != -1 && cy <= melist[j].middlevalue;

                                                }

                                                if (judge) {

                                                    melist.splice(i, 1);
                                                    melist.splice(j, 0, nobj);

                                                    that.$nextTick(function () {
                                                        that.resetval_mw();
                                                    })

                                                    con = false;
                                                }

                                            }
                                        } else {
                                            break;
                                        }
                                    }

                                }

                            } else {

                                if (that.moudles_state.move) {
                                    that.moudles_state.madd = false;
                                    let list = that.moudles_edit;
                                    for (let i = 0; i < list.length; i++) {
                                        if (list[i].lsid == ming.lsid) {
                                            list.splice(i, 1);
                                            break;
                                        }
                                    }
                                    that.$nextTick(function () {
                                        that.resetval_mw();
                                    })
                                }

                            }
                        }
                    }
                }

            },
            testup(e) {//- 全局move up
                let that = this;
                if (that.moudles_state.move || that.moudles_state.move_e) {

                    let list = that.moudles_edit;
                    let ming = that.moudles_state.ming;
                    for (let i = 0; i < list.length; i++) {
                        let li=list[i];
                        if (li.id == ming.id) {
                            if(that.moudles_state.move){
                                li.default_dom = ming.default_dom;
                            }else if(that.moudles_state.move_e){
                                li.moving=false;
                            }
                        }
                    }

                    that.moudles_state.ming = {};
                    that.moudles_state.madd = false;
                    that.moudles_state.move = false;
                    that.moudles_state.move_e = false;

                    that.$nextTick(function () {
                        that.resetval_mw();
                    })

                }

            },
            resetdiscoor() {
                let that = this;
                let d = document.getElementById("dissqu");
                that.basecoor.disx = d.offsetLeft;
                that.basecoor.disy = d.offsetTop;
                that.basecoor.disx_e = d.offsetLeft + d.offsetWidth;
            },
            resetval_mw() {//- 初始化编辑区模块的中间值
                let that = this;
                that.moudles_edit.forEach(function (v) {
                    let ls_id = v.lsid;
                    let d = document.getElementById(ls_id);

                    let cv = d.offsetTop + d.offsetHeight / 2;
                    v.middlevalue = cv;
                })

            }
        },
        watch: {//- 监听

        },
        created() {//- 创建 常用
            this.init();
        },
        updated() {//- 组件更新之后

        },
        destroyed() {//- 销毁

        },
        beforeRouteEnter(to, from, next) {//- 进入路由，无this

            next(true);
        },
        beforeRouteUpdate(to, from, next) {//- 路由改变，可this

            next(true);
        },
        beforeRouteLeave(to, from, next) {//- 离开路由，可this

            next(true);
        }
    }
</script>

<style lang="sass">
    @import "../../css/cuspage/cuspage.scss"
</style>
