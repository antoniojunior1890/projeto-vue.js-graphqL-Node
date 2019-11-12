<template>
  <div>
    <h5>
      {{ title }}
      <span class="badge badge-info">{{ items.length }}</span>
    </h5>
    <div class="card">
      <div class="card-body">
        <ul class="list-group">
          <li class="list-group-item" v-for="item in items" :key="item.id">
            <div class="row">
              <div class="col-md">{{ item.description }}</div>
              <div class="col-md text-right">
                <button class="btn btn-info" @click="deleteItem(item)">
                  <span class="fa fa-trash"></span>
                </button>
              </div>
            </div>
          </li>
        </ul>
        <br />
        <div class="input-group">
          <input
            type="text"
            class="form-control"
            v-model="item"
            placeholder="Digite o item"
            v-on:keyup.enter="additem(item)"
          />
          <div class="input-group-append">
            <button class="btn btn-info" @click="addItem(item)">
              <span class="fa fa-plus"></span>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AppItemList",
  props: ["title", "items"],
  data() {
    return {
      item: ""
    };
  },
  methods: {
    addItem(item) {
      this.$emit("addItem", item);
      this.item = "";
    },
    deleteItem(item) {
      this.$emit("deleteItem", item);
    }
  }
};
</script>

<style>
</style>