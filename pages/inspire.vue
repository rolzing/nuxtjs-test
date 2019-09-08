
<template>
  <v-card>
    <v-toolbar
      flat
      prominent
    >

      <v-tabs
        slot="extension"
        v-model="tabs"
        background-color="transparent"
        centered
      >
        <v-tab
          v-for="n in 2"
          :key="n"
        >
          {{ n == 1 ?'Product':'characteristic' }}
        </v-tab>
      </v-tabs>
    </v-toolbar>

    <v-tabs-items v-model="tabs">
      <v-tab-item>
        <v-card flat>
          <v-card-title>
         <h1>Product</h1>
          <v-img
          width="200" height="200"
        src="https://s3-eu-west-1.amazonaws.com/media-esp-buyviu-com/products/50fa85698a693ed73ca211ca315a20be_image_1_thumb.png"
      > 
      </v-img>
          </v-card-title>
          <v-card-text>
         <form @submit.prevent="submit">
    <v-text-field
      v-model="name"
      :error-messages="nameErrors"
      :counter="90"
      label="Name"
      required
      @input="$v.name.$touch()"
      @blur="$v.name.$touch()"
    ></v-text-field>
    <v-text-field
      v-model="email"
      :error-messages="emailErrors"
      label="Model"
      required
      @input="$v.email.$touch()"
      @blur="$v.email.$touch()"
    ></v-text-field>
    <v-text-field
      v-model="select"
      :error-messages="selectErrors"
      label="Trademark"
      required
      @change="$v.select.$touch()"
      @blur="$v.select.$touch()"
    ></v-text-field>
     <v-textarea
     v-model="description"
      :error-messages="descriptionErrors"
      label="description"
      required
      @change="$v.description.$touch()"
      @blur="$v.description.$touch()"
    ></v-textarea>

    <v-btn class="mr-4" :disabled="disabledButton || $v.$invalid" @click="submit();snackbar = true">submit</v-btn>
    <!-- <v-btn @click="clear">clear</v-btn> -->
  </form> 
  <div class="text-center">
    <v-snackbar
      v-model="snackbar"
      :vertical="vertical"
    >
      {{ text }}
      <v-btn
        color="indigo"
        text
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </div>
          </v-card-text>
        </v-card>
      </v-tab-item>
     
      <v-tab-item>
        <v-card flat>
          <v-card-title class="headline">An even better title</v-card-title>
          <v-card-text>
            
          </v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs-items>
  </v-card>
</template>

<script>
  import { validationMixin } from 'vuelidate'
  import { required, maxLength } from 'vuelidate/lib/validators'

  export default {
    mixins: [validationMixin],

    validations: {
      name: { required, maxLength: maxLength(90) },
      email: { required },
      select: { required },
      description: {
       required, maxLength: maxLength(300)
      },
    },

    data: () => ({
      tabs: null,
      name: 'Traje de baño amarillo',
      oldName: 'Traje de baño amarillo',
      email: 'Slim',
      select: 'Supertienda',
      description: 'El mejor traje de baño de tu vida',
      submitStatus: null,
      disabledButton: true,
       snackbar: false,
      text: 'Product Saved!',
      vertical: true,
    }),

    computed: {
      descriptionErrors () {
        const errors = []
        if (!this.$v.description.$dirty) return errors
        !this.$v.description.maxLength && errors.push('Description must be at most 300 characters long')
        !this.$v.description.required && errors.push('Description is required.')
        return errors
      },
      selectErrors () {
        const errors = []
        if (!this.$v.select.$dirty) return errors
        !this.$v.select.required && errors.push('Trademark is required')
        return errors
      },
      nameErrors () {
        const errors = []
        if (!this.$v.name.$dirty) return errors
        !this.$v.name.maxLength && errors.push('Name must be at most 90 characters long')
        !this.$v.name.required && errors.push('Name is required.')
        return errors
      },
      emailErrors () {
        const errors = []
        if (!this.$v.email.$dirty) return errors
        !this.$v.email.required && errors.push('E-mail is required')
        return errors
      },
    },
watch: {
    // whenever name changes, this function will run
    name: function (newNameValue, oldNameValue) {
      if(newNameValue == this.oldName ){
        this.disabledButton = true;
      } else
        this.disabledButton = false; 
    }
  },
    methods: {
      submit() {
      console.log('submit!')
      this.$v.$touch()
      if (this.$v.$invalid) {
        this.submitStatus = 'ERROR'
      } else {
        // do your submit logic here
        this.submitStatus = 'PENDING'
        setTimeout(() => {
          this.submitStatus = 'OK'
          this.oldName = this.name;
        this.disabledButton = true;
        }, 500)
      }
    },
      clear () {
        this.$v.$reset()
        this.name = ''
        this.email = ''
        this.select = ''
        this.description = ''
      },
    },
  }
</script>