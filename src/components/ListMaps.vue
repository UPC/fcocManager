<template>
  <div class='col-sm-6 col-sm-offset-3'>
    <h1>Llistat de mapes</h1>
    <b-button to="/addMap" variant="primary">
      <b-icon icon="plus" />
      Nou mapa
    </b-button>
    <b-table ref="table" striped responsive=" md" hover :fields="fields" :items="items">
      <template v-slot:cell(actions)="row">
        <b-button-group>
          <b-button @click="onEdit(row.item.id)" variant="success">
            <b-icon icon="pencil"/>
          </b-button>
          <b-button @click="onDelete(row.item.id)" variant="danger">
            <b-icon icon="trash"/>
          </b-button>
        </b-button-group>
      </template>
    </b-table>
  </div>
</template>

<script>
  import axios from "axios";

  export default {
    name: "ListMaps",
    components: {},
    data() {
      return {
        fields: [
          {key: 'name', sortable: true},
          {key: 'club', sortable: true},
          {key: 'cartographer', sortable: true},
          {key: 'cartography', sortable: true},
          {key: 'year', sortable: true},
          'actions'
        ],
        items: [],
        user: null,
      }
    },
    mounted: function () {
      this.getMyMaps();

    },
    methods: {

     getMyMaps() {
       this.user =  JSON.parse (localStorage.getItem("user"));
        axios.get('http://localhost:3000/api/maps/' + this.user)
          .then(res => {
            this.items = res.data;
          })
      },
      onEdit: function (event) {
        this.$router.push('/editMap/'+event);
      },
      onDelete: function (event) {
        if(confirm("Do you really want to delete?")) {
          axios.delete('http://localhost:3000/api/map/' + event, {
            headers: {
              'Authorization': ` ${this.$store.state.token}`
            }
          })
            .then(response => {
              const index = this.items.findIndex(items => items.id === event);
              if (~index)
                this.items.splice(index, 1)
            })
            .catch(error => {
              console.log(error);
            })
        }
      }
    }
  }
</script>

<style scoped>

</style>
