<template>
  <div class="mt-3 container">
    <h3 class="mytxt">
      <v-icon>mdi-account-arrow-left</v-icon> ຂໍ້ມູນຜູ້ສະຫນັບສະຫນູນ
    </h3>
    <hr />

    <v-data-table
      :headers="headers"
      :items="desserts"
      :search="search"
      sort-by="usernam"
      class="elevation-1 mt-5"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <!-- <v-toolbar-title>Suppiler</v-toolbar-title> -->
          <v-spacer></v-spacer>

          <!-- s search -->
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="ຄົ້ນຫາ"
            single-line
            hide-details
            class="mr-5"
          ></v-text-field>
          <!-- e search -->

          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="success"
                dark
                class="mb-2"
                fab
                v-bind="attrs"
                v-on="on"
              >
                <v-icon>mdi-plus</v-icon>
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field
                        v-model="editedItem.sps_name"
                        label="ຊື່ຜູ້ສະຫນັບສະຫນູນ"
                        :rules="nameRules"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  ຍັກເລີກ
                </v-btn>
                <v-btn color="blue darken-1" text @click="save"> ບັນທຶກ </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title>ທ່ານຕ້ອງການລົບ ແທ້ ຫລື ບໍ?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="blue darken-1"
                  text
                  @click="deleteItemConfirm(editedItem.sps_id)"
                  >ຕົກລົງ</v-btn
                >
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >ຍົກເລີກ</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template #[`item.numlist`]="{ item }">
        {{ desserts.indexOf(item) + 1 }}
      </template>
      <template #[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>
    <!-- s alert -->
    <SuccessAlert :snackbar="snackbar"></SuccessAlert>
    <!-- e alert -->
  </div>
</template>

<script>
import SuccessAlert from '@/components/SuccessAlert'

export default {
  components: {
    SuccessAlert,
  },
  data() {
    return {
      search: '',
      snackbar: false,
      carouselInterval: null,
      text: 'Success!.',
      dialog: false,
      dialogDelete: false,
      nameRules: [
        (v) => !!v || 'Sponsor_name is required',
        (v) => v.length <= 30 || 'Sponsor_name must be less than 30 characters',
      ],
      headers: [
        {
          text: 'ລຳດັບ',
          align: 'center',
          sortable: false,
          value: 'numlist',
        },
        {
          text: 'ຊື່ຜູ້ສະຫນັບສະຫນູນ',
          value: 'sps_name',
          align: 'center',
        },

        { text: 'ຈັດການ', value: 'actions', sortable: false, align: 'center' },
      ],
      desserts: [],
      editedIndex: -1,
      editedItem: {
        sps_name: '',
      },
      defaultItem: {
        sps_name: '',
      },
    }
  },

  computed: {
    formTitle() {
      return this.editedIndex === -1
        ? 'ເພີ່ມ ຂໍ້ມູນຜູ້ສະຫນັບສະຫນູນ'
        : 'ແກ້ໄຂ ຂໍ້ມູນຜູ້ສະຫນັບສະຫນູນ'
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  created() {
    this.initialize()
  },

  methods: {
    async initialize() {
      try {
        await this.$axios.get('/sponsors').then((res) => {
          this.desserts = res.data
        })
      } catch (error) {
        console.log(err)
      }
    },

    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = this.desserts.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    async deleteItemConfirm(spId) {
      try {
        await this.$axios.delete(`/sponsors/${spId}`).then((res) => {
          console.log('Delete completed!')
        })
        this.initialize()
        this.dialogDelete = false
      } catch (error) {}
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    async save() {
      if (this.editedIndex > -1) {
        const sendresdata = {
          sps_name: this.editedItem.sps_name,
        }

        try {
          await this.$axios
            .put(`/sponsors/${this.editedItem.sps_id}`, sendresdata)
            .then((res) => {
              console.log('edit completed!')
            })

          this.initialize()
        } catch (error) {
          console.log(error)
        }
      } else {
        const getresdata = {
          sps_name: this.editedItem.sps_name,
        }

        try {
          await this.$axios.post('/sponsors', getresdata).then((res) => {
            console.log('Insert completed!')
          })
        } catch (error) {
          console.log(error)
        }

        this.carouselInterval = setInterval(() => {
          this.snackbar = true
        }, 800)

        this.initialize()

        this.snackbar = false
      }
      this.close()
    },
  },
}
</script>
