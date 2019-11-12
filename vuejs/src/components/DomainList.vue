<template>
  <div>
    <div id="main">
      <div class="container">
        <div class="row">
          <div class="col-md">
            <AppItemList
              title="Prefixos"
              :items="prefixes"
              @addItem="addPrefix"
              @deleteItem="deletePrefix"
            />
          </div>
          <div class="col-md">
            <AppItemList
              title="Sufixos"
              :items="sufixes"
              @addItem="addSufix"
              @deleteItem="deleteSufix"
            />
          </div>
        </div>
        <br />
        <h5>
          Domínios
          <span class="badge badge-info">{{ domains.length }}</span>
        </h5>
        <div class="card">
          <div class="card-body">
            <ul class="list-group">
              <li class="list-group-item" v-for="domain in domains" :key="domain.name">
                <div class="row">
                  <div class="col-md">{{ domain.name }}</div>
                  <div class="col-md text-right">
                    <a class="btn btn-info" :href="domain.checkout" target="_blank">
                      <span class="fa fa-shopping-cart"></span>
                    </a>
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios/dist/axios";
import AppItemList from "./AppItemList";

export default {
  name: "app",
  components: {
    AppItemList
  },
  data() {
    return {
      prefixes: [],
      sufixes: []
    };
  },
  methods: {
    addPrefix(prefix) {
      axios({
        url: "http://localhost:4000",
        method: "post",
        data: {
          query: `
            mutation ($item: ItemInput) {
              newPrefix: saveItem(item: $item) {
                id
                type
                description
              }
            }
          `,
          variables: {
            item: {
              type: "prefix",
              description: prefix
            }
          }
        }
      }).then(response => {
        const query = response.data;
        const newPrefix = query.data.newPrefix;
        this.prefixes.push(newPrefix.description);
      });
    },
    addSufix(sufix) {
      this.sufixes.push(sufix);
    },
    generate() {
      this.domains = [];
      for (const prefix of this.prefixes) {
        for (const sufix of this.sufixes) {
          this.domains.push(prefix + sufix);
        }
      }
    },
    deletePrefix(prefix) {
      this.prefixes.splice(this.prefixes.indexOf(prefix), 1);
    },
    deleteSufix(sufix) {
      this.sufixes.splice(this.sufixes.indexOf(sufix), 1);
    },
    getPrefixes() {
      axios({
        url: "http://localhost:4000",
        method: "post",
        data: {
          query: `
          {
            prefixes: items (type: "prefix") {
              id
              type
              description
            }
          }
        `
        }
      }).then(response => {
        const query = response.data;
        this.prefixes = query.data.prefixes;
        console.log(query.data);
      });
    },
    getSufixes() {
      axios({
        url: "http://localhost:4000",
        method: "post",
        data: {
          query: `
          {
            sufixes: items (type: "sufix") {
              description
            }
          }
        `
        }
      }).then(response => {
        const query = response.data;
        this.sufixes = query.data.sufixes;
      });
    }
  },
  computed: {
    domains() {
      console.log("gerando domains..");
      const domains = [];
      for (const prefix of this.prefixes) {
        for (const sufix of this.sufixes) {
          const name = prefix.description + sufix.description;
          const url = name.toLowerCase();
          const checkout = `https://checkout.hostgator.com.br/?a=add&sld=${url}&tld=.com.br`;
          domains.push({
            name,
            checkout
          });
        }
      }
      return domains;
    }
  },
  created() {
    this.getPrefixes();
    this.getSufixes();
  }
};
</script>

<style>
</style>
