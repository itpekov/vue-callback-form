<template>
  <div v-loading="loading" class="container form-container p-5">
    <div class="row">
      <div class="col text-center">
        <p class="font-weight-bold">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit. Praesent pellentesque urna pharetra, tincidunt tortor quis, molestie lacus.
          Mauris blandit venenatis volutpat. Cras nulla metus, tristique a lacinia at, accumsan at velit.
          In convallis luctus erat et elementum. Pellentesque eleifend nulla id dolor euismod ultricies.
          Curabitur mauris neque, fringilla sed ex eu, tempus aliquet erat. Phasellus ut magna  nisl.
          Vestibulum quam neque, posuere nec diam nec, porta sagittis justo. Vivamus in metus enim.
        </p>
        <p class="font-weight-bold">
          Contact us <a href="tel:+123456789">+123456789</a>
        </p>
        <p>
          Or you can use the form below, with your contacts.
          We will call you back in short time for details.
        </p>
      </div>
    </div>

    <el-alert
      v-if="this.form.sended"
      :title="message.text"
      :type="message.type">
    </el-alert>
    <div v-else class="row pt-2">
    </div>
    <el-form :inline="true" :model="form" :rules="rules" ref="form">
      <el-form-item prop="name">
        <el-input
          placeholder="Name"
          v-model="form.name"
        ></el-input>
      </el-form-item>
      <el-form-item prop="mobile">
        <el-input
          placeholder="Phone"
          v-model="form.mobile"
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="success" :disabled="userAgreed"
          @click="submitForm('form')"
        >Submit</el-button>
      </el-form-item>
      <el-form-item prop="agreementConfirmed">
        <el-checkbox v-model="form.agreementConfirmed">
          I agree with given
          <a href="/static/agreement.pdf" target="_blank">private policy</a>
        </el-checkbox>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  props: ['description'],
  data() {
    return {
      endpoint: 'https://your-own-domain.something/backend_api_address.php',
      form: {
        name: '',
        mobile: '',
        agreementConfirmed: false,
        sended: false,
      },
      message: {
        text: '',
        type: '',
      },
      loading: false,
      rules: {
        name: [
          { required: true, message: 'Please input name', trigger: 'blur' },
          { min: 2, message: 'Length should be more than 2', trigger: 'blur' }
        ],
        mobile: [
          { required: true, message: 'Please input phone', trigger: 'blur' },
          { min: 6, message: 'Phone number length should be more than 5', trigger: 'blur' }
        ]
      }
    };
  },
  computed: {
    userAgreed() {
      return !(this.form.agreementConfirmed);
    },
  },
  methods: {
    showMessage() {
      this.$message({
        message: this.message.text,
        type: this.message.type,
      });
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.postData();
        } else { return false; }
      });
    },
    postData() {
      this.loading = true;
      const myInit = {
        method: 'POST',
        mode: 'cors',
        cache: 'no-cache',
        credentials: 'same-origin',
        headers: {
          'Content-Type': 'application/json',
        },
        redirect: 'follow',
        referrer: 'no-referrer',
        body: JSON.stringify(this.form),
      };
      fetch(this.endpoint, myInit)
      .then((response) => {
        if (response.status === 201) return response.json();
        throw new Error('Backend error');
      })
      .then(() => {
        this.message.text = 'Thank you! We will call you as soon as possible.';
        this.message.type = 'success';
        this.form.sended = true;
      })
      .catch((error) => {
        this.message.text = error;
        this.message.type = 'error';
      })
      .finally(() => {
        this.loading = false;
        this.showMessage();
      });
    },
  },
};
</script>

<style scoped>
.form-container{
  background-color: #F1EEC8;
  padding-bottom: 20px !important;
}
</style>
