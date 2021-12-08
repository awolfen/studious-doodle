<template>
  <div class="container">
    <form @submit.prevent="submitForm" action="submit">
      <input type="text" v-model="postcode" placeholder="Enter postcode..." />
      <input type="submit" value="Submit" />
    </form>
    <input
      type="submit"
      id="existing"
      @click="addExisting"
      value="Add Existing"
    />
  </div>
</template>

<script>
export default {
  name: "FormInput",
  data() {
    return {
      postcode: "",
      existingCodes: {
        postcodes: [
          "EC2A 3AG",
          "N1 1RA",
          "W12 9BL",
          "W1U 6AA",
          "SW7 3HZ",
          "SW7 3DX",
          "W11 4UA",
          "SW13 9LB",
          "HP9 2JH",
          "HP9 1QD",
          "N1 2UQ",
          "W2 2HU",
          "W4 5DG",
          "NW5 2HR",
          "W11 2SE",
          "W8 6QD",
          "W11 3HL",
          "OL2 8NP",
          "W11 1LA",
          "NW3 2PT",
        ],
      },
    };
  },
  methods: {
    async validatePostcode() {
      let responseData;
      try {
        const res = await fetch(
          "https://api.postcodes.io/postcodes/" + this.postcode + "/validate"
        );
        responseData = await res.json();
      } catch (err) {
        console.log(err);
      }
      return responseData.result;
    },
    async fetchCoords(postcode) {
      let responseData;
      try {
        const res = await fetch(
          "https://api.postcodes.io/postcodes/" + postcode
        );
        responseData = await res.json();
      } catch (err) {
        console.log(err);
      }
      return [
        Number(responseData.result.latitude),
        Number(responseData.result.longitude),
      ];
    },
    async fetchMultipleCoords() {
      let responseData;
      try {
        const res = await fetch("https://api.postcodes.io/postcodes", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(this.existingCodes),
        });
        responseData = await res.json();
      } catch (err) {
        console.log(err);
      }
      return responseData.result.map((item) => {
        return [item.result.latitude, item.result.longitude];
      });
    },
    async submitForm() {
      const valid = await this.validatePostcode();
      if (!valid) {
        alert("Please enter a valid postcode");
        return;
      }
      const coords = await this.fetchCoords(this.postcode);
      this.$emit("add-marker", coords);
    },
    // code I wrote before I noticed the Bulk lookup postcodes API
    // async addExisting() {
    //   const coordsList = await Promise.all(
    //     this.existingCodes.map(async (code) => {
    //       const coords = await this.fetchCoords(code);
    //       return coords;
    //     })
    //   );
    //   this.$emit("add-existing", coordsList);
    // },
    async addExisting() {
      const coordsList = await this.fetchMultipleCoords();
      this.$emit("add-existing", coordsList);
    },
  },
  emits: ["add-marker", "add-existing"],
};
</script>

<style>
.container {
  background-color: rgb(75, 100, 131);
  width: 100%;
  padding: 25px;
}
input[type="text"] {
  display: block;
  width: 50%;
  padding: 12px 20px;
  margin: 8px 0;
  margin-left: auto;
  margin-right: auto;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

input[type="submit"] {
  display: block;
  width: 50%;
  background-color: #4caf50;
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  margin-left: auto;
  margin-right: auto;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

#existing {
  background-color: chocolate;
}
</style>
