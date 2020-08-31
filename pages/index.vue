<template>
  <div class="dashboard">
    <v-container class="my-5">
      <h1 class="text-center subheading grey--text my-10">
        myTCA
      </h1>

      <!-- <h1> {{ dailyOpSchedules[0] }} </h1> -->

      <ValidationObserver v-slot="{ invalid }">
        <form method="post" enctype="application/x-www-form-urlencoded" @submit.prevent="onSubmit">
          <ValidationProvider v-slot="{ errors }" name="numMO" rules="required|numeric|min:1">
            <label>Number of MO
              <input v-model="form.numMO" type="text" name="numMO">
              <span>{{ errors[0] }}</span>
            </label>
          </ValidationProvider>

          <button type="submit" :disabled="invalid">
            Submit
          </button>
        </form>
      </ValidationObserver>

      <v-row justify="center" class="mx-3">
        <v-col cols="12" sm="6" md="4">
          <v-autocomplete
            v-model="clinic"
            :items="clinics"
            outlined
            color="blue-grey lighten-2"
            label="Select klinik kesihatan"
            item-text="name"
            item-value="value"
          >
            <template v-slot:item="data">
              <v-list-item-content>
                <v-list-item-title class="font-weight-medium" v-text="data.item.name" />
                <v-list-item-subtitle class="caption" v-text="data.item.group" />
              </v-list-item-content>
            </template>
          </v-autocomplete>
        </v-col>
      </v-row>

      <br>
      <v-divider />

      <h4 class="text-center subheading grey--text my-6">
        Pick a date
      </h4>

      <v-row justify="center" class="mx-3">
        <v-date-picker
          v-model="date"
          :allowed-dates="allowedDates"
          @click:date="dateClick"
        />
      </v-row>

      <div id="apptSchedule" />

      <div v-show="selectedDate">
        <br>
        <v-divider />

        <h4 class="text-center subheading grey--text my-6">
          Appointment Schedule on {{ selectedDate }}
        </h4>
      </div>

      <div v-if="selectedDate">
        <v-card v-for="(project, index) in projects" :key="index" class="mx-auto mt-9" max-width="600">
          <v-card-title>
            800am - 900am
          </v-card-title>

          <v-card-subtitle>
            Morning shift
          </v-card-subtitle>

          <div class="mx-3">
            <v-progress-linear
              :value="progressValue"
              color="light-blue"
              height="25"
            >
              <template v-slot="{ value }">
                <strong class="white--text">{{ Math.ceil(value) }}% booked</strong>
              </template>
            </v-progress-linear>
          </div>

          <v-card-actions>
            <v-btn
              class="mx-auto mt-2"
              color="black"
              text
              outlined
              @click="makeBooking(index)"
            >
              Book now
            </v-btn>
          </v-card-actions>
        </v-card>
      </div>
      <div v-else>
        <br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br>
      </div>
    </v-container>
  </div>
</template>

<script>
import {
  ValidationObserver,
  ValidationProvider
} from 'vee-validate'
import qs from 'qs'

export default {
  components: {
    ValidationObserver,
    ValidationProvider
  },
  async fetch () {
    const { data } = await this.$axios.get('http://localhost:8080/booking')
    this.dailyOpSchedules = data.dailyOpSchedule
  },
  data () {
    return {
      patient1: '', // TODO: To remove this line in production
      clinic: '',
      clinics: [
        { header: 'Pahang' },
        { name: 'KK Maran', value: 'kk_maran', group: 'Maran' },
        { name: 'KK Bandar Jengka', value: 'kk_bandar_jengka', group: 'Maran' },
        { name: 'KK Jengka 2', value: 'kk_jengka2', group: 'Maran' },
        { name: 'KK Jengka 22', value: 'kk_jengka22', group: 'Maran' },
        { divider: true },
        { header: 'Selangor' },
        { name: 'KK Bandar Botanik', value: 'kk_bandar_botanik', group: 'Klang' },
        { name: 'KK Pandamaran', value: 'kk_pandamaran', group: 'Klang' },
        { name: 'KK Shah Alam', value: 'kk_shah_alam', group: 'Klang' },
        { name: 'KK Port Klang', value: 'kk_port_klang', group: 'Klang' }
      ],
      date: new Date().toISOString().substr(0, 10),
      selectedDate: '',
      progressValue: 70,
      projects: [
        { title: 'Design a new website', person: 'The Net Ninja', due: '1st Jan 2019', status: 'ongoing', content: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Sunt consequuntur eos eligendi illum minima adipisci deleniti, dicta mollitia enim explicabo fugiat quidem ducimus praesentium voluptates porro molestias non sequi animi!' },
        { title: 'Code up the homepage', person: 'Chun Li', due: '10th Jan 2019', status: 'complete', content: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Sunt consequuntur eos eligendi illum minima adipisci deleniti, dicta mollitia enim explicabo fugiat quidem ducimus praesentium voluptates porro molestias non sequi animi!' },
        { title: 'Design video thumbnails', person: 'Ryu', due: '20th Dec 2018', status: 'complete', content: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Sunt consequuntur eos eligendi illum minima adipisci deleniti, dicta mollitia enim explicabo fugiat quidem ducimus praesentium voluptates porro molestias non sequi animi!' },
        { title: 'Create a community forum', person: 'Gouken', due: '20th Oct 2018', status: 'overdue', content: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Sunt consequuntur eos eligendi illum minima adipisci deleniti, dicta mollitia enim explicabo fugiat quidem ducimus praesentium voluptates porro molestias non sequi animi!' }
      ],
      dailyOpSchedules: '',
      form: {
        numMO: null
      }
    }
  },
  methods: {
    dateClick (date) {
      // alert(`You have just clicked the following date: ${date}`)
      // const schedule = document.getElementById('apptSchedule')
      // schedule.scrollIntoView({ behavior: 'smooth' })
      if (this.selectedDate === '') {
        this.selectedDate = date
        document.querySelector('#apptSchedule').scrollIntoView({ behavior: 'smooth' })
      } else {
        this.selectedDate = date
      }
    },
    allowedDates: val => parseInt(val.split('-')[2], 10) % 2 === 0,
    onSubmit () {
      // alert('Form has been submitted!')
      const data = qs.stringify(this.form)
      this.$axios.post('http://localhost:8080/submit', data)
        .then((response) => {
          alert('Form submitted successfully!')
        }, (response) => {
          alert('Form submit failed!')
        })
    },
    async makeBooking (btnInput) {
      // const payload = this.clinic + ', ' + this.selectedDate + ', ' + btnInput
      const payload = { clinic: this.clinic, date: this.selectedDate, opHrIdx: btnInput }
      const payloadData = qs.stringify(payload)
      // alert(payloadData)
      // this.$axios.post('http://localhost:8080/book', payloadData)
      const { data } = await this.$axios.post('http://localhost:8080/book', payloadData)
      alert(data.bookingRes)
    }
  }
}
</script>

<style>

.project.complete{
  border-left: 4px solid #3cd1c2;
}
.project.ongoing{
  border-left: 4px solid #ffaa2c;
}
.project.overdue{
  border-left: 4px solid #f83e70;
}
.v-chip.v-chip--no-color.complete{
  background: #3cd1c2;
}
.v-chip.v-chip--no-color.ongoing{
  background: #ffaa2c;
}
.v-chip.v-chip--no-color.overdue{
  background: #f83e70;
}

</style>
