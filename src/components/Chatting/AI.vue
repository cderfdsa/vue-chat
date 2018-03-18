<template lang="html">

  <transition name="slide-right">
    <div class="chatting">
      <!-- 聊天界面头部 -->
      <div class="chatting-header">
          <div class="chatting-back">
            <i  :class="[isRedAI ? 'icon-back' : 'icon-back2']"></i>
          </div>
          <div class="chatting-title">
            <h2>AI 智能小白</h2>
          </div>
          <div class="chatting-menu">
            <!--<i @click="$router.push('/login')" class="icon-menu"></i>-->
            <!--<i  class="icon-menu"></i>-->
          </div>
      </div>

      <!-- 聊天内容区域 -->
      <div ref="chattingContent" id="chattingContent" class="chatting-content">
        <div v-for="item of msgs">
          <div v-if="item.self" class="chatting-item self clearfix">
            <div class="msg-date">
              {{ item.date }}
            </div>
            <div class="msg-from">
              <!--<span class="loc">[{{item.loc}}]</span>-->
              <!--<span class="msg-author">{{ item.from}}</span>-->
              <img src="./gen.svg" alt="">
            </div>
            <div class="msg-content">{{ item.content }}</div>
          </div>
          <div v-else class="chatting-item other clearfix">
            <div class="msg-date">
              {{ item.date }}
            </div>
            <div class="msg-from">
             <img src="./zhayan.gif" alt="">
              <!--<span class="msg-author">{{ item.from }}</span>-->
            </div>
            <div class="msg-content">
            <div v-if=" item.size1 == 1 " >
                       <div v-for="value in item.content" class="msg">
{{value.content}}
</div>
            </div>
            <div v-else>

              <div class="msg">为您找到以下相似问题：
            </div>
            <div v-for="value in item.content" class="msg" @click="send1(value.title)" >
{{value.title}}
</div>
</div>

            <!--{{ item.content }}-->



            </div>
          </div>
        </div>
        <!-- <div class="online">
          microzz上线了
        </div> -->
      </div>

      <!-- 输入区域 -->
      <div class="chatting-input">
        <input @keyup.enter="send" v-model.trim="inputContent" placeholder="与智能小白聊些啥">
        <button @click="send">发送</button>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: 'chatting',
  data() {
    return {
      msgs: localStorage.msgs_ai && JSON.parse(localStorage.msgs_ai) || [
        // { date: '2015-11-09 09:57:08', from: 'microzz', avatarUrl: `http://omratag7g.bkt.clouddn.com/icon-avatar${this.random(11)}.svg`, content: 'test', self: false}
      ],
      inputContent: '',
      isRedAI: false,
      oContent: {}
    }
  },
  watch: {
    msgs(val) {
      localStorage.msgs_ai = JSON.stringify(val);
    }
  },
  computed: {
//    name() {
//      return this.$store.state.name;
//    },
    avatarUrl() {
      return this.$store.state.avatarUrl;
    }
  },
  beforeRouteEnter(to, from, next) {
//    if (!localStorage.name) {
//      next('/')
//    } else {
      next();
//    }
  },

  // beforeRouteLeave(to, from, next) {
  //   console.log(this.$refs);
  // },

  mounted() {
    setInterval(() => this.isRedAI = !this.isRedAI, 2500);
    this.oContent = document.getElementById('chattingContent');
    setTimeout(() => {
      this.$refs.chattingContent.scrollTop = this.$refs.chattingContent.scrollHeight
    }, 0)
  },
  methods: {
    send() {
      this.oContent.scrollTop = this.oContent.scrollHeight;
      if (this.inputContent === '') {
        return;
      } else {
        this.msgs.push({
          date: this.moment().format('MM-DD HH:mm:ss'),
//          loc: localStorage.addr,
//          from: `${localStorage.name}`,
          content: this.inputContent,
          self: true,
          avatarUrl:'./user.svg'
        });
        setTimeout(() => {
          this.$refs.chattingContent.scrollTop = this.$refs.chattingContent.scrollHeight
        }, 0)
//        this.axios.get(`https://zhaoplus.com/api/AI?search=${this.inputContent}`)
        this.axios.get(`http://117.159.25.217:8686/wechat/ask/askMsg?ask=${this.inputContent}&companyid=0`)
          .then(result => {
            this.msgs.push({
              date: this.moment().format('MM-DD HH:mm:ss'),
              from: '智能机器人',
              content: result.data.depotKnowledgeList,
              msg: result.data.content,
              size1:result.data.knowledgeSize,
              self: false,
              // avatarUrl: 'https://icdn.microzz.com/20170426_vue_chat/icon-ai.svg'
              avatarUrl: './zhayan.gif'
            })
          })
          .then(() => {
            this.$refs.chattingContent.scrollTop = this.$refs.chattingContent.scrollHeight
          })
        this.inputContent = '';
      };


    },

    send1(msg) {
//      alert("click");
//      alert(msg);
      this.oContent.scrollTop = this.oContent.scrollHeight;
      if (msg === '') {
        return;
      } else {
        this.msgs.push({
          date: this.moment().format('MM-DD HH:mm:ss'),
//          loc: localStorage.addr,
//          from: `${localStorage.name}`,
          content: msg,
          self: true,
          avatarUrl:'./user.svg'
        });
        setTimeout(() => {
          this.$refs.chattingContent.scrollTop = this.$refs.chattingContent.scrollHeight
        }, 0)
//        this.axios.get(`https://zhaoplus.com/api/AI?search=${this.inputContent}`)
        this.axios.get(`http://117.159.25.217:8686/wechat/ask/askMsg?ask=`+msg+`&companyid=0`)
          .then(result => {
            this.msgs.push({
              date: this.moment().format('MM-DD HH:mm:ss'),
              from: '智能机器人',
              content: result.data.depotKnowledgeList,
              msg: result.data.content,
              size1:result.data.knowledgeSize,
              self: false,
              // avatarUrl: 'https://icdn.microzz.com/20170426_vue_chat/icon-ai.svg'
              avatarUrl: './total.svg'
            })
          })
          .then(() => {
            this.$refs.chattingContent.scrollTop = this.$refs.chattingContent.scrollHeight
          })
//        this.inputContent = '';
      };


    },
  }
}
</script>

<style lang="scss" scoped>
  $blue: #2196f3;

  .chatting {
    display: flex;
    flex-direction: column;

    width: 100%;
    height: 100%;

    .chatting-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      height: 50px;
      width: 100%;
      background-color: $blue;
      color: white;
      padding-left: 10px;
      padding-right: 15px;

      /*.chatting-back {*/
        /*width: 30px;*/
        /*height: 30px;*/
        /*i.icon-back {*/
          /*background: url('../../common/icons/icon-group2.svg') no-repeat;*/
          /*background-size: contain;*/
        /*}*/
      /*}*/

      .chatting-back {
        width: 32px;
        height: 32px;
        .icon-back {
          background: url('../../common/icons/icon-ai.svg') no-repeat;
          background-size: contain;
        }
        .icon-back2 {
          background: url('../../common/icons/icon-ai2.svg') no-repeat;
          background-size: contain;
        }
      }

      .chatting-title {
        i.icon-group {
          vertical-align: top;
          width: 30px;
          height: 30px;
          background: url('../../common/icons/icon-group.svg') no-repeat;
          background-size: contain;
          margin-right: 3px;
        }
      }

      .chatting-menu {
        width: 30px;
        height: 30px;
        i.icon-menu {
          background: url('../../common/icons/icon-index.svg') no-repeat;
          background-size: contain;
        }
      }
    }

    .chatting-content {
      flex: 1;
      width: 100%;
      background-color: rgba(0, 0, 0, .1);
      overflow: auto;
      .chatting-item {
        padding: 10px;
        width: 100%;
        .msg-date {
          text-align: center;
          color: gray;
          font-size: 80%;
        }
        .msg-from {
          display: flex;
          align-items: center;
          span.loc {
            color: gray;
            font-size: 60%;
            margin-right: 5px;
          }
          .msg-author {
            font-size: 1.2rem;
          }
          img {
            width: 30px;
            height: 30px;
            border-radius: 15px;
          }
        }
        .msg-content {
          margin-top: 5px;
          background-color: white;
          width: 300px;
          padding: 6px 10px;
          border-radius: 10px;
        }
      }

      .chatting-item + .chatting-item {
        margin-top: 10px;
      }
      .self {
        .msg-from {
          display: flex;
          justify-content: flex-end;
          align-items: center;
          img {
            margin-left: 10px;
          }
        }

        .msg-content {
          float: right;
          word-wrap: break-word;
          word-break: break-all;
          margin-right: 10px;
        }


      }

      .other {
        .msg-from {
          display: flex;
          justify-content: flex-start;
          align-items: center;
          img {
            margin-right: 10px;
          }
        }

        .msg-content {
          float: left;
          margin-left: 10px;
          word-wrap: break-word;
          word-break: break-all;
          .msg{
            margin-top: 10px;
          }
        }

      }

      .online {
        width: 200px;
        // max-width: 100%;
        margin: 3px auto;
        border-radius: 4px;
        text-align: center;
        background-color: #FFFDE7;
      }
    }

    .chatting-input {
      display: flex;
      height: 40px;
      width: 100%;
      input {
        flex: 1;
        padding-left: 10px;
        // padding-top: 10px;
        height: 100%;
        font-size: 1.3rem;
      }
      button {
        width: 60px;
        height: 100%;
        background-color: $blue;
        color: white;
        font-size: 1.2rem;
      }
    }
  }
</style>
