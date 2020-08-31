<template>
  <div class="team">
    <v-container class="my-5">
      <h1 class="subheading grey--text my-10">
        Team
      </h1>

      <v-row wrap>
        <v-col
          v-for="person in team"
          :key="person.name"
          xs="12"
          sm="6"
          md="4"
          lg="3"
        >
          <v-card flat outlined class="text-xs-center ma-3">
            <v-container>
              <v-row class="mt-3" justify="center">
                <v-avatar size="100" class="grey lighten-3">
                  <img :src="person.avatar">
                </v-avatar>
              </v-row>
            </v-container>

            <v-card-text>
              <div class="subheading">
                {{ person.name }}
              </div>
              <div class="grey--text">
                {{ person.role }}
              </div>
            </v-card-text>
            <v-card-actions>
              <v-btn text color="grey">
                <v-icon small left>
                  mdi-message
                </v-icon>
                <span class="grey--text">Message</span>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
export default {
  data () {
    return {
      team: [
        { name: 'The Net Ninja', role: 'Web developer', avatar: '/avatar-1.png' },
        { name: 'Ryu', role: 'Graphic designer', avatar: '/avatar-2.png' },
        { name: 'Chun Li', role: 'Web developer', avatar: '/avatar-3.png' },
        { name: 'Gouken', role: 'Social media maverick', avatar: '/avatar-4.png' },
        { name: 'Yoshi', role: 'Sales guru', avatar: '/avatar-5.png' }

      ]
    }
  }
}
</script>
