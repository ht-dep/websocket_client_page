<template>
  <div id="app" class="layout">
    <Layout>
      <Header :style="{position: 'fixed', width: '100%'}">
        <Menu mode="horizontal" theme="dark" active-name="1" @on-select="clickMenu">
          <!--<div class="layout-logo"></div>-->
          <Row>
            <i-col span="6">
              <div>
                <i-input v-model="wsserver" placeholder="请输入...  ws://x.x.x.x:9999" style="width: 300px"></i-input>
                l
              </div>
            </i-col>
            <i-col span="16">
              <div class="layout-nav">
                <MenuItem name="1">
                  <Icon type="ios-navigate"></Icon>
                  连接
                </MenuItem>
                <MenuItem name="2">
                  <Icon type="ios-keypad"></Icon>
                  断开
                </MenuItem>
                <MenuItem name="3">
                  <Icon type="ios-analytics"></Icon>
                  清空
                </MenuItem>

              </div>
            </i-col>
            <i-col span="2">
              <Button type="primary">{{getWebsockState.state}}</Button>
            </i-col>
          </Row>
        </Menu>
      </Header>
      <Content :style="{margin: '88px 20px 0', background: '#fff', }">
        <Row>
          <Row>
            <i-input display type="textarea" disabled :rows="15" :placeholder="getChatLog">
            </i-input>
          </Row>
          <Row>
            <i-col span="18">
              <i-input type="text" v-model="msg" placeholder="请输入..."></i-input>
            </i-col>
            <i-col span="6">
              <i-button :style="{ width: 1}" @click="sendWebsock" type="primary" long>发送</i-button>
            </i-col>
          </Row>
        </Row>

        <!--<i-input disabled type="textarea"  placeholder="请输入..."></i-input>-->
      </Content>

      <Footer class="layout-footer-center">
        2011-2016 &copy; TalkingData
      </Footer>
    </Layout>
  </div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        wsserver: '',
        chat_now: "",
        chat_log: [],
        msg: "",
        websock: null,
        websock_state: 0,


      }
    },
    methods: {
      clickMenu(selected) {
        if (selected === "1") {
          console.log("连接");
          this.initWebSocket();
        }
        else if (selected === "2") {
          console.log("断开");
          this.websocketclose();
        }
        else if (selected === "3") {
          console.log("清空");
          this.chat_log = [];
        }
        else if (selected === "4") {
          console.log("示例");
        }
      },
      initWebSocket() {
        //ws地址
        this.websock = new WebSocket(this.wsserver);
        this.websock.onopen = this.websocketonopen;
        this.websock.onmessage = this.websocketonmessage;
        this.websock.onclose = this.websocketclose;
        this.websock_state = 1;
      },
      websocketonopen(e) {
        this.websocketsend({"type": "client", "message": "请求连接"});
        this.websock_state = 1;
      },
      websocketonmessage(e) {
        console.log(e);
        // const redata = JSON.parse(e.data);
        console.log(e.data);
        this.chat_log.push("server ：" + e.data);


      },
      websocketsend(agentData) {
        this.websock.send(JSON.stringify(agentData));
        // this.websock.send(agentData);
      },
      sendWebsock() {
        console.log(this.msg);
        this.websocketsend(this.msg);
        this.chat_log.push("client ：" + this.msg);
      },
      websocketclose(e) {
        console.log("connection closed (" + e + ")");
        this.websock_state = 0;
        //连接关闭，则自动重连
        // this.initWebSocket();
      }
    },
    computed: {
      getChatLog() {
        return this.chat_log.join(" \n");
      },

      getWebsockState() {
        console.log("************");
        console.log(this.websock_state);
        if (this.websock === null) {
          return {"state": "运行状态：未连接"}
        } else if (this.websock_state === 0) {
          return {"state": "运行状态：未连接"}
        } else if (this.websock_state === 1) {
          return {"state": "运行状态：已连接"}
        } else {
          return {"state": "运行状态：未连接"}
        }

      },

    }

  }
</script>


<style scoped>
  .layout {
    border: 1px solid #d7dde4;
    background: #f5f7f9;
    position: relative;
    border-radius: 4px;
    overflow: hidden;
  }

  .layout-logo {
    width: 100px;
    height: 30px;
    background: #5b6270;
    border-radius: 3px;
    float: left;
    position: relative;
    top: 15px;
    left: 20px;
  }

  .layout-nav {
    width: 420px;
    margin: 0 auto;
    margin-right: 20px;
  }

  .layout-footer-center {
    text-align: center;
  }
</style>
