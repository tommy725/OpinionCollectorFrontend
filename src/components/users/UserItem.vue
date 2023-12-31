<template>
  <div v-if="prepareData" class="container border m-2">
    <div class="row">
      <div class="col-4" :class="{ invalid: !profilePictureUrl.isValid }">
        <img :src="img" class="img-fluid rounded m-3" alt="Responsive image">
        <label v-if="!profilePictureUrl.isValid">URL must not be empty.</label>
        <input v-if="isEditable"
          type="text"
          class="form-control m-3"
          placeholder="URL"
          v-model.trim="profilePictureUrl.val"
          @blur="clearValidity('profilePictureUrl')"
          />
      </div>
      <div class="d-flex col align-items-center">
        <div class="row">
          <div class="group-info" :class="{ invalid: !firstName.isValid }">
            <label>First Name:</label>
            <p v-if="!isEditable"> {{ first }}</p>
            <label v-if="!firstName.isValid">First Name must not be empty.</label>
            <input v-if="isEditable"
              type="text"
              class="form-control m-2"
              placeholder="First Name"
              v-model.trim="firstName.val"
              @blur="clearValidity('firstName')"
              />
          </div>
          <div class="group-info" :class="{ invalid: !lastName.isValid }">
            <label>Last Name:</label>
            <p v-if="!isEditable">{{ last }}</p>
            <label v-if="!lastName.isValid">Last Name must not be empty.</label>
            <input v-if="isEditable"
              type="text"
              class="form-control m-2"
              placeholder="Last Name"
              v-model.trim="lastName.val"
              @blur="clearValidity('lastName')"
              />
          </div>
          <div class="group-info" :class="{ invalid: !emailNew.isValid }">
            <label>Email:</label>
            <p v-if="!isEditable">{{ email }}</p>
            <label v-if="!emailNew.isValid">Email must not be empty and email format.</label>
            <input v-if="isEditable"
              type="email"
              class="form-control m-2"
              placeholder="Email"
              v-model.trim="emailNew.val"
              @blur="clearValidity('emailNew')"
              />
          </div>
          <div class="group-info">
            <label v-if="isEditable && editPassword">Password:</label>
            <input v-if="isEditable && editPassword"
              type="password"
              class="form-control m-2"
              placeholder="Password"
              v-model.trim="password"
              />
              <button
              v-if="editToggle && !editPassword"
              type="button"
              class="btn btn-outline-dark m-2"
              @click="passwordEdit()"
              >
              Change Password
            </button>
          </div>
          <div class="group-info">
            <label>Admin Status:</label>
            <p v-if="!isEditable">{{ admin }}</p>
          </div>
          <div v-if="isEditable">
            <div class="radio m-4">
              <div class="form-check">
                <label class="form-check-label">
                    Admin
                </label>
                <input
                  class="form-check-input"
                  type="radio"
                  value="true"
                  v-model="adminStatus"
                  />
                </div>
                <div class="form-check">
                  <label class="form-check-label">
                    User
                  </label>
                <input
                  class="form-check-input"
                  type="radio"
                  value="false"
                  v-model="adminStatus"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <button
      type="button"
      class="btn btn-outline-dark m-2"
      @click="editUser()
      ">
      {{ buttonText }}
    </button>
    <button v-if="isEditable"
      type="button"
      class="btn btn-outline-dark m-2"
      @click="submitForm()
      ">
      Save Edit
    </button>
  </div>
</template>

<script>
import { PutRequest } from "@/communication/Network.ts";
import {SweetAlert} from "@/communication/SweetAlerts.ts";

export default {
  props: ['first', 'last', 'email', 'admin', 'img', 'userId'],
  emits: ['update:first', 'update:last', 'update:email', 'update:admin','update:img'],
  data() {
    return {
      firstName: {
        val: '',
        isValid: true,
      },
      lastName: {
        val: '',
        isValid: true,
      },
      emailNew: {
        val: '',
        isValid: true,
      },
      profilePictureUrl: {
        val: '',
        isValid: true,
      },
      editToggle: false,
      password: '',
      adminStatus: false,
      editPassword: false,
      formIsValid: true,
      prepareData: false,
    }
  },
  methods: {
    editUser() {
      this.editToggle = !this.editToggle;
    },
    passwordEdit() {
      this.editPassword = true;
    },
    clearValidity(input) {
      this[input].isValid = true;
    },
    saveUserData() {
      const userData = {
        email: this.emailNew.val,
        firstName: this.firstName.val,
        isAdmin: this.adminStatus,
        lastName: this.lastName.val,
        password: this.password,
        pictureUrl: this.profilePictureUrl.val,
      };
      if (userData.password.length === 0) {
        delete userData.password;
      }
      PutRequest.userEdit(this.$props.userId, userData).then(() => {
        SweetAlert.success(this.$swal, "Successfully edited user").then(() => {
          this.$router.push( { name: 'UsersPanel' } ).then(() => { this.$router.go() });
          this.editToggle = !this.editToggle;
        });
      }).catch(() => {
          SweetAlert.error(this.$swal, "Something went wrong!");
      })
    },
    validateForm() {
      this.formIsValid = true;
      if (this.firstName.val === '') {
        this.firstName.isValid = false;
        this.formIsValid = false;
      }
      if (this.lastName.val === '') {
        this.lastName.isValid = false;
        this.formIsValid = false;
      }
      if (this.emailNew.val === '' || !this.emailNew.val.includes('@')) {
        this.emailNew.isValid = false;
        this.formIsValid = false;
      }
      if (this.profilePictureUrl.val === '') {
        this.profilePictureUrl.isValid = false;
        this.formIsValid = false;
      }
    },
    submitForm() {
      this.validateForm();

      if (!this.formIsValid) {
        return;
      }

      this.saveUserData();
    },
  },
  computed: {
    isAdmin() {
      return this.user.isAdmin ? 'Active' : 'Disabled';
    },
    isEditable() {
      return this.editToggle;
    },
    buttonText() {
      return this.editToggle ? 'Back' : 'Edit User';
    }
  },
  mounted() {
    this.firstName.val = this.$props.first;
    this.lastName.val = this.$props.last;
    this.emailNew.val = this.$props.email;
    this.profilePictureUrl.val = this.$props.img;
    this.adminStatus = this.$props.admin;
    this.prepareData = true;
  }
}
</script>

<style scoped>
.group-info {
  display: flex;
  justify-content: center;
  gap: 10px;
}

.group-info label {
  font-weight: bold;
}

.radio {
  display: flex;
  justify-content: space-around;
}

.invalid label {
  color: red;
}

.invalid input {
  border: 1px solid red;
}
</style>