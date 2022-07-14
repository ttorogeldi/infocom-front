<template>
  <div class="ma-15">
    <v-card width="1000px" class="mx-15">
      <div class="text-right">
        <v-btn color="blue" @click="goAdd()" class="white--text font-weight-bold ma-3">Add</v-btn>
      </div>
      <v-card>
        <v-text-field
            v-on:keydown.enter.prevent='enter()'
            v-model="search"
            @input="Search(search)"
            label="Search"
            class="mx-4"
        ></v-text-field>

        <v-data-table
            :headers="headers"
            :items="people"
            item-key="name"
            class="elevation-1"
        >
          <template v-slot:top>

          </template>

          <template v-slot:[`item.btn`]="{ item }">
            <div class="text-right">
              <v-btn color="green" @click="go(item.id)" class="white--text font-weight-bold mr-3">Edit</v-btn>
              <v-btn color="red" @click="confirmDel(item.id)" class="white--text font-weight-bold">Remove</v-btn>
            </div>
          </template>

        </v-data-table>
      </v-card>
    </v-card>
    <transition name="fade">
      <div class="popup-modal" v-if="isVisible">
        <div class="window">
          <slot>
            <div class="ma-2 font-weight-bold">Are you sure?</div>
            <v-btn color="yellow" @click="isVisible=false" class="white--text font-weight-bold mr-3">Cancel</v-btn>
            <v-btn color="red" @click="remove(currentId)" class="white--text font-weight-bold">Remove</v-btn>
          </slot>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'

export default {
  name: 'HomeView',
  data() {
    return {
      search: '',
      calories: '',
      people: [],
      isVisible: false,
      currentId: ''
    }
  },
  methods: {
    enter(){

    },
    confirmDel(id) {
      this.currentId = id
      this.isVisible = true
    },
    open() {
      this.isVisible = true
    },
    close() {
      this.isVisible = false
    },
    go(id) {
      this.$router.push('/edit-person?id=' + id)
    },
    goAdd() {
      this.$router.push('/add-person')
    },
    remove(id) {
      axios
          .delete('https://localhost:7023/Person/DeletePerson?Id=' + id)
          .then(response => {
            console.log(response)
            this.isVisible = false
            this.$toast.info('Person deleted');
            this.getPeople()
          })
    },
    getPeople() {
      axios
          .get('https://localhost:7023/Person/GetPeople')
          .then(response => {
            this.people = response.data

          })
    },
    Search(searchData) {
      try {
        if (searchData !== '') {
          axios
              .get('https://localhost:7023/Person/SearchPerson?name=' + searchData)
              .then(response => {
                this.people = response.data.result
              }).catch(er=>{
                console.error(er)
          })
        }else {
          this.getPeople()
        }
      } catch (e) {
        console.error(e)
      }
    }
  },
  mounted() {
    this.getPeople()
  }, computed: {
    headers() {
      return [
        {
          text: 'Sure Name',
          align: 'start',
          value: 'sureName',
        },
        {
          text: 'Last Name',
          value: 'lastName',
        },
        {text: 'Age', value: 'age'},
        {text: 'Gender', value: 'sex'},
        {text: '', value: 'btn'},
      ]
    },
  },

}
</script>
<style scoped>
.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.popup-modal {
  background-color: rgba(0, 0, 0, 0.5);
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 0.5rem;
  display: flex;
  align-items: center;
  z-index: 1;
}

.window {
  background: #fff;
  border-radius: 5px;
  box-shadow: 2px 4px 8px rgba(0, 0, 0, 0.2);
  max-width: 480px;
  margin-left: auto;
  margin-right: auto;
  padding: 1rem;
}
</style>
