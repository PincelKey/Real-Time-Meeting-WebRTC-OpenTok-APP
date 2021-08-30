<template>
  <section class="c-app d-flex flex-column fill-height">
    <v-navigation-drawer
      v-model="isActiveChat"
      absolute
      right
      temporary
      :width="400"
    >
      <section class="pa-3">
        <h2 class="text-h6 py-1 d-flex">
          Group Chat
          <v-spacer />
          <v-btn icon color="red">
            <v-icon dark @click="isActiveChat = false">
              mdi-close
            </v-icon>
          </v-btn>
        </h2>

        <v-divider />

        <v-list three-line>
          <template v-for="(message, index) in messages">
            <v-list-item
              v-if="message.author !== userDialog.name"
              :key="message.id"
              :ref="'message-' + index"
            >
              <v-list-item-avatar color="indigo" size="48">
                <span class="white--text text-h6">{{ message.author[0] }}</span>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title class="font-weight-bold">
                  {{ message.author }}
                </v-list-item-title>
                <v-list-item-subtitle>
                  {{ message.content }}
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-list-item
              v-else
              :key="message.id"
              class="text-right"
            >
              <v-list-item-content>
                <v-list-item-title class="font-weight-bold">
                  Tu
                </v-list-item-title>
                <v-list-item-subtitle>
                  {{ message.content }}
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </template>
        </v-list>

        <div class="d-flex flex-column align-end mt-4">
          <v-text-field v-model="chatEditor" label="Say something..." solo style="width: 100%;" @keyup.enter="sendMessage()" />
          <v-btn small fab color="primary" @click="sendMessage()">
            <v-icon dark>
              mdi-send
            </v-icon>
          </v-btn>
        </div>
      </section>
    </v-navigation-drawer>

    <v-app-bar class="c-app__footer" app>
      <v-row justify="center" align="center" class="ma-1">
        <v-btn
          class=""
          fab
          small
          dark
          color="red"
          @click="closeSession()"
        >
          <v-icon dark>
            mdi-phone-hangup
          </v-icon>
        </v-btn>

        <v-spacer />

        <v-btn
          class="mx-2"
          fab
          small
          dark
          color="primary"
          :outlined="(mediaControls.audio)"
          @click="updateMediaStatus('audio')"
        >
          <v-icon dark>
            {{ (mediaControls.audio) ? 'mdi-microphone' : 'mdi-microphone-off' }}
          </v-icon>
        </v-btn>

        <v-btn
          class="mx-2"
          fab
          small
          dark
          color="primary"
          outlined
          @click="shareScreen()"
        >
          <v-icon dark>
            mdi-monitor-screenshot
          </v-icon>
        </v-btn>

        <v-btn
          class="mx-2"
          fab
          small
          dark
          color="primary"
          :outlined="(mediaControls.video)"
          @click="updateMediaStatus('video')"
        >
          <v-icon dark>
            {{ (mediaControls.video) ? 'mdi-video' : 'mdi-video-off' }}
          </v-icon>
        </v-btn>

        <v-spacer />

        <v-btn
          v-if="publisherScreen"
          class="mr-3"
          small
          dark
          color="primary"
          outlined
          @click="stopShareScreen()"
        >
          Stop screen sharing
        </v-btn>

        <v-btn
          class="mr-3"
          fab
          small
          dark
          color="primary"
          :outlined="(isShowedUsersList)"
          @click="isShowedUsersList = !isShowedUsersList"
        >
          <v-icon dark>
            {{ (isShowedUsersList) ? 'mdi-account' : 'mdi-account-off' }}
          </v-icon>
        </v-btn>

        <v-btn
          class=""
          fab
          small
          dark
          color="primary"
          outlined
          @click="isActiveChat = true"
        >
          <v-icon dark>
            mdi-message
          </v-icon>
        </v-btn>
      </v-row>
    </v-app-bar>

    <section class="c-app__main flex-grow-1">
      <div v-show="isShowedUsersList" class="c-users fill-height pa-3">
        <v-virtual-scroll
          height="500"
          item-height="200"
          :items="users"
        >
          <template #default="{ item }">
            <div v-if="item.stream" :key="item.stream.id" class="c-user overflow-hidden">
              <div :id="`user-${item.stream.id}`" class="c-user__camera" />
              <div class="c-user__name pa-1 px-3 d-flex justify-end align-center">
                <v-btn icon :color="(monitorUser == item.stream.id) ? 'red' : 'white'" @click="setMonitor(item.stream.id)">
                  <v-icon>mdi-pin</v-icon>
                </v-btn>
              </div>
            </div>
          </template>
        </v-virtual-scroll>
      </div>
      <main ref="monitor-container" class="c-app__monitor fill-height">
        <div id="monitor" />
      </main>
    </section>

    <v-dialog
      v-model="userDialog.switcher"
      persistent
      max-width="400px"
    >
      <v-card>
        <v-card-title>
          <span class="text-h5">Start Meeting</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-text-field
              v-model="userDialog.name"
              label="Name"
              required
            />
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            @click="joinMetting()"
          >
            Join meeting
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </section>
</template>

<script>

export default {
  data () {
    return {
      userDialog: {
        switcher: false,
        name: 'User-' + (new Date()).getTime()
      },

      credentials: { apiKey: '', sessionId: '', token: '' },

      items: [
      ],

      isActiveChat: false,
      messages: [],
      chatEditor: '',

      session: null,
      users: [],
      isShowedUsersList: true,
      publisher: null,
      publisherScreen: null,
      subscribers: [],
      mediaControls: {
        video: true,
        audio: true
      },

      monitorUser: -1,

      modules: {
        OT: null
      }
    }
  },
  mounted () {
    this.modules.OT = require('@opentok/client')

    this.userDialog.switcher = true
  },
  methods: {
    async getSession () {
      this.credentials = await this.$axios.$get('https://192.168.0.118:3300/session/123')
    },
    initSession () {
      this.session = this.modules.OT.initSession(this.credentials.apiKey, this.credentials.sessionId)

      this.session.on('streamCreated', (event) => {
        this.users.push({
          stream: event.stream
        })

        window.setTimeout(() => {
          this.session.subscribe(event.stream, `user-${event.stream.id}`, {
            insertMode: 'append',
            width: '100%',
            height: '100%'
          }, this.handleError)

          this.monitorSubscriber = this.session.subscribe(event.stream, 'monitor', {
            insertMode: 'replace',
            width: '100%',
            height: '100%'
          }, this.handleError)
        }, 100)
      })

      this.session.on('streamDestroyed', (event) => {
        event.preventDefault()

        this.users = this.users.filter(el => el.stream.id !== event.stream.id)

        const subscribers = this.session.getSubscribersForStream(event.stream)

        subscribers.forEach((subscriber) => {
          this.session.unsubscribe(subscriber)
        })

        this.$refs['monitor-container'].innerHTML = '<div id="monitor"></div>'
      })

      this.session.on('signal:msg', (event) => {
        const message = JSON.parse(event.data)

        this.messages.push({
          author: message.author,
          content: message.content,
          time: message.time
        })

        window.setTimeout(() => {
          this.$refs[`message-${this.messages.length - 1}`][0].scrollIntoView()
        }, 100)
      })

      this.session.connect(this.credentials.token, (error) => {
        if (error) {
          this.handleError(error)
        } else {
          this.shareVideo()
        }
      })
    },
    closeSession () {
      this.session.disconnect()

      this.$router.push({ path: '/' })
    },

    async openRoom () {
      await this.getSession()
      this.initSession()
    },

    shareScreen () {
      this.modules.OT.checkScreenSharingCapability((response) => {
        if (!response.supported || response.extensionRegistered === false) {
          alert('Este navegador no soporte compartir pantalla')
        } else if (response.extensionInstalled === false) {
          alert('Usted necesita una extension extra')
        } else {
          this.users.push({
            stream: {
              id: 'publisher-screen',
              name: `${this.userDialog.name} - Pantalla`
            }
          })

          window.setTimeout(() => {
            this.publisherScreen = this.modules.OT.initPublisher('user-publisher-screen', {
              videoSource: 'screen',
              name: `${this.userDialog.name} - Pantalla`
            }, this.handleError)

            this.publisherScreen.on('mediaStopped', (event) => {
              this.stopShareScreen()
            })

            this.session.publish(this.publisherScreen, this.handleError)
          }, 100)
        }
      })
    },
    stopShareScreen () {
      this.users = this.users.filter(el => el.stream.id !== 'publisher-screen')
      this.session.unpublish(this.publisherScreen)

      this.publisherScreen = ''
    },

    shareVideo () {
      this.publisher = this.modules.OT.initPublisher('user-publisher', {
        insertMode: 'append',
        width: '100%',
        height: '100%',
        name: this.userDialog.name
      }, this.handleError)

      this.mediaControls.audio = true

      this.session.publish(this.publisher, this.handleError)

      this.publisher.publishAudio(false)
    },
    updateMediaStatus (media) {
      if (media === 'video') {
        this.publisher.publishVideo(this.mediaControls.video = !this.mediaControls.video)
      } else if (media === 'audio') {
        this.publisher.publishAudio(this.mediaControls.audio = !this.mediaControls.audio)
      }
    },
    setMonitor (userStreamId) {
      if (userStreamId !== 'publisher' && userStreamId !== 'publisher-screen') {
        this.monitorUser = userStreamId

        this.session.subscribe(this.users.filter(el => el.stream.id === userStreamId)[0].stream, 'monitor', {
          insertMode: 'replace',
          width: '100%',
          height: '100%'
        }, this.handleError)
      }
    },

    sendMessage () {
      this.session.signal({
        type: 'msg',
        data: JSON.stringify({
          id: (new Date()).getTime(),
          author: this.userDialog.name,
          content: this.chatEditor,
          time: `${(new Date()).getHours()}:${(new Date()).getMinutes()}`
        })
      }, (error) => {
        if (error) {
          alert('Error sending signal:', error.name, error.message)
        } else {
          this.chatEditor = ''
        }
      })
    },

    joinMetting () {
      this.userDialog.switcher = false

      this.users.push({
        stream: {
          id: 'publisher',
          name: this.userDialog.name
        }
      })

      this.openRoom()
    },
    handleError (error) {
      if (error) {
        alert(error.message)
      }
    }
  }
}
</script>
