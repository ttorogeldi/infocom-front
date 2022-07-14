<template>
  <div>
    <v-card color="grey" width="400" class="mx-auto mt-15 pa-15">
      <v-text-field
          v-model="person.sureName"
          solo
          flat
          placeholder="Sure Name"
      />
      <v-text-field
          v-model="person.lastName"
          solo
          flat
          placeholder="Last Name"
      />

      <v-slider
          v-model="person.age"
          color="orange"
          label="Age"
          hint="Be honest"
          min="1"
          max="100"
          thumb-label
      ></v-slider>
      <v-select
          v-model="sex"
          solo
          flat
          :items="['Unknow','Male','Female']"
          placeholder="Sex"
      />
      <v-btn @click="editPerson()">Edit</v-btn>
    </v-card>

  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'HomeView',
  data() {
    return {
      search: '',
      calories: '',
      person: {},
      sex: ''
    }
  },
  methods: {
    getPerson(id) {
      axios
          .get('https://localhost:7023/Person/GetPerson?id=' + id)
          .then(response => {
            this.person = response.data

            if (this.person.sex === 1)
              this.sex = 'Male'
            if (this.person.sex === 2)
              this.sex = 'Female'
            if (this.person.sex === 0)
              this.sex = 'Unknow'
          })
    },
    genderToInt(value) {
      if (value === 'Male') {
        this.person.sex = 1
      } else if (value === 'Female') {
        this.person.sex = 2
      } else
        this.person.sex = 0
    },
    editPerson() {
      this.genderToInt(this.sex)
      try {
      axios
          .patch('https://localhost:7023/Person/EditPerson?id=' + this.id, this.person)
          .then(response => {
            console.log(response)
            this.$toast.success('Person edited')
            this.$router.push('/');

          })
      } catch (e) {
        console.log(e)
      }
    }


  },
  mounted() {
    this.getPerson(location.search.slice(4))

  },
  computed: {
    id() {
      return location.search.slice(4)
    },
    Sex: {
      get() {
        return this.Sex
      },
      set(newValue) {
        this.Sex = newValue
      }
    }
  }
}
</script>
