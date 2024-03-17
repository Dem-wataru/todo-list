<template>
  <div class="Home">
    <p>My name is {{ name }} and from is {{ age }}</p>
  </div>
</template>

<script>
export default{
  name: "Home",
  setup() {
    console.log("setup");

    let name = "Goku";
    let age = "Japan";

    return { name, age };
  },
};
</script>