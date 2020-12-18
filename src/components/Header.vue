<template>
  <mdb-navbar color="white">
    <mdb-navbar-brand>
      <h1 class="mb-0">Food Truck Finder</h1>
    </mdb-navbar-brand>
  </mdb-navbar>
</template>

<script>
import { mdbNavbar, mdbNavbarBrand } from "mdbvue";
export default {
  name: "Header",
  components: {
    mdbNavbar,
    mdbNavbarBrand,
  },
};
</script>

<style>
.navbar-toggler {
  display: none;
}
</style>
