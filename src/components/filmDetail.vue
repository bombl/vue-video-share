<template xmlns:c="http://www.w3.org/1999/XSL/Transform">
  <el-scrollbar>
    <el-main>
      <el-row style="margin-top: 10px;">
        <el-col :span="24">
            <el-breadcrumb separator-class="el-icon-arrow-right">
              <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
              <el-breadcrumb-item>{{ film.cataLogName }}</el-breadcrumb-item>
              <el-breadcrumb-item>{{film.subClassName}}</el-breadcrumb-item>
              <el-breadcrumb-item>{{film.typeName}}</el-breadcrumb-item>
              <el-breadcrumb-item>{{film.name}}</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="20">
          <el-row style="margin-top: 10px;margin-bottom: 10px;">
            <el-col :span="8" class="pic" v-if="film && film.image">
              <el-image :lazy="true" :src="HOME+film.image" :alt="film.name" fit="contain" style="height: 300px;">
                <div slot="error" class="image-slot">
                  <i class="el-icon-picture-outline"></i>
                </div>
              </el-image>
              <a @click="goAnchor('#kan')">立即播放</a>
            </el-col>
            <el-col :span="16" style="">
              <div class="info" style="padding: 10px;">
                <h1>{{ film.name }}</h1>
                <ul>
                  <li>
                    <span>上映年代：</span>{{ film.onDecade }}&nbsp;&nbsp;
                    <span>状态：</span>{{ film.status }}
                  </li>
                  <li>
                    <span>类型：</span>
                    <a href="#" target="_blank">{{ film.typeName }}</a>
                    <!--<a href="xl/1.html?type=${film.type_id}">${film.typeName}</a>-->
                  </li>
                  <li>
                    <span>主演：</span>
                    <a href="javascript:void(0)">{{film.actor}}</a>
                  </li>
                  <li>
                    <span>地区：</span>{{ film.locName }}
                  </li>
                  <li>
                    <span>更新日期：</span>{{film.updateTime}}&nbsp;&nbsp;
                  </li>
                  <li>
                    <span>剧情：</span>
                    <span v-html="film.plot.substring(0, 200)+'......'"></span>[
                    <a @click="goAnchor('#plotDetail')" class="goAnchor">详细</a>
                    ]
                  </li>
                  <div class="pfen">
                    <p>影片评价</p>
                    <el-rate
                      v-model="userRaty"
                      :colors="colors"
                      @change="saveRaty()"
                      show-text
                      :disabled="disabled_flag"></el-rate>
                    <div class="fen" id="filmStarScoreTip">
                                    <span class="no c1" id="filmStarScore">
                                        {{ film.evaluation | toFixed(2)}}
                                    </span>
                      <span v-if="film.evaluation>=1 && film.evaluation<2">很烂</span>
                      <span v-else-if="film.evaluation>=2 && film.evaluation<4">一般</span>
                      <span v-else-if="film.evaluation>=4&&film.evaluation<6">比较精彩</span>
                      <span v-else-if="film.evaluation>=6&&film.evaluation<8">比较精彩</span>
                      <span v-else-if="film.evaluation>=8&&film.evaluation<=10">棒极了</span>
                      <span v-else-if="film.evaluation>=2 && film.evaluation<4">一般</span>
                      ({{ totalRatys }}评)
                      <div class="bdsharebuttonbox" style="float: right;">
                        <a href="#" class="bds_more" data-cmd="more"></a>
                        <a href="#" class="bds_qzone" data-cmd="qzone"></a>
                        <a href="#" class="bds_tsina" data-cmd="tsina"></a>
                        <a href="#" class="bds_tqq" data-cmd="tqq"></a>
                        <a href="#" class="bds_renren" data-cmd="renren"></a>
                        <a href="#" class="bds_weixin" data-cmd="weixin"></a>
                      </div>
                    </div>
                  </div>
                </ul>
              </div>
            </el-col>
          </el-row>
        </el-col>
        <el-col :span="4">
          <div class="infoad" >
            <div class="skydrive">
              <p><span>友情提示：</span>欢迎大家使用网盘连接下载！</p>
              <ul v-if="resListDupan && resListDupan.length!==0">
                <li v-for="res in resListDupan" class="wbaidu">
                  <a :href="res.link" target="_blank">密码：{{ res.name }}</a>
                </li>
              </ul>
            </div>
          </div>
        </el-col>
      </el-row>
      <el-row style="background: #dcdfe2;height: 3px;"></el-row>
      <el-row>
        <el-col>
          <div class="endpage clearfix">
            <div class="ulike">
              <div class="title"><span>猜你喜欢</span></div>
              <ul v-if="films && films.length!==0" class="img-list imglist clearfix">
                <li v-for="list in films" >
                  <a class="play-img" @click="goFilmDetail(list.id)" :title="list.name"
                     target="_blank">
                    <el-image
                      :src="HOME+list.image"
                      :alt="list.name"
                      :lazy="true"
                      key="fill"></el-image>
                    <i></i>
                    <em v-if="list.resolution==='1080'">1280高清</em>
                    <em v-if="list.resolution!=='1080'">{{list.resolution}}P</em>
                  </a>
                  <h5>
                    <a @click="goFilmDetail(list.id)" :title="list.name">
                      {{list.name}}
                    </a>
                  </h5>
                </li>
              </ul>
            </div>
          </div>
        </el-col>
      </el-row>
      <el-row style="background: #dcdfe2;height: 3px;"></el-row>
      <el-row>
        <el-col>
          <div v-if="resListFlh!==null && resListFlh.length!==0">
            <el-row>
              <el-col :span="12">
                <p><span class="title-icon">在线播放</span></p>
              </el-col>
              <el-col :span="12">
                <p>
                  <span class="title-con">
                      此处支持在线播放，若不能播放请下载后观看或者点击
                      <a href="javascript:void(0)" style="color:red;">失效一键反馈</a>
                  </span>
                </p>
              </el-col>
            </el-row>
            <el-row>
              <el-col>
                <div class="player" v-if="src!=null">
                  <video-player  class="video-player-box vjs-custom-skin content"
                                 ref="videoPlayer"
                                 :options="playerOptions"
                                 :playsinline="true"
                  >
                  </video-player>
                </div>
                <div class="mad-box-form" v-if="resListFlh!==null && resListFlh.length!==0">
                  <a class="mad-a" href="javascript:void(0)" v-for="li in resListFlh" @click="palyVideo(li.link)">{{li.episodes}}集</a>
                </div>
                <div class="mad-box-form" v-if="resListFlh===null || resListFlh.length===0">
                  暂无资源
                </div>
              </el-col>
            </el-row>
          </div>
        </el-col>
        <a id="kan"></a>
      </el-row>
      <el-row>
        <el-col>
          <div v-if="checkListAll()">
            <div class="download_list_div" v-if="resListEd2k!==null && resListEd2k.length!==0">
              <el-row>
                <el-col :span="12">
                  <span class="title-icon">下载地址</span>
                </el-col>
                <el-col :span="12">
                  <span class="title-con">
                    此处支持迅雷、QQ旋风、小米路由下载，同时支持迅雷影音播放，若不能下载请点击
                    <a href="javascript:void(0);" @click="fk('x2')" style="color:red;">
                      失效一键反馈
                    </a>
                  </span>
                </el-col>
              </el-row>
              <el-row>
                  <el-col>
                    <div class="ndownlist">
                      <ul v-if="resListEd2k!==null && resListEd2k.length!==0">
                        <li v-for="relist in resListEd2k">
                          <el-row>
                            <el-col :span="12">
                              <!--<i><input type="checkbox" :value="relist.id" name="CopyAddr1"/></i>-->
                              <p style="width:300px;height: auto;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;">
                                {{ getSubstr(relist.link) }}
                              </p>
                            </el-col>
                            <el-col :span="12" style="padding-right: 2%;">
                              <a :href="relist.link" style="float: right" class="btn">下载</a>
                            </el-col>
                          </el-row>
                        </li>
                      </ul>
                    </div>
                  </el-col>
                </el-row>

              <div class="download_list_div" v-if="checkList()">
                <el-row>
                  <el-col :span="12">
                    <span class="title-icon">下载地址</span>
                  </el-col>
                  <el-col :span="12">
                    <span class="title-con">
                      此处支持本地下载、迅雷下载，若不能下载请点击
                      <a href="javascript:void(0);" @click="fk('x2')" style="color:red;">
                        失效一键反馈
                      </a>
                  </span>
                  </el-col>
                </el-row>

                <el-row>
                  <el-col>
                    <div class="ndownlist">
                      <ul class="thunder">
                        <li v-for="relist in resListHttp">
                          <el-row>
                            <el-col :span="12">
                              <!--<i><input type="checkbox" :value="relist.id" name="CopyAddr1"/></i>-->
                              <p style="width:300px;height: auto;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;">
                                {{relist.name}}
                              </p>
                            </el-col>
                            <el-col :span="12" style="padding-right: 2%;">
                              <a :href="relist.link" style="float: right" class="btn">下载</a>
                            </el-col>
                          </el-row>
                        </li>

                        <li v-for="relist in resListThunder">
                          <el-row>
                            <el-col :span="12">
                              <!--<i><input type="checkbox" :value="relist.id" name="CopyAddr1"/></i>-->
                              <p style="width:300px;height: auto;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;">
                                {{ get_movie_name(relist.name)}}
                              </p>
                            </el-col>
                            <el-col :span="12" style="padding-right: 2%;">
                              <a :href="relist.link" style="float: right" class="btn">下载</a>
                            </el-col>
                          </el-row>
                        </li>

                        <li v-for="relist in resListOther">
                          <el-row>
                            <el-col :span="12">
                              <!--<i><input type="checkbox" :value="relist.id" name="CopyAddr1"/></i>-->
                              <p style="width:300px;height: auto;overflow: hidden;white-space: nowrap;text-overflow: ellipsis;">{{relist.name}}</p>
                            </el-col>
                            <el-col :span="12" style="padding-right: 2%;">
                              <a :href="relist.link" style="float: right" class="btn">下载</a>
                            </el-col>
                          </el-row>
                        </li>
                      </ul>
                    </div>
                  </el-col>
                </el-row>
              </div>
            </div>
          </div>
        </el-col>
      </el-row>
      <el-row style="margin-top: 10px;" id="plotDetail">
        <el-col>
          <div class="endtext">
            <div class="title"><span>剧情介绍</span></div>
            <div v-html="film.plot"></div>
          </div>
        </el-col>
      </el-row>
    </el-main>
  </el-scrollbar>
</template>

<script>
  import { getFilmDetail, errorDeal, dealResult, saveRaty } from '../api/api';
  import {goPage, ThunderEncode} from "../util/index";
  import {toFixed} from "../filter/numberFilter";
  export default {
      name: "film-detail",
      data() {
        return {
          film: {
            id: this.$route.params.filmId,
            name: '',
            image: '',
            onDecade: '',
            status: '',
            typeName: '',
            type_id: '',
            actor: '',
            locName: '',
            loc_id: '',
            updateTime: '',
            isUse: '',
            cataLog_id: '',
            cataLogName: '',
            subClass_id: '',
            subClassName: '',
            isVip: '',
            plot: '',
            evaluation: 0.0,
            resList: [],
            view_number: '',
          },
          userRaty: null,
          colors: ['#99A9BF', '#F7BA2A', '#FF9900'],
          totalRatys: 0,
          resListDupan: [],
          disabled_flag: false,
          films:[],
          resListFlh:[],
          src: null,
          resListHttp:[],
          resListOther:[],
          resListThunder:[],
          resListEd2k:[],
          playerOptions: { // 播放器设置
            playbackRates: [ 0.5, 0.75, 1.0, 1.5, 2.0, 3.0], //播放速度
            autoplay: false, //如果true,浏览器准备好时开始回放。
            muted: false, // 默认情况下将会消除任何音频。
            loop: false, // 导致视频一结束就重新开始。
            preload: 'auto', // 建议浏览器在<video>加载元素后是否应该开始下载视频数据。auto浏览器选择最佳行为,立即开始加载视频（如果浏览器支持）
            language: 'zh-CN',
            aspectRatio: '16:9', // 将播放器置于流畅模式，并在计算播放器的动态大小时使用该值。值应该代表一个比例 - 用冒号分隔的两个数字（例如"16:9"或"4:3"）
            fluid: true, // 当true时，Video.js player将拥有流体大小。换句话说，它将按比例缩放以适应其容器。
            sources: [{
              // type: "video/mp4",
              type: "video/ogg",
              src: this.src //url地址
            }],
            width: document.documentElement.clientWidth,
            notSupportedMessage: '此视频暂无法播放，请稍后再试', //允许覆盖Video.js无法播放媒体源时显示的默认信息。
            controlBar: {
              timeDivider: true,
              durationDisplay: true,
              remainingTimeDisplay: false,
              fullscreenToggle: true  //全屏按钮
            }
          }
        }
      },
      mounted() {
        if( this.checkFilmId() ) {
          this.init();
        }
      },
      methods: {
        checkFilmId() {// 检查初始化参数
          if( null==this.film.id || ''===this.film.id.trim() ) {
            this.$message.error({
              message: '系统错误,请重试!',
              duration: 2000,
            });
            this.$router.go(-1);
          } else {
            return true;
          }
        },
        init() { // 初始化
          this.$store.state.fullscreenLoading = true;
          getFilmDetail({'filmId':this.film.id}).then( res => {
            const data = dealResult(res.data);
            if( null!=data ) {
              this.film = data.film;
              this.totalRatys = data.totalRatys;
              this.resListDupan = data.resListDupan;
              this.films = data.films;
              this.resListFlh=data.resListFlh;
              this.src = data.src;
              this.resListHttp = data.resListHttp;
              this.resListOther = data.resListOther;
              this.resListThunder = data.resListThunder;
              this.resListEd2k = data.resListEd2k;
              this.$store.state.fullscreenLoading = false;
            }
          }).catch(function (err) {
            errorDeal(err);
          })
        },
        saveRaty() {// 保存评分
          const params = {"film_id": this.film.id, "score": this.userRaty*2};
          saveRaty(params).then( res => {
            const data = dealResult(res.data);
            if( null!=data ) {
              this.film.evaluation = data;
              this.totalRatys ++;
              this.$message.success({
                message: '提交成功',
                duration: 2000,
              });
              this.disabled_flag = true;
            }
          }).catch( function (err) {
            console.log(err);
          })
        },
        goFilmDetail(filmId) {
          goPage('/detail/'+filmId);
        },
        checkListAll() {
          return ( this.resListHttp!==null && this.resListHttp.length!==0) || (this.resListOther!==null && this.resListOther.length!==0) || (this.resListThunder!==null && this.resListThunder.length!==0) || (this.resListEd2k!==null && this.resListEd2k.length!==0);
        },
        checkList() {
          return ( this.resListHttp!==null && this.resListHttp.length!==0) || (this.resListOther!==null && this.resListOther.length!==0) || (this.resListThunder!==null && this.resListThunder.length!==0);
        },
        fk(type) {// TODO 反馈接口，页面迁移完成之后会调用后端接口
          this.$message.success('反馈成功...');
        },
        goAnchor(selector) {
          this.$el.querySelector(selector).scrollIntoView()
        },
        getSubstr(downurl) {
          let resultStr = downurl;
          if (downurl.indexOf("ed2k://|file|") === 0) {
            const tmpStr = resultStr.split('|');
            if (tmpStr.length > 3) {
              if (tmpStr[2].length > 0) {
                resultStr = decodeURIComponent(tmpStr[2]);
              }
            }
            return resultStr;
          } else {
            return resultStr;
          }
        },
        Flh() {
          // TODO 方法待完成
        },
        palyVideo(url) {
          this.playerOptions.sources[0].src = url;
          this.src = url;
        },
        get_movie_name(filename, arsg) {// 获取视频名称
          let tname = filename.split(arsg);
          if(tname.length>2) {
            return this.get_movie_name(tname[(tname.length-1)], ']');
          }  else {
            return tname[(tname.length-1)];
          }
        }
      },
      watch :{
        '$route': function (to) {
          this.film.id = to.params.filmId;
          if( this.checkFilmId() ) {
            this.init();
          } else {
            this.$message.error({
              message: '系统错误,请重试!',
              duration: 2000,
            });
          }
        }
      }
    }
</script>

<style lang="css">
@import "../assets/css/index/detail.css";
</style>
