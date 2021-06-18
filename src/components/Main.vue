<template>
  <div>
    <Header/>
    <div class="search">
      <div class="pad-15-hor pad-15-ver search-parent">
        <div class="search-bar" >
          <b-form-input
            @input="search_text()"
            v-model="search.text"
            type="text"
            placeholder="Search by Name"
          ></b-form-input>
          <span class="search-icon">
            <i class="fas fa-search"></i>
          </span>
        </div>
        <div>
          <span class="bold">Cerca le tue bustine!</span>
        </div>
        <!--<div>
          <b-form-select @input="sort()" v-model="search.filter" :options="options"/>
        </div>-->
      </div>
    </div>

    <div class="container-fluid">
      <div class="row">
        <div class="col-md-6 pad-15-ver card" v-for="gioco in giocos_data" :key="gioco.id_bgg">
          <div
            class="card-inner"
            @mouseover="show_hover(true,gioco.id_bgg)"
            @mouseout="show_hover(false,0)"
          >
            <!--<img class="card-img" :src="gioco.imageURL">-->

            <div :class="{'card-hover':1}" v-show="hover_flag && active_id == gioco.id_bgg">
              <h5>{{gioco.nome}}</h5>
              <p>{{gioco.versione}}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "@/components/Header.vue";

import axios from "axios";

export default {
  name: "Main",
  mounted() {
    this.hover_flag = false;

    var inside = this;

    axios
      .get("http://localhost:5000/get_all_sleeves")
      .then(function(response) {
        console.log(response);

        inside.bustine_gioco = response.data.result;

        /*response.data.result.map(function(gioco) {
          inside.likes.count += gioco.likes;
        });*/

        /*inside.bustine_gioco = inside.bustine_gioco.map(function(
          gioco
        ) {
          gioco.active_like = false;
          return gioco;
        });*/
        inside.giocos_data = response.data.result;
      })
      .catch(function(error) {
        console.log(error);
      });
  },
  data() {
    return {
      hover_flag: false,
      bustine_gioco: [],
      giocos_data: [],
      active_id: 0
      /*options: [
        { value: null, text: "Sort By" },
        { value: "a", text: "Ratings" },
        { value: "b", text: "Likes" }
      ],*/
      //search: { filter: null, text: "" }
      //likes: { count: 0, hit: 0 }
    };
  },
  methods: {
    show_hover(flag, active_id) {
      this.hover_flag = flag;
      this.active_id = active_id;
    },
    sort() {
      //console.log(this.search.filter);
      this.search.filter == "b"
        ? this.giocos_data.sort(function(a, b) {
            return b.likes - a.likes;
          })
        : this.giocos_data.sort(function(a, b) {
            return b.ratings - a.ratings;
          });
    },
    search_text() {
      //console.log(this.search.text);

      var inside = this;

      this.giocos_data = this.bustine_gioco.filter(function(gioco) {
        if (
          gioco.nome
            .toLowerCase()
            .indexOf(inside.search.text.toLowerCase()) != "-1"
        ) {
          return gioco;
        }
      });
    },
    check_active(id) {
      var flag = false;
      this.bustine_gioco.map(function(gioco) {
        if (gioco.id == id) {
          flag = gioco.active_like;
        }
      });
      return flag;
    },
    make_active(id) {
      this.likes.hit++;
      this.bustine_gioco = this.bustine_gioco.map(function(gioco) {
        if (gioco.id == id) {
          gioco.active_like = !gioco.active_like;
          gioco.active_like ? gioco.likes++ : gioco.likes--;
        }

        return gioco;
      });
      var inside = this;

      inside.likes.count = 0;
      this.bustine_gioco.map(function(gioco) {
        inside.likes.count += gioco.likes;
      });
    }
  },
  components: {
    Header
  }
};
</script>




<style scoped  lang="scss">
  @import "../styles/main.scss";
</style>


