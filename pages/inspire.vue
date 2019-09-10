
<template>
  <v-card>
    <v-toolbar flat prominent>
      <v-tabs slot="extension" v-model="tabs" background-color="transparent" centered>
        <v-tab v-for="n in 2" :key="n">{{ n == 1 ?'Product':'characteristic' }}</v-tab>
      </v-tabs>
    </v-toolbar>

    <v-tabs-items v-model="tabs">
      <v-tab-item>
        <v-card flat>
          <v-card-title>
            <h1>Product</h1>
            <v-img
              width="200"
              height="200"
              src="https://s3-eu-west-1.amazonaws.com/media-esp-buyviu-com/products/50fa85698a693ed73ca211ca315a20be_image_1_thumb.png"
            ></v-img>
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
                v-model="model"
                :error-messages="emailErrors"
                label="Model"
                required
                @input="$v.model.$touch()"
                @blur="$v.model.$touch()"
              ></v-text-field>
              <v-text-field
                v-model="brand"
                :error-messages="selectErrors"
                label="Trademark"
                required
                @change="$v.brand.$touch()"
                @blur="$v.brand.$touch()"
              ></v-text-field>
              <v-textarea
                v-model="description"
                :error-messages="descriptionErrors"
                label="description"
                required
                @change="$v.description.$touch()"
                @blur="$v.description.$touch()"
              ></v-textarea>

              <v-btn
                class="mr-4"
                :disabled="disabledButton || $v.$invalid"
                @click="submit();snackbar = true"
              >submit</v-btn>
              <!-- <v-btn @click="clear">clear</v-btn> -->
            </form>
            <div class="text-center">
              <v-snackbar v-model="snackbar" :vertical="vertical">
                {{ text }}
                <v-btn color="indigo" text @click="snackbar = false">Close</v-btn>
              </v-snackbar>
            </div>
          </v-card-text>
        </v-card>
      </v-tab-item>

      <v-tab-item>
        <v-card flat>
          <v-card-title class="headline">Component 2</v-card-title>
          <v-card-text>
            <form @submit.prevent="submit2">
              <v-col class="d-flex" cols="12" sm="6">
                <v-select
                  v-model="sizeValue"
                  :items="sizeCloth"
                  :error-messages="selectErrors"
                  label="Item"
                  required
                  @change="$v.sizeValue.$touch()"
                  @blur="$v.sizeValue.$touch()"
                ></v-select>
              </v-col>

              <v-col class="d-flex" cols="12" sm="6">
                <v-select :items="colorCloth" label="Standard"></v-select>
              </v-col>
              <v-btn
                class="mr-4"
                :disabled="disabledButton2 || $v.$invalid"
                @click="submit2();snackbar = true"
              >submit</v-btn>
            </form>
          </v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs-items>
  </v-card>
</template>

<script>
import { validationMixin } from "vuelidate";
import { required, maxLength } from "vuelidate/lib/validators";
import axios from 'axios';

export default {
  mixins: [validationMixin],
  validations: {
    name: { required, maxLength: maxLength(90) },
    model: { required },
    brand: { required },
    description: {
      required,
      maxLength: maxLength(300)
    },
    sizeValue:{required}
  },

  data: () => ({
    tabs: null,
    name: "",
    oldName: "",
    model: "",
    brand: "",
    description: "",
    submitStatus: null,
    disabledButton: true,
    disabledButton2: true,
    snackbar: false,
    text: "Product Saved!",
    vertical: true,
    sizeCloth: ["sm", "lg", "etc"],
    colorCloth: ["green", "yellow", "etc"],
    sizeValue: "sm",
    oldSizeValue: "sm"
  }),

  computed: {
    descriptionErrors() {
      const errors = [];
      if (!this.$v.description.$dirty) return errors;
      !this.$v.description.maxLength &&
        errors.push("Description must be at most 300 characters long");
      !this.$v.description.required && errors.push("Description is required.");
      return errors;
    },
    selectErrors() {
      const errors = [];
      if (!this.$v.brand.$dirty) return errors;
      !this.$v.brand.required && errors.push("Trademark is required");
      return errors;
    },
    nameErrors() {
      const errors = [];
      if (!this.$v.name.$dirty) return errors;
      !this.$v.name.maxLength &&
        errors.push("Name must be at most 90 characters long");
      !this.$v.name.required && errors.push("Name is required.");
      return errors;
    },
    emailErrors() {
      const errors = [];
      if (!this.$v.model.$dirty) return errors;
      !this.$v.model.required && errors.push("E-mail is required");
      return errors;
    },
    siveValueErrors() {
      const errors = [];
      if (!this.$v.sizeValue.$dirty) return errors;
      !this.$v.sizeValue.required && errors.push("Size is required");
      return errors;
    },
     async fetchSomething() {
    const ip = await this.$axios.$get('http://www.mocky.io/v2/5d6de07e30000048c38fbc9b')
    console.log(ip.product)
    this.name = ip.product.long_name
    this.oldName = ip.product.long_name
    this.model = ip.product.model
    this.description = ip.product.plain_description
    this.brand = ip.product.brand
  }
  },
  watch: {
    // whenever name changes, this function will run
    name: function(newNameValue, oldNameValue) {
      if (newNameValue == this.oldName) {
        this.disabledButton = true;
      } else this.disabledButton = false;
    },
    sizeValue: function(newSizeValue, oldSizeValue) {
      if (newSizeValue == this.oldSizeValue) {
        this.disabledButton2 = true;
      } else this.disabledButton2 = false;
    }
  },
  methods: {
    submit() {
      this.$v.$touch();
      if (this.$v.$invalid) {
        this.submitStatus = "ERROR";
      } else {
        // do your submit logic here
        this.submitStatus = "PENDING";
        setTimeout(() => {
          this.submitStatus = "OK";
          this.oldName = this.name;
          this.disabledButton = true;
        }, 500);
      }
    },
    submit2() {
      this.$v.$touch();
      if (this.$v.$invalid) {
      } else {
        // do your submit logic here
        setTimeout(() => {
          this.oldSizeValue = this.sizeValue;
          this.disabledButton2 = true;
        }, 500);
      }
    },
    clear() {
      this.$v.$reset();
      this.name = "";
      this.model = "";
      this.brand = "";
      this.description = "";
    }
  }
};
</script>