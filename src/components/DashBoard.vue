<template>
  <v-stepper v-model="current_step">
    <v-stepper-header>
      <v-stepper-step :complete="current_step > 1" step="1">Build the glyph template</v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step :complete="current_step > 2" step="2">Map data to glyph objects</v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step :complete="current_step > 3" step="3">Place object in ANTZ</v-stepper-step>

      <v-divider></v-divider>

      <v-stepper-step step="4">Export node file</v-stepper-step>
    </v-stepper-header>

    <v-stepper-items>
      <v-stepper-content step="1">
        <v-card
          class="mb-5"
          color="grey lighten-1"

        >
          <v-container>
            <v-layout
                    row
            >
              <v-flex xs6></v-flex>
              <v-flex xs6>

                <UploadCSV/>
              </v-flex>
            </v-layout>
          </v-container>


        </v-card>

        <v-btn
          color="primary"
          @click="current_step = 2"
        >
          Continue
        </v-btn>

        <v-btn flat>Cancel</v-btn>
      </v-stepper-content>

      <v-stepper-content step="2">
        <v-card
          class="mb-5"
          color="grey lighten-1"
          height="200px"
        ></v-card>

        <v-btn
          color="primary"
          @click="current_step = 3"
        >
          Continue
        </v-btn>

        <v-btn flat>Cancel</v-btn>
      </v-stepper-content>

      <v-stepper-content step="3">
        <v-card
          class="mb-5"
          color="grey lighten-1"
          height="200px"
        ></v-card>

        <v-btn
          color="primary"
          @click="current_step = 4"
        >
          Continue
        </v-btn>

        <v-btn flat>Cancel</v-btn>
      </v-stepper-content>

      <v-stepper-content step="4">
        <v-card
          class="mb-5"
          color="grey lighten-1"
          height="200px"
        ></v-card>

        <v-btn
          color="primary"
          @click="current_step = 1"
        >
          Continue
        </v-btn>

        <v-btn flat>Cancel</v-btn>
      </v-stepper-content>

    </v-stepper-items>
  </v-stepper>
</template>


<script>
  import UploadCSV from './UploadCSV'
  export default {
    components: {
      UploadCSV
    },
    data () {
      return {
        current_step: 0
      }
    }
  }
</script>