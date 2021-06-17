<template>
  <div id="app">
    <h1>Villains Gallore!</h1>
    <form id="villain_form" action="javascript:void(0)">
      <input id="villain_name" type="text" placeholder="Name" />
      <input id="villain_desc" type="text" placeholder="Description" />
      <input id="villain_img" type="text" placeholder="Image URL" />
      <input @click="post_villain" type="submit" value="Submit" />
    </form>
    <h3>{{ post_status }}</h3>
    <div id="villain_container">
      <div v-for="villain in villains" :key="villain[3]" class="villain_card">
        <img :src="villain[2]" :alt="villain[1]" />
        <h3 class="villain_title">{{ villain[0] }}</h3>
        <p class="villain_desc">{{ villain[1] }}</p>
        <input
          class="villain_delete_button"
          type="submit"
          value="Delete Villain"
          @click="delete_villain(villain[3])"
        />
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      villains: [],
      post_status: "",
    };
  },
  // Make sure you pay attention to the URL being used! Put that variable in the .env.local file!
  // Also make sure you change that after you do your local testing!
  methods: {
    delete_villain(id) {
      this.post_status = "Deleting Villain!";
      axios
        .request({
          url: `${process.env.VUE_APP_API_URL}/villain`,
          method: "DELETE",
          data: {
            id: id,
          },
        })
        .then((res) => {
          console.log(res);
          this.villains = this.villains.filter((villain) => villain[3] != id);
          this.post_status = "Villain Deleted";
        })
        .catch((err) => {
          console.log(err);
          this.post_status = "Error Deleting Villain, Sorry!";
        });
    },
    post_villain() {
      this.post_status = "Posting New Villain!";
      axios
        .request({
          url: `${process.env.VUE_APP_API_URL}/villain`,
          method: "POST",
          data: {
            name: document.getElementById("villain_name").value,
            desc: document.getElementById("villain_desc").value,
            img: document.getElementById("villain_img").value,
          },
        })
        .then((res) => {
          console.log(res);
          document.getElementById("villain_form").reset();
          this.villains.push(res.data);
          this.post_status = "Villain Created!";
        })
        .catch((err) => {
          console.log(err);
          this.post_status = "Error Posting New Villain, Sorry!";
        });
    },
  },
  mounted() {
    this.post_status = "Loading Villains";
    axios
      .request({
        url: `${process.env.VUE_APP_API_URL}/villain`,
        method: "GET",
      })
      .then((res) => {
        console.log(res);
        this.villains = res.data;
        this.post_status = "";
      })
      .catch((err) => {
        console.log(err);
        this.post_status = "Error Loading Villains, Sorry!";
      });
  },
};
</script>

<style>
img {
  max-width: 100%;
  object-fit: cover;
}

input[type="text"],
input[type="text"]:focus,
input[type="text"]:active {
  border: none;
  border-bottom: 2px solid black;
  margin: 10px;
  outline: none;
}

input[type="submit"]:hover {
  cursor: pointer;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

form {
  display: grid;
  place-items: center;
  width: 50%;
}

#villain_container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  column-gap: 50px;
  row-gap: 50px;
  width: 90%;
  margin-left: 5%;
  place-items: center;
  margin-top: 50px;
}

.villain_title {
  text-align: left;
  padding-left: 15px;
}

.villain_desc {
  text-align: left;
  padding-left: 15px;
}

#villain_container img {
  width: 100%;
  border-radius: 15px;
}

.villain_card {
  box-shadow: 3px 3px 6px grey;
  border-radius: 15px;
  transition: all 0.25s ease-in-out;
  max-width: 400px;
}

.villain_card:hover {
  box-shadow: 6px 6px 9px grey;
}

.villain_card:focus,
.villain_card:active {
  box-shadow: 1px 1px 2px grey;
}

.villain_delete_button {
  margin: 10px;
}
</style>
