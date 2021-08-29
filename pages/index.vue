<template>
  <section class="d-flex flex-column fill-height">
    <v-navigation-drawer
      v-model="drawer"
      absolute
      right
      temporary
      :width="400"
    >
      <section class="pa-3">
        <h2 class="text-h6 py-1">
          Group Chat
        </h2>

        <v-divider />

        <v-list three-line>
          <template v-for="(item, index) in items">
            <v-list-item
              v-if="index % 2 == 0"
              :key="item.title"
            >
              <v-list-item-avatar color="indigo" size="48">
                <span class="white--text text-h6">CL</span>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title class="font-weight-bold">
                  {{ item.title }}
                </v-list-item-title>
                <v-list-item-subtitle>
                  {{ item.subtitle }}
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
            <v-list-item
              v-else
              :key="item.title"
              class="text-right"
            >
              <v-list-item-content>
                <v-list-item-title class="font-weight-bold">
                  {{ item.title }}
                </v-list-item-title>
                <v-list-item-subtitle>
                  {{ item.subtitle }}
                </v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </template>
        </v-list>

        <v-divider />

        <div class="d-flex flex-column align-end mt-4">
          <v-text-field label="Say something..." solo style="width: 100%;" />
          <v-btn fab color="primary" small>
            <v-icon dark>
              mdi-send
            </v-icon>
          </v-btn>
        </div>
      </section>
    </v-navigation-drawer>
    <section class="flex-grow-1">
      dd
    </section>
    <v-footer>
      <v-row justify="center" class="ma-1">
        <v-btn
          class=""
          fab
          small
          dark
          color="red"
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
          outlined
        >
          <v-icon dark>
            mdi-microphone
          </v-icon>
        </v-btn>

        <v-btn
          class="mx-2"
          fab
          small
          dark
          color="primary"
          outlined
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
          outlined
        >
          <v-icon dark>
            mdi-video
          </v-icon>
        </v-btn>

        <v-spacer />

        <v-btn
          class=""
          fab
          small
          dark
          color="primary"
          outlined
          @click="drawer = true"
        >
          <v-icon dark>
            mdi-message
          </v-icon>
        </v-btn>
      </v-row>
    </v-footer>
  </section>
</template>

<script>
export default {
  data () {
    return {
      drawer: false,
      group: false,

      items: [
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/1.jpg',
          title: 'Brunch this weekend?',
          subtitle: '<span class="text--primary">Ali Connors</span> &mdash; I\'ll be in your neighborhood doing errands this weekend. Do you want to hang out?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/2.jpg',
          title: 'Tu',
          subtitle: '<span class="text--primary">to Alex, Scott, Jennifer</span> &mdash; Wish I could come, but I\'m out of town this weekend.'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/3.jpg',
          title: 'Oui oui',
          subtitle: '<span class="text--primary">Sandra Adams</span> &mdash; Do you have Paris recommendations? Have you ever been?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
          title: 'Birthday gift',
          subtitle: '<span class="text--primary">Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?'
        },
        {
          avatar: 'https://cdn.vuetifyjs.com/images/lists/5.jpg',
          title: 'Recipe to try',
          subtitle: '<span class="text--primary">Britta Holt</span> &mdash; We should eat this: Grate, Squash, Corn, and tomatillo Tacos.'
        }
      ]
    }
  }
}
</script>
