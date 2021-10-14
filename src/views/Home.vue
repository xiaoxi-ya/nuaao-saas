<template>
  <v-app id="inspire">
    <v-navigation-drawer
        v-model="drawer"
        :mini-variant.sync="mini"
        :light="true"
        app
    >
      <!--  -->
      <v-list :expand="true">
        <v-list-item class="text-subtitle-2 justify-center">NUAAO  PRO</v-list-item>
        <v-list-item>
          <v-list-item-icon>
            <v-icon>mdi-home</v-icon>
          </v-list-item-icon>

          <v-list-item-title>Home</v-list-item-title>
        </v-list-item>

        <v-list-group
            :value="true"
            prepend-icon="mdi-account-circle"
        >
          <template v-slot:activator>
            <v-list-item-title>Users</v-list-item-title>
          </template>

          <v-list-group
              :value="true"
              no-action
              sub-group
          >
            <template v-slot:activator>
              <v-list-item-content>
                <v-list-item-title>Admin</v-list-item-title>
              </v-list-item-content>
            </template>

            <v-list-item
                v-for="([title, icon], i) in admins"
                :key="i"
                link
            >
              <v-list-item-title v-text="title"></v-list-item-title>

              <v-list-item-icon>
                <v-icon v-text="icon"></v-icon>
              </v-list-item-icon>
            </v-list-item>
          </v-list-group>

          <v-list-group
              no-action
              sub-group
          >
            <template v-slot:activator>
              <v-list-item-content>
                <v-list-item-title>Actions</v-list-item-title>
              </v-list-item-content>
            </template>

            <v-list-item
                v-for="([title, icon], i) in cruds"
                :key="i"
                link
            >
              <v-list-item-title v-text="title"></v-list-item-title>

              <v-list-item-icon>
                <v-icon v-text="icon"></v-icon>
              </v-list-item-icon>
            </v-list-item>
          </v-list-group>
        </v-list-group>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar app class="white" :dense="true">
      <v-app-bar-nav-icon @click="drawer=!drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>Application</v-toolbar-title>

    </v-app-bar>

    <v-main>
      <div class="px-5 py-3">
        <!--  -->
        <v-row>
          <v-col cols="5"></v-col>
          <v-col cols="7">
            <v-sheet
                tile
                height="54"
                class="d-flex"
            >
              <v-btn
                  icon
                  class="ma-2"
                  @click="$refs.calendar.prev()"
              >
                <v-icon>mdi-chevron-left</v-icon>
              </v-btn>
              <v-select
                  v-model="type"
                  :items="types"
                  dense
                  outlined
                  hide-details
                  class="ma-2"
                  label="type"
              ></v-select>
              <v-select
                  v-model="mode"
                  :items="modes"
                  dense
                  outlined
                  hide-details
                  label="event-overlap-mode"
                  class="ma-2"
              ></v-select>
              <v-select
                  v-model="weekday"
                  :items="weekdays"
                  dense
                  outlined
                  hide-details
                  label="weekdays"
                  class="ma-2"
              ></v-select>
              <v-spacer></v-spacer>
              <v-btn
                  icon
                  class="ma-2"
                  @click="$refs.calendar.next()"
              >
                <v-icon>mdi-chevron-right</v-icon>
              </v-btn>
            </v-sheet>
            <v-sheet height="600">
              <v-calendar
                  ref="calendar"
                  v-model="value"
                  :weekdays="weekday"
                  :type="type"
                  :events="events"
                  :event-overlap-mode="mode"
                  :event-overlap-threshold="30"
                  :event-color="getEventColor"
                  @change="getEvents"
              ></v-calendar>
            </v-sheet>
          </v-col>
        </v-row>
      </div>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      drawer:null,
      admins: [
        ['Management', 'mdi-account-multiple-outline'],
        ['Settings', 'mdi-cog-outline'],
      ],
      cruds: [
        ['Create', 'mdi-plus-outline'],
        ['Read', 'mdi-file-outline'],
        ['Update', 'mdi-update'],
        ['Delete', 'mdi-delete'],
      ],
      mini:false,
      type: 'month',
      types: ['month', 'week', 'day', '4day'],
      mode: 'stack',
      modes: ['stack', 'column'],
      weekday: [0, 1, 2, 3, 4, 5, 6],
      weekdays: [
        { text: 'Sun - Sat', value: [0, 1, 2, 3, 4, 5, 6] },
        { text: 'Mon - Sun', value: [1, 2, 3, 4, 5, 6, 0] },
        { text: 'Mon - Fri', value: [1, 2, 3, 4, 5] },
        { text: 'Mon, Wed, Fri', value: [1, 3, 5] },
      ],
      value: '',
      events: [],
      colors: ['blue', 'indigo', 'deep-purple', 'cyan', 'green', 'orange', 'grey darken-1'],
      names: ['Meeting', 'Holiday', 'PTO', 'Travel', 'Event', 'Birthday', 'Conference', 'Party'],
    }
  },
  methods:{
    getEvents ({ start, end }) {
      const events = []

      const min = new Date(`${start.date}T00:00:00`)
      const max = new Date(`${end.date}T23:59:59`)
      const days = (max.getTime() - min.getTime()) / 86400000
      const eventCount = this.rnd(days, days + 20)

      for (let i = 0; i < eventCount; i++) {
        const allDay = this.rnd(0, 3) === 0
        const firstTimestamp = this.rnd(min.getTime(), max.getTime())
        const first = new Date(firstTimestamp - (firstTimestamp % 900000))
        const secondTimestamp = this.rnd(2, allDay ? 288 : 8) * 900000
        const second = new Date(first.getTime() + secondTimestamp)

        events.push({
          name: this.names[this.rnd(0, this.names.length - 1)],
          start: first,
          end: second,
          color: this.colors[this.rnd(0, this.colors.length - 1)],
          timed: !allDay,
        })
      }

      this.events = events
    },
    getEventColor (event) {
      return event.color
    },
    rnd (a, b) {
      return Math.floor((b - a + 1) * Math.random()) + a
    },
  },
}
</script>

<style scoped>

</style>