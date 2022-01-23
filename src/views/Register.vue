<template>
  <b-row class="vh-100 vw-100 row-login">
    
    <b-col sm="7" class="d-flex justify-content-center align-items-center">
      <img src="../assets/cadastro.svg" class="img-login">
    </b-col>

    <b-col sm="5" class="d-flex justify-content-center align-items-center right-register">
      <div class="col-8">
        <h2 class="text-center mb-5 title-register">Faça o seu cadastro</h2>
        <b-form>
          <b-form-group
            label="Nome"
            label-for="name"
          >
            <b-form-input
              id="name"
              type="text"
              placeholder="José Silva"
              autocomplete="off"
              v-model.trim="$v.form.name.$model"
              :state="getValidation('name')"
            ></b-form-input>
          </b-form-group>

          <b-form-group
            label="E-mail"
            label-for="email"
          >
            <b-form-input
              id="email"
              type="email"
              placeholder="josesilva@email.com"
              autocomplete="off"
              v-model.trim="$v.form.email.$model"
              :state="getValidation('email')"
            ></b-form-input>
          </b-form-group>

          <b-form-group   
          label="Senha"        
            label-for="password"
          >
            
            <b-form-input
              id="password"
              type="password"
              placeholder="Digite sua senha"         
              v-model.trim="$v.form.password.$model"
              :state="getValidation('password')"
            ></b-form-input>
          </b-form-group>

           <b-form-group 
            label="Confirme sua Senha"          
            label-for="confirmPassword"
          >
            
            <b-form-input
              id="confirmPassword"
              type="password"
              placeholder="Confirme sua senha"         
              v-model.trim="$v.form.confirmPassword.$model"
              :state="getValidation('confirmPassword')"
            ></b-form-input>
          </b-form-group>

          <b-button
            type="button"
            variant="primary"
            block
            @click="register">
            <i class="fas fa-sign-in-alt"></i> Cadastrar
          </b-button>

          <hr>

          <b-button
            type="button"
            variant="outline-secondary"
            block
            @click="goToLogin">
            <i class="fas fa-arrow-left"></i> Voltar
          </b-button>


        </b-form>
      </div>
    </b-col>
  </b-row>
</template>

<script>
import { required, minLength, email, sameAs } from "vuelidate/lib/validators";
import UsersModel from "@/models/UsersModel";
import ToastMixin from "@/mixins/toastMixin";

export default {
  mixins: [ToastMixin],

  data() {
    return {
      form: {
        name: "",
        email: "",
        password: "",
        confirmPassword: ""
        
      }
    }
  },

  validations: {
    form: {
      name: {
        required,
        minLength: minLength(3)
      },
      email: {
        required,
        email
      },
      password: {
        required,
        minLength: minLength(6)
      },
      confirmPassword: {
        required,
        sameAsPassword: sameAs('password')
      }
    }
  },

  methods: {
    register() {
      this.$v.$touch();
      if(this.$v.$error) {
        return;
      }

      const user = new UsersModel(this.form);
      user.save();

      this.showToast("success", "Sucesso!", "Usuário cadastrado com sucesso");
      this.goToLogin();
    },

    goToLogin() {
      this.$router.push({ name: 'login' });
    },

    getValidation(field) {
      if(this.$v.form.$dirty === false) {
        return null;
      }

      return !this.$v.form[field].$error;
    }
  }
}
</script>

<style>

*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  text-decoration: none;
}

.row-login {
  margin-left: 0;
}

.right-register {
  background-color: #f2f2f2;
}

.title-register{
  font-weight: bold;
}

.img-login {
  width: 400px;
  height: 400px;
}

</style>
