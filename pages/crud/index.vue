<template>
  <div data-app>
    <v-data-table
      :headers="headers"
      :items="items"
      class="elevation-1"
      sort-by="id"
    >
      <template v-slot:top
        ><v-toolbar flat
          ><v-toolbar-title>MY CRUD</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }"
              ><v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on"
                >New Item</v-btn
              ></template
            >
            <v-card>
              <v-card-title
                ><span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row
                    ><v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedUser.firstname"
                        label="Firstname"
                      ></v-text-field>
                    </v-col>
                    ><v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedUser.lastname"
                        label="Lastname"
                      ></v-text-field>
                    </v-col>
                    ><v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedUser.roleid"
                        label="RoleId"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer> </v-spacer>
                <v-btn color="blue darken-1" text @click="dialog = false"
                  >Cancel</v-btn
                >
                <v-btn color="blue darken-1" text @click="save">Save</v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Are you sure you want to delete this item?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:[`item.actions`]="{ item }"
        ><v-icon small @click="editItem(item)">mdi-pencil</v-icon>
        <v-icon small @click="deleteItem(item)">mdi-delete</v-icon></template
      >
      <template v-slot:no-data
        ><v-btn color="primary" @click="fetchData">Reset</v-btn></template
      >
    </v-data-table>
  </div>
</template>

<script>
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        text: "#",
        align: "start",
        sortable: false,
        value: "id",
      },
      { text: "Firstname", value: "firstname" },
      { text: "Lastname", value: "lastname" },
      { text: "RoleId", value: "roleid" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    items: [],
    editedIndex: -1,
    editedUser: {
      firstname: "",
      lastname: "",
      roleid: 0,
    },
    defaultUser: {
      firstname: "",
      lastname: "",
      roleid: 0,
    },
  }),
  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
  },
  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      const res = await this.$axios.$get(`http://localhost:5000/users`);
      this.items = res;
    },

    editItem(item) {
      this.editedIndex = this.items.indexOf(item);
      this.editedUser = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      this.editedIndex = this.items.indexOf(item);
      this.editedUser = Object.assign({}, item);
      this.dialogDelete = true;
    },

    async deleteItemConfirm() {
      const res = await this.$axios.$delete(
        `http://localhost:5000/users/${this.editedUser.id}`
      );
      this.fetchData();
      this.closeDelete()
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    async save() {
      if (this.editedIndex > -1) {
        const res = await this.$axios.$put(
          `http://localhost:5000/users/${this.editedUser.id}`,
          this.editedUser
        );
      } else {
        const res = await this.$axios.$post(
          `http://localhost:5000/users/add`,
          this.editedUser
        );
      }
      this.fetchData();
      this.close();
    },
  },
};
</script>

<style lang="scss" scoped></style>
