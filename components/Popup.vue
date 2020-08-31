<template>
  <v-dialog v-model="dialog" max-width="600px" @click:outside="resetDialog">
    <template v-slot:activator="{ on }">
      <v-btn text class="success" v-on="on">
        Add New Project
      </v-btn>
    </template>

    <v-card>
      <v-card-title>
        <h2>Add a New Project</h2>
      </v-card-title>
      <v-card-text>
        <v-form ref="form" :lazy-validation="lazyValidation" class="px-3">
          <v-text-field v-model="title" :autofocus="focus" label="Title" prepend-icon="mdi-folder" :rules="inputRules" />
          <v-textarea v-model="content" label="Information" prepend-icon="mdi-pencil" :rules="inputRules" />

          <v-menu v-model="menu" :close-on-content-click="false">
            <template v-slot:activator="{ on }">
              <v-text-field
                :value="formattedDate"
                clearable
                label="Due date"
                prepend-icon="mdi-calendar"
                v-on="on"
              />
            </template>
            <v-date-picker v-model="due" @change="menu = false" />
          </v-menu>

          <v-spacer />

          <v-btn text class="success mx-0 mt-3" :loading="loading" @click="submit">
            Add Project
          </v-btn>
        </v-form>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
import format from 'date-fns/format'

export default {
  data () {
    return {
      title: '',
      content: '',
      due: null,
      menu: false,
      inputRules: [
        v => !!v || 'This field is required',
        v => v.length >= 3 || 'Minimum length is 3 characters'
      ],
      loading: false,
      dialog: false,
      lazyValidation: true,
      focus: true
    }
  },
  computed: {
    formattedDate () {
      // eslint-disable-next-line
      console.log(this.due)
      return this.due ? format(new Date(this.due), 'do MMM yyyy') : ''
    }
  },
  methods: {
    submit () {
      if (this.$refs.form.validate()) {
        this.loading = true
        // eslint-disable-next-line
        // console.log(this.title, this.content)
        this.title = ''
        this.content = ''
        this.due = null
        this.$refs.form.resetValidation()
        // this.$refs.form.reset()
        this.loading = false
        this.dialog = false
        this.$emit('projectAdded')
      }
    },
    resetDialog () {
      this.title = ''
      this.content = ''
      this.due = null
      this.$refs.form.resetValidation()
    }
  }
}
</script>
