<template>
  <div>
    <v-card color="grey" width="400" class="mx-auto mt-15 pa-15">
      <v-form>
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
        <v-btn @click="addPerson()">Add</v-btn>
      </v-form>
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
      person: {
        sureName: '',
        lastName: '',
        age: 18,
        sex: 0,

      },
      sex: 'Unknow'
    }
  },
  methods: {
    genderToInt(value) {
      if (value === 'Male') {
        this.person.sex = 1
      } else if (value === 'Female') {
        this.person.sex = 2
      } else
        this.person.sex = 0
    },
    addPerson() {
      try {
        this.genderToInt(this.sex)
        axios
            .post('https://localhost:7023/Person/AddPerson?', this.person)
            .then(response => {
              console.log(response)
              this.$toast.success('Person added')
              this.$router.push('/');
            }).catch(er=>{
          console.log(er)
          this.$toast.error('Something went wrong.')

        })
      } catch (e) {
        console.log(e)
      }
    },
    computed: {
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
}
</script>
