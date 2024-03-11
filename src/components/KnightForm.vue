<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-12">
        <div class="card-header">
          Add Knight
        </div>
        <div class="card-body">
          <form id="knight-form" @submit.prevent="save">
            <div class="form-group row" v-for="(input, index) in formInputs" :key="index">
              <label class="col-md-2 col-form-label" :for="input.id">{{ input.label }}:</label>
              <div class="col-md-4">
                <input :type="input.type" :id="input.id" v-model="newKnight[input.model]" :placeholder="input.placeholder">
              </div>
            </div>
            <div class="form-group row">
              <label class="col-md-2 col-form-label" for="keyAttribute">Key Attribute:</label>
              <div class="col-md-4">
                <select id="keyAttribute" v-model="newKnight.keyAttribute">
                  <option v-for="(attribute, index) in attributes" :value="attribute" :key="index">{{ attribute }}</option>
                </select>
              </div>
            </div>
            <div class="weapons-container row">
              <div class="col-md-12" v-for="(weapon, index) in newKnight.weapons" :key="index">
                <div class="input-container weapon row">
                  <div class="col-md-4">
                    <label for="weapon-name">Weapon Name:</label>
                    <input type="text" v-model="weapon.name" placeholder="Enter weapon's name">
                  </div>
                  <div class="col-md-4">
                    <label for="weapon-mod">Mod:</label>
                    <input type="number" v-model="weapon.mod">
                  </div>
                  <div class="col-md-3">
                    <label for="weapon-attr">Attribute:</label>
                    <select v-model="weapon.attr">
                      <option v-for="(attribute, index) in attributes" :value="attribute" :key="index">{{ attribute }}</option>
                    </select>
                  </div>
                  <div class="col-md-10">
                    <button type="button" class="btn btn-danger col-md-2" @click="removeWeapon(index)">Remove</button>
                  </div>
                </div>
              </div>
            </div>
            <div class="button-container row">
              <div class="col-md-3">
                <button type="button" class="btn btn-info" @click="addWeapon">Add Weapon</button>
              </div>
              <div class="col-md-4">
                <button type="submit" class="btn btn-primary">Save</button>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
    <div class="row justify-content-center">
      <div class="col-md-12">
          <div class="card-header">
            Knights List
          </div>
          <div class="card-body">
            <table class="table table-dark">
              <thead>
                <tr>
                  <th scope="col">Nome</th>
                  <th scope="col">Idade</th>
                  <th scope="col">Armas</th>
                  <th scope="col">Atributo</th>
                  <th scope="col">Ataque</th>
                  <th scope="col">Exp</th>
                  <th></th>
                  <th></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(knight, index) in resultknights" :key="index">
                  <td>{{ knight.name }}</td>
                  <td>{{ calculateAge(knight.birthday) }}</td>
                  <td>{{ knight.weapons.length }}</td>
                  <td>{{ knight.keyAttribute }}</td>
                  <td>{{ knight.attack }}</td>
                  <td>{{ knight.experience }}</td>
                  <td>
                    <button type="button" class="btn btn-warning" @click="edit(knight)">Edit</button>
                  </td>
                  <td>
                    <button type="button" class="btn btn-danger" @click="remove(knight)">Delete</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
      <div class="row justify-content-center">
        <div class="col-md-12">
          <div class="card-header">
            Heroes List
          </div>
          <div class="card-body">
            <table class="table table-dark">
              <thead>
                <tr>
                  <th scope="col">Nome</th>
                  <th scope="col">Idade</th>
                  <th scope="col">Armas</th>
                  <th scope="col">Atributo</th>
                  <th scope="col">Ataque</th>
                  <th scope="col">Exp</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(heroes, index) in resultHeroes" :key="index">
                  <td>{{ heroes.name }}</td>
                  <td>{{ calculateAge(heroes.birthday) }}</td>
                  <td>{{ heroes.weapons.length }}</td>
                  <td>{{ heroes.keyAttribute }}</td>
                  <td>{{ heroes.attack }}</td>
                  <td>{{ heroes.experience }}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "KnightForm",
  data() {
    return {
      resultHeroes:{},
      resultknights: {},
      newKnight: {
        name: '',
        nickname: '',
        birthday: '',
        weapons: [{
          name: '',
          mod: 0,
          attr: '',
          equipped: true
        }],
        attributes: {
          strength: 0,
          dexterity: 0,
          constitution: 0,
          intelligence: 0,
          wisdom: 0,
          charisma: 0,
        },
        keyAttribute: ''
      },
      newWeapon: {
        name: '',
        mod: 0,
        attr: '',
        equipped: false
      },
      formInputs: [
        { id: "name", label: "Name", type: "text", model: "name", placeholder: "Enter knight's name" },
        { id: "nickname", label: "Nickname", type: "text", model: "nickname", placeholder: "Enter knight's nickname" },
        { id: "birthday", label: "Birthday", type: "date", model: "birthday", placeholder: "Enter knight's birthday" },
        { id: "strength", label: "Strength", type: "number", model: "attributes.strength" },
        { id: "dexterity", label: "Dexterity", type: "number", model: "attributes.dexterity" },
        { id: "constitution", label: "Constitution", type: "number", model: "attributes.constitution" },
        { id: "intelligence", label: "Intelligence", type: "number", model: "attributes.intelligence" },
        { id: "wisdom", label: "Wisdom", type: "number", model: "attributes.wisdom" },
        { id: "charisma", label: "Charisma", type: "number", model: "attributes.charisma" },
      ],
      attributes: ['Strength', 'Dexterity', 'Constitution', 'Intelligence', 'Wisdom', 'Charisma']
    }
  },
  created() {
    this.loadKnights();
    this.loadHeroes();
  },
  methods: {
    addWeapon() {
      this.newKnight.weapons.push({ ...this.newWeapon });
      this.newWeapon.name = '';
      this.newWeapon.mod = 0;
      this.newWeapon.attr = '';
      this.newWeapon.equipped = false;
    },
    calculateAge(birthday) {
      const today = new Date();
      const birthDate = new Date(birthday);
      let age = today.getFullYear() - birthDate.getFullYear();
      const monthDiff = today.getMonth() - birthDate.getMonth();

      if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birthDate.getDate())) {
        age--;
      }

      return age;
    },
    removeWeapon(index) {
      this.newKnight.weapons.splice(index, 1);
    },
    async loadKnights() {
      var page = "http://localhost:3000/knights";
      await axios.get(page).then(({ data }) => {
        this.resultknights = data;
      })
    },
    async loadHeroes() {
      var page = "http://localhost:3000/knights?filter=heroes";
      await axios.get(page).then(({ data }) => {
        this.resultHeroes = data;
      })
    },
    save() {
      if (!this.newKnight._id) {
        this.createKnight();
      } else {
        this.updateKnight();
      }
    },
    async createKnight() {
      await axios.post("http://localhost:3000/knights", this.newKnight)
        .then(({ data }) => {
          this.newKnight = {
            name: '',
            nickname: '',
            birthday: '',
            weapons: [{
              name: '',
              mod: 0,
              attr: '',
              equipped: true
            }],
            attributes: {
              strength: 0,
              dexterity: 0,
              constitution: 0,
              intelligence: 0,
              wisdom: 0,
              charisma: 0,
            },
            keyAttribute: ''
          };

          this.loadKnights();
        })
    },
    edit(knight) {
      this.newKnight = knight;
    },
    async updateKnight() {
      var url = 'http://localhost:3000/knights/' + this.newKnight._id;
      await axios.put(url, this.newKnight).then(({ data }) => {
        this.newKnight = {
          name: '',
          nickname: '',
          birthday: '',
          weapons: [{
            name: '',
            mod: 0,
            attr: '',
            equipped: true
          }],
          attributes: {
            strength: 0,
            dexterity: 0,
            constitution: 0,
            intelligence: 0,
            wisdom: 0,
            charisma: 0,
          },
          keyAttribute: ''
        };

        this.loadKnights();
      })
    },
    async remove(knight) {
      var url = 'http://localhost:3000/knights/' + knight._id;
      await axios.delete(url);
      this.loadKnights();
      this.loadHeroes();
    }
  }
}
</script>

<style scoped>
.input-container {
  margin-bottom: 15px;
}
label {
  display: block;
  margin-bottom: 5px;
}
input, select, button {
  padding: 8px;
  border: 1px solid;
  border-radius: 5px;
  margin-top: 3px;
}
button {
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
div 
.card-header{
  background-color:darkgray;
  border-radius: 5px;
}
</style>
