<template>
  <v-container>
    <v-card>
      <!-- Barra superior
      =======================================================================-->
      <v-toolbar dark color="primary">
        <v-toolbar-title>
          <v-icon right>fa-keyboard</v-icon> Dades de l'<span v-if="dataForm.userSex == 'dona'">usuaria</span><span v-else>usuari</span>
        </v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items>
          <v-btn dark flat @click="saveDataFormToFile()">Desar <v-icon right>fa-save</v-icon></v-btn>
        </v-toolbar-items>
      </v-toolbar>

      <!-- Formulari
      =======================================================================-->
      <v-card-text>
        <v-form ref="form">
          <!-- Sexe
          ...................................................................-->
          <v-radio-group v-model="dataForm.userSex" :mandatory="false" row>
            <v-radio label="Dona" value="dona" color="primary"></v-radio>
            <v-radio label="Home" value="home" color="primary"></v-radio>
            <v-icon v-if="dataForm.userSex == 'dona'" color="primary">fa-venus</v-icon>
            <v-icon v-else color="primary">fa-mars</v-icon>
          </v-radio-group>

          <!-- Edat
          ...................................................................-->
          <v-text-field v-model="dataForm.userAge" label="Edat" type="number"></v-text-field>

          <!-- Servei
          ...................................................................-->
          <v-select v-model="dataForm.userService"
                    :items="dataFormItems.userServiceItems" item-text="name"
                    label="Servei"></v-select>

          <!-- Ubicació
          ...................................................................-->
          <v-select v-model="dataForm.userLocation"
                    :items="dataFormItems.userLocationItems" item-text="name"
                    label="Ubicació"></v-select>

          <!-- Antecedents
          ...................................................................-->
          <v-textarea v-model="dataForm.userAntecedents" label="Antecedents" rows="3"></v-textarea>

          <!-- Estat cognitiu
          ...................................................................-->
          <v-select v-model="dataForm.userCognitiveState" :items="dataFormItems.userCognitiveStateItems" label="Estat cognitiu"></v-select>

          <!-- Transferències
          ...................................................................-->
          <v-textarea v-model="dataForm.userTransfers" label="Transferències" rows="3"></v-textarea>

          <!-- Marxa
          ...................................................................-->
          <v-textarea v-model="dataForm.userMarch" label="Marxa" rows="3"></v-textarea>

          <!-- Productes de suport / Altres AT
          ...................................................................-->
          <v-textarea v-model="dataForm.userSupportProducts" label="Productes de suport / Altres AT" rows="3"></v-textarea>

          <!-- Balanç muscular
          ...................................................................-->
          <v-textarea v-model="dataForm.userMuscularBalance" label="Balanç muscular" rows="3"></v-textarea>

          <!-- Balanç articular
          ...................................................................-->
          <v-textarea v-model="dataForm.userJointBalance" label="Balanç articular" rows="3"></v-textarea>

          <!-- Dolor/Clínica
          ...................................................................-->
          <v-textarea v-model="dataForm.userPain" label="Dolor / Clínica" rows="3"></v-textarea>

          <!-- ttm de FST
          ...................................................................-->
          <v-textarea v-model="dataForm.userTtm" label="Ttm de FST" rows="3"></v-textarea>

          <!-- Evolució
          ...................................................................-->
          <v-textarea v-model="dataForm.userEvolution" label="Evolució" rows="3"></v-textarea>

          <!-- Estat cutani
          ...................................................................-->
          <v-textarea v-model="dataForm.userCutaneousState" label="Estat cutani" rows="3"></v-textarea>

          <!-- Pautes específiques
          ...................................................................-->
          <v-textarea v-model="dataForm.userSpecificGuidelines" label="Pautes específiques" rows="3"></v-textarea>

          <!-- Contencions
          ...................................................................-->
          <v-textarea v-model="dataForm.userContentions" label="Contencions / Restriccions" rows="3"></v-textarea>

          <!-- TO
          ...................................................................-->
          <v-textarea v-model="dataForm.userTo" label="TO" rows="3"></v-textarea>

          <!-- Caigudes
          ...................................................................-->
          <v-textarea v-model="dataForm.userFalls" label="Caigudes" rows="3"></v-textarea>

          <!-- Grup de risc
          ...................................................................-->
          <v-select v-model="dataForm.userRiskGroup" :items="dataFormItems.userRiskGroupItems" label="Grup de risc"></v-select>

          <!-- Test Tinetti
          ...................................................................-->
          <v-text-field v-model="dataForm.userTinettiTest" label="Test Tinetti" type="number" :suffix="userTestsResult('tinetti')" hint="Entre 0-18 o 19-23 o 24-28"></v-text-field>

          <!-- Test Downton
          ...................................................................-->
          <v-text-field v-model="dataForm.userDowntonTest" label="Test Downton" type="number" :suffix="userTestsResult('downton')" hint="Entre 1-2 o 3-4 o 5-9"></v-text-field>

          <!-- Test Barthel
          ...................................................................-->
          <v-text-field v-model="dataForm.userBarthelTest" label="Test Barthel" type="number" :suffix="userTestsResult('barthel')" hint="Entre 0-15 o 20-35 o 40-55 o 60-95 o 100"></v-text-field>

          <!-- Adaptació
          ...................................................................-->
          <v-textarea v-model="dataForm.userObservations" label="Adaptació" rows="3"></v-textarea>

          <!-- Necessitat
          ...................................................................-->
          <v-textarea v-model="dataForm.userDetectionNeeds" label="Necessitat" rows="3"></v-textarea>
        </v-form>
      </v-card-text>

      <!-- Botons -->
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn flat color="primary" @click="dataFormToText()">Generar text <v-icon right>fa-file-signature</v-icon></v-btn>
        <v-btn flat color="primary" @click="clearForm()">Nou Formulari <v-icon right>fa-file</v-icon></v-btn>
      </v-card-actions>
    </v-card>

    <!-- Modal Generar Text -->
    <v-layout row justify-center>
      <v-dialog v-model="dialogs.dialogWithGeneratedText" scrollable persistent>
        <v-card>
          <!-- Títol -->
          <v-card-title primary-title class="headline">Text generat</v-card-title>
          <v-divider></v-divider>

          <!-- Cos -->
          <v-card-text id="generatedText">
            <span v-if="dataForm.userSex == 'dona'">Usuària</span><span v-else>Usuari</span>
            <span v-if="dataForm.userAge"> de {{dataForm.userAge}} anys.</span>
            <span v-if="dataForm.userService"> Servei de {{dataForm.userService}}.</span>
            <span v-if="dataForm.userLocation"> Ubicat al menjador {{dataForm.userLocation}}.</span>
            <span v-if="dataForm.userAntecedents"> Presenta com antecedents: {{dataForm.userAntecedents}}.</span>
            <span v-if="dataForm.userCognitiveState"> Estat cognitiu: {{dataForm.userCognitiveState}}.</span>
            <span v-if="dataForm.userTransfers"> Transferències: {{dataForm.userTransfers}}.</span>
            <span v-if="dataForm.userMarch"> Marxa: {{dataForm.userMarch}}.</span>
            <span v-if="dataForm.userSupportProducts"> Productes de suport / Altres AT: {{dataForm.userSupportProducts}}.</span>
            <span v-if="dataForm.userMuscularBalance"> Balanç muscular: {{dataForm.userMuscularBalance}}.</span>
            <span v-if="dataForm.userJointBalance"> Balanç articular: {{dataForm.userJointBalance}}.</span>
            <span v-if="dataForm.userPain"> Dolor / Cínica: {{dataForm.userPain}}.</span>
            <span v-if="dataForm.userTtm"> Ttm de FST: {{dataForm.userTtm}}.</span>
            <span v-if="dataForm.userEvolution"> Evolució del ttm: {{dataForm.userEvolution}}.</span>
            <span v-if="dataForm.userCutaneousState"> Estat cutani: {{dataForm.userCutaneousState}}.</span>
            <span v-if="dataForm.userSpecificGuidelines"> Pautes específiques: {{dataForm.userSpecificGuidelines}}.</span>
            <span v-if="dataForm.userContentions"> Contencions / Restriccions: {{dataForm.userContentions}}.</span>
            <span v-if="dataForm.userTo"> TO: {{dataForm.userTo}}.</span>
            <span v-if="dataForm.userFalls"> Caigudes: {{dataForm.userFalls}}. </span>
            <span v-if="dataForm.userRiskGroup"> Tests actualitzats: Grup de risc {{dataForm.userRiskGroup}},</span>
            <span v-if="dataForm.userTinettiTest"> Tinetti {{dataForm.userTinettiTest}} ({{userTestsResult('tinetti')}}),</span>
            <span v-if="dataForm.userDowntonTest"> Downton {{dataForm.userDowntonTest}} ({{userTestsResult('downton')}})</span>
            <span v-if="dataForm.userBarthelTest"> i Barthel {{dataForm.userBarthelTest}} ({{userTestsResult('barthel')}}).</span>
            <span v-if="dataForm.userObservations"> Observacions sobre la seva adaptació al centre: {{dataForm.userObservations}}.</span>
            <span v-if="dataForm.userDetectionNeeds"> Es detecta la necessitat de {{dataForm.userDetectionNeeds}}.</span>
          </v-card-text>
          <v-divider></v-divider>

          <!-- Botons -->
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn flat color="primary" @click.native="dialogs.dialogWithGeneratedText = false">Tancar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-layout>
  </v-container>
</template>

<script>
  const fs = require("fs");
  const {app, clipboard, dialog} = require("electron").remote;
  export default {
    name: "componentForm",
    created () {
      this.readFromImaginaryDatabase();
      this.readFromFileTemp();
      
      this.$eventBus.$on("saveDataPaiTemp", () => {
        this.saveDataFormToFileTemp();
      });
      this.$eventBus.$on("dbChangeInfoToPai", () => {
        this.readFromImaginaryDatabase();
      });
      this.$eventBus.$on("tempChangeInfoToPai", () => {
        this.readFromFileTemp();
      });
    },
    beforeDestroy() {
      this.$eventBus.$off("saveDataPaiTemp");
      this.$eventBus.$off("dbChangeInfoToPai");
      this.$eventBus.$off("tempChangeInfoToPai");
    },
    data: () => ({
      dialogs: {
        dialogWithGeneratedText: false,
      },
      // Dades del formulari.
      dataForm: {
        userSex: "dona",
        userAge: "",
        userService: null,
        userLocation: null,
        userAntecedents: "",
        userCognitiveState: null,
        userTransfers: "",
        userMarch: "",
        userSupportProducts: "",
        userMuscularBalance: "",
        userJointBalance: "",
        userPain: "",
        userTtm: "",
        userEvolution: "",
        userCutaneousState: "",
        userSpecificGuidelines: "",
        userContentions: "",
        userTo: "",
        userFalls: "",
        userRiskGroup: null,
        userTinettiTest: "",
        userDowntonTest: "",
        userBarthelTest: "",
        userObservations: "",
        userDetectionNeeds: "",
      },
      dataFormItems: {
        userServiceItems: [],
        userLocationItems: [],
        userCognitiveStateItems: [
          "Sense afecció",
          "Lleu",
          "Moderat",
          "Greu"
        ],
        userRiskGroupItems: [
          "A",
          "B",
          "C",
          "D",
          "E"
        ],
      },
      // Desar fitxer.
      dataStore: {
        dataType: "fisiopaiForm",
        data: []
      },
    }),
    methods: {
      /* FORMULARI ************************************************************/

      // Tests -----------------------------------------------------------------
      // Permet controlar els valors dels tests.
      // -----------------------------------------------------------------------
      userTestsResult(type) {
        let message;
        let num;

        if (type == "tinetti") {
          num = this.dataForm.userTinettiTest;

          if (num != "" && num >= 0 && num <= 18) {
            message = "alt risc de caiguda";
          } else if (num >= 19 && num <= 23) {
            message = "risc moderat de caiguda";
          } else if (num >= 24 && num <= 28) {
            message = "mínim risc de caiguda";
          } else {
            message = "";
          }
        }
        
        if (type == "downton") {
          num = this.dataForm.userDowntonTest;

          if (num == 1 || num == 2) {
            message = "risc baix de caiguda";
          } else if (num == 3 || num == 4) {
            message = "risc moderat de caiguda";
          } else if (num >= 5 && num <= 9) {
            message = "alt risc de caiguda";
          } else {
            message = "";
          }
        }
        
        if (type == "barthel") {
          num = this.dataForm.userBarthelTest;

          if (num != "" && num >= 0 && num <= 15) {
            message = "dependència total";
          } else if (num >= 20 && num <= 35) {
            message = "dependència greu";
          } else if (num >= 40 && num <= 55) {
            message = "dependència moderada";
          } else if (num >= 60 && num <= 95) {
            message = "dependència lleu";
          } else if (num == 100) {
            message = "independent";
          } else {
            message = "";
          }
        }

        return message;
      },

      // Generar text ----------------------------------------------------------
      // Permet generar els text amb les dades del formulari i copiar aquest,
      // de forma automàtica, en el portapepers.
      // -----------------------------------------------------------------------
      dataFormToText() {
        let text = document.querySelector("#generatedText").textContent;

        // Obrir el "modal" amb el text generat.
        this.dialogs.dialogWithGeneratedText = true;

        // Copiar text de forma automàtica.
        clipboard.writeText(text);
      },

      // Netejar ---------------------------------------------------------------
      // Netejar el formulari per poder-ne començar un de nou.
      // -----------------------------------------------------------------------
      clearForm() {
        this.$refs.form.reset();
        this.dataForm.userSex = "dona";
        this.dataForm         = this.dataForm;
        window.scrollTo(0,0); // Enviar al principi de la pàgina.
      },

      /* DESAR ****************************************************************/

      // Fitxer ----------------------------------------------------------------
      // Generar un fitxer JSON amb totes les dades per poder-les importar en un
      // altre moment.
      // -----------------------------------------------------------------------
      saveDataFormToFile() {
        // Mostrar informació sobre l'acció realitzada (objecte).
        let info = {};
        
        // Guardar dades del formulari en array preparat per a aquestes.
        this.dataStore.data.push(this.dataForm);

        // Convertir a JSON.
        let content = JSON.stringify(this.dataStore);

        // Definir opcions finestra per guardar fitxer.
        let options = {
          title: "Desar dades",
          // Ruta a directori per defecte + nom per defecte (modificable).
          defaultPath: app.getPath("documents") + "/fisiopai.json",
          filters: [{
            name: "fisioPAI",    // Nom del tipus de fitxer (desplegable).
            extensions: ["json"] // Extensions dels fitxers a visualitzar.
          }]
        }

        // Obrir finestra per guardar dades en fitxer.
        dialog.showSaveDialog(null, options, (filename) => {
          try {
            fs.writeFileSync(filename, content, "utf-8");

            // Buidem "data".
            this.dataStore.data = [];

          } catch(e) {
            info = {
              color: "amber darken-4",
              text: "No s'ha desat el fitxer."
            }
            this.$eventBus.$emit("showSnackbar", info);
          }
        });
      },

      /* BASE DE DADES ********************************************************/

      // Lectura de la BD ------------------------------------------------------
      // Llegir el JSON per extreure les dades. En el cas de que no existeixi el
      // fitxer, aquest es generarà de forma automàtica.
      // -----------------------------------------------------------------------
      readFromImaginaryDatabase() {
        let filepath = app.getPath("userData");
        let filename = "fisiopai.config.json";
        let content  = '[{"userServices":{"data":[]},"userLocations":{"data":[]}}]';

        try {
          content = JSON.parse(fs.readFileSync(filepath+"/"+filename, "utf-8"));

          this.dataFormItems.userServiceItems  = []; // Buidar.
          this.dataFormItems.userLocationItems = [];

          this.dataFormItems.userServiceItems  = content[0].userServices.data;
          this.dataFormItems.userLocationItems = content[0].userLocations.data;
        } catch(e) {
          // alert("No existeix cap base de dades.\n"+e);

          try {
            fs.writeFileSync(filepath+"/"+filename, content, "utf-8");
          } catch(e) {
            alert("No s'ha creat la base de dades.\n"+e);
          }
        }
      },
      
      /* FITXER (temporal) ****************************************************/

      // Fitxer temp -----------------------------------------------------------
      // Generar un fitxer JSON en el que emmagatzemar les dades en el cas de
      // que es canvie de pàgina. Mitjançant aquest s'evita la pèrdua de dades.
      // -----------------------------------------------------------------------
      saveDataFormToFileTemp() {
        // Guardar dades del formulari en array preparat per a aquestes.
        this.dataStore.data.push(this.dataForm);

        // Convertir a JSON.
        let content = JSON.stringify(this.dataStore.data);

        // Desar dades en fitxer temporal.
        let filepath = app.getPath("userData");
        let filename = "fisiopai.temp.json";
        try {
          fs.writeFileSync(filepath+"/"+filename, content, "utf-8");

          // Buidem "data".
          this.dataStore.data = [];
          
        } catch(e) {
          alert("S'han perdut les dades del formulari.\n"+e);
        }
      },

      // Llegir fitxer temp ----------------------------------------------------
      // Llegir el fitxer temporal per recuperar les dades. Si no existeix el
      // fitxer se'n generarà un de forma automàtica.
      // -----------------------------------------------------------------------
      readFromFileTemp() {
        let filepath = app.getPath("userData");
        let filename = "fisiopai.temp.json";
        let content  = '[{"userSex":"dona","userAge":"","userService":null,"userLocation":null,"userAntecedents":"","userCognitiveState":null,"userTransfers":"","userMarch":"","userSupportProducts":"","userMuscularBalance":"","userJointBalance":"","userPain":"","userTtm":"","userEvolution":"","userCutaneousState":"","userSpecificGuidelines":"","userContentions":"","userTo":"","userFalls":"","userRiskGroup":null,"userTinettiTest":"","userDowntonTest":"","userBarthelTest":"","userObservations":"","userDetectionNeeds":""}]';

        try {
          content = JSON.parse(fs.readFileSync(filepath+"/"+filename, "utf-8"));

          this.dataForm  = []; // Buidar.

          this.dataForm  = content[0];
        } catch(e) {
          // alert("No existeix la taula per poder emmagatzemar les dades del formulari temporalment.\n"+e);

          try {
            fs.writeFileSync(filepath+"/"+filename, content, "utf-8");
          } catch(e) {
            alert("No s'ha creat la taula per poder emmagatzemar les dades del formulari temporalment.\n"+e);
          }
        }
      }
    }
  }
</script>

<style>

</style>
