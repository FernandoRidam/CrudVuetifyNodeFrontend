<template>
  <v-layout align-start>
    <v-flex>
      <v-data-table
        :headers="headers"
        :items="users"
        :search="search"
        sort-by="calories"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Categorias</v-toolbar-title>
            <v-divider
              class="mx-4"
              inset
              vertical
            ></v-divider>
            <div class="flex-grow-1"></div>
            <v-text-field class="text-xs-center" v-model="search" append-icon="mdi-magnify" label="Buscar categoria" single-line hide-details> </v-text-field>
            <div class="flex-grow-1"></div>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on }">
                <v-btn color="primary" dark class="mb-2" v-on="on">New User</v-btn>
              </template>
              <v-card>
                <v-card-title>
                  <span class="headline">{{ formTitle }}</span>
                </v-card-title>
  
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field v-model="editedItem.name" label="User Name"></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>
  
                <v-card-actions>
                  <div class="flex-grow-1"></div>
                  <v-btn color="blue darken-1" text @click="close">Cancel</v-btn>
                  <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>
        <template v-slot:item.action="{ item }">
          <v-icon
            small
            class="mr-2"
            @click="editItem(item)"
          >
            mdi-pencil
          </v-icon>
          <v-icon
            small
            @click="deleteItem(item)"
          >
            mdi-delete
          </v-icon>
        </template>
        <template v-slot:no-data>
          <v-btn color="primary" @click="initialize">Reset</v-btn>
        </template>
      </v-data-table>
    </v-flex>
  </v-layout>
</template>

<script>
  import api from '../plugins/api';

  export default {
    data() {
      return {
        dialog: false,
        search: '',
        headers: [
          {
            text: 'Name',
            align: 'center',
            sortable: false,
            value: 'name',
          },
          { text: 'Actions', value: 'action', sortable: false },
        ],
        users: [],
        editedIndex: -1,
        editedItem: {
          name: '',
        },
        defaultUser: {
          name: '',
        },
      };
    },

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New User' : 'Edit User'
      },
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
      async initialize () {
        this.users = []

        const users = await api.get('/users');
        
        this.users = users.data;
      },
  
      editItem (item) {
        this.editedIndex = this.users.indexOf(item)
        console.log( item );
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },
  
      deleteItem (item) {
        const index = this.users.indexOf(item)
        confirm('Are you sure you want to delete this item?')
        && this.users.splice(index, 1)
        && api.delete(`/user/${ item.id }`);
      },
  
      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },
  
      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.users[this.editedIndex], this.editedItem)
          
          const { name } = this.editedItem;

          api.put(`/user/${ this.editedItem.id }`,{
            name
          });
        } else {
          this.users.push(this.editedItem)

          const { name } = this.editedItem;
          
          api.post('/user', {
            name
          });
        }
        this.close()
      },
    },
  };
</script>
