<template>
  <v-app>
    <v-container>
      <v-layout row class="text-xs-center">
        <v-flex xs4 sm6>
          <v-container
            style="position: relative; left: 50%"
            class="justify-center"
          >
            <v-card flat>
              <v-card-title primary-title class="justify-center">
                <h4>Registro de usuarios</h4>
              </v-card-title>
              <v-form
                class="pa-3 pt-3"
                lazy-validation
                color="#ccf2f4"
                v-model="valid"
                azy-validation
                ref="formRegistro"
              >
                <v-row>
                  <v-col cols="12" sm="6">
                    <v-select
                      :rules="rules.required"
                      required
                      :items="tipos_id"
                      label="Tipo de identificación"
                      v-model="usuario.id_tipo_id"
                      item-text="nombre"
                      item-value="id_tipo_id"
                    ></v-select>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      name="user"
                      label="Nro de Identificación"
                      :rules="rules.required"
                      required
                      v-model="usuario.id_usuario"
                    >
                    </v-text-field>
                  </v-col>
                </v-row>
                <div v-if="perfil.rol != 0">
                  <v-col cols="12" sm="12">
                    <v-select
                      :rules="rules.required"
                      required
                      :items="tipos_rol"
                      label="Tipo de rol"
                      v-model="usuario.id_rol"
                      item-text="nombre"
                      item-value="id_rol"
                    ></v-select>
                  </v-col>
                </div>

                <v-row>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      name="user"
                      label="Nombres"
                      :rules="rules.required"
                      required
                      v-model="usuario.nombres"
                    >
                    </v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6">
                    <v-text-field
                      name="user"
                      label="Apellidos"
                      :rules="rules.required"
                      required
                      v-model="usuario.apellidos"
                    >
                    </v-text-field>
                  </v-col>
                </v-row>
                <v-text-field
                  name="user"
                  label="Correo"
                  required
                  :rules="emailRules"
                  v-model="usuario.correo"
                >
                </v-text-field>
                <v-text-field
                  name="user"
                  label="Celular"
                  :rules="rules.required"
                  required
                  v-model="usuario.celular"
                >
                </v-text-field>

                <v-row>
                  <v-col cols="12" sm="">
                    <v-text-field
                      name="password"
                      label="Contraseña"
                      :rules="rules.required"
                      required
                      v-model="usuario.contrasenia"
                      :append-icon="show1 ? 'mdi-eye' : 'mdi-eye-off'"
                      :type="show1 ? 'text' : 'password'"
                      counter
                      @click:append="show1 = !show1"
                    ></v-text-field>
                  </v-col>
                </v-row>

                <v-card-actions class="justify-center">
                  <v-btn
                    primary
                    large
                    block
                    class="white--text"
                    color="#26bb8f"
                    @click="agregarUsuario()"
                  >
                    Registrar</v-btn
                  >
                </v-card-actions>
              </v-form>
            </v-card>
          </v-container>
        </v-flex>
      </v-layout>
    </v-container>
  </v-app>
</template>


<script>
const url_tipos_id = "http://localhost:3001/tipos_id/";
const url_tipos_rol = "http://localhost:3001/roles/";
const url_usuarios = "http://localhost:3001/usuarios/";
export default {
  beforeMount() {
    this.loadUser();
    this.getTipos_id();
    this.getTipos_Rol();
  },
  data: () => ({
    show1: false,
    valid: true,
    tipos_id: [],
    usuario: [],
    tipos_rol: [],
    Pass: {},
    perfil: {},

    rules: {
      required: [(v) => !!v || "El campo es obligatorio"],
    },
    emailRules: [
      (v) => !!v || "El correo es obligatorio",
      (v) => /.+@.+\..+/.test(v) || "El correo no es valido",
    ],
  }),
  methods: {
    loadUser() {
      let stringUser = localStorage.getItem("user-in");
      this.perfil = JSON.parse(stringUser);
      console.log(this.perfil.rol);
    },
    async getTipos_id() {
      try {
        let response = await this.$axios.get(url_tipos_id);
        this.tipos_id = response.data.content;
      } catch (error) {
        this.tipos_id = [];
        console.error(error);
      }
    },
    async getTipos_Rol() {
      try {
        let response = await this.$axios.get(url_tipos_rol);
        this.tipos_rol = response.data.content;
      } catch (error) {
        this.tipos_rol = [];
        console.error(error);
      }
    },

    async agregarUsuario() {
      if (this.perfil.rol == 0) {
        this.usuario.id_rol = "1";
      }
      if (this.$refs.formRegistro.validate() || this.perfil != 0) {
        let user = Object.assign({}, this.usuario);
        let response = await this.$axios.post(url_usuarios, user);
        if (response.data.ok == true) {
          this.$swal.fire({
            type: "success",
            title: "Operación exitosa.",
            text: "El usuario se agregó correctamente.",
          });
          this.usuario = "";
          if (this.perfil.rol == 0) {
            this.$router.push("/");
          } else {
            this.$router.push("adminHome");
          }
        } else {
          this.$swal.fire({
            type: "error",
            title: "Error al crear el usuario",
            text: response.data.message,
          });
        }
      } else {
        this.$swal.fire({
          type: "warning",
          title: "Formulario incompleto.",
          text: "Hay campos que deben ser diligenciados.",
        });
      }
    },
  },
};
</script>

<style>
</style>