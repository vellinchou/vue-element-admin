<template>
  <div class="documentation-contaniner">
    <video  id = "remoteVideo" > </video> 
    <video  id = "localVideo"  muted = "静音" > </video>
    <button @click="register">注册</button>
    <button @click="call">呼叫</button>
  </div>
</template>
<script>
import DropdownMenu from '@/components/Share/dropdownMenu'
import SIP from 'sip.js'

export default {
  name: 'documentation',
  components: { DropdownMenu },
  data() {
    return {
      simple: null,
      session: null,
      inviteUri: '1002@rtc.vsbc.com'
    }
  },
  mounted() {
    console.log(typeof SIP)
    var options = {
      media: {
        local: {
          video: document.getElementById('localVideo')
        },
        remote: {
          video: document.getElementById('remoteVideo')
          // 这是进行音频/视频通话而不是视频通话
          // 音频所必需的：document.getElementById（' remoteVideo '）
        }
      },
      ua: {
        uri: '1001@rtc.vsbc.com',
        wsServer: 'wss://rtc.vsbc.com:5092/wss',
        authorizationUser: '1001',
        traceSip: true,
        password: '1001',
        displayName: '1001',
        userAgentString: 'SIP.js/0.7.8 BB',
        register: false,
        registerExpires: 60,
        noAnswerTimeout: 45,
        wsServerMaxReconnection: 3,
        wsServerReconnectionTimeout: 5,
        iceCheckingTimeout: 2000,
        stunServers: [],
        turnServers: [],
        pushedRegister: false
      }
    }

    this.simple = new SIP.UA(options)

    this.simple.on('connected', function() {
      console.log('sip is connected')
    })

    this.simple.on('registered', function() {
      console.log('sip is registered')
    })

    this.simple.on('unregistered', function() {
      console.log('sip is unregistered')
    })

    this.simple.on('invite', function(session) {
      console.log('sip is invite')
    })

    this.simple.on('message', function(message) {
      console.log('sip is message')
    })
  },
  methods: {
    register() {
      this.simple.register()

      console.log(typeof this.simple.isRegistered())

      setTimeout(() => {
        console.log(`isRegistered: ${this.simple.isRegistered()}`)
      }, 3000)
    },
    call() {
      // this.simple.call("1002@rtc.vsbc.com")
      var options = {
        media: {
          constraints: {
            audio: true,
            video: true
          }
        }
      }

      this.session = this.simple.invite(this.inviteUri, options)

      this.session.invite(this.inviteurl, options)

      this.session.on('invite', session => {
        session.accept()
      })

      this.session.on('accepted', () => {
        console.log('accepted')
      })

      this.session.on('notify', request => {
        console.log('notify', request)
      })

      this.session.on('failed', () => {
        console.log('failed')
      })
    },

    getRemoteVideo() {
      var domElement = document.getElementById('remoteVideo')
      var pc = this.session.SessionDescriptionHandler.peerConnection
      var remoteStream = new MediaStream()
      pc.getReceivers().forEach(function(receiver) {
        var track = receiver.track
        if (track) {
          remoteStream.addTrack(track)
        }
      })
      domElement.srcObject = remoteStream
      domElement.play()
    }
  }
}
</script>


<style rel="stylesheet/scss" lang="scss" scoped>
.documentation-container {
  margin: 50px;
  .document-btn {
    float: left;
    margin-left: 50px;
    vertical-align: middle;
    display: block;
    cursor: pointer;
    background: black;
    color: white;
    height: 60px;
    width: 200px;
    line-height: 60px;
    font-size: 20px;
    text-align: center;
  }
}
video {
  border: 1px solid black;
}
</style>