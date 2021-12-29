<template>
  <div class="upload container">
    
  </div>
</template>

<script>
// import AXIOS

export default {
  name: 'Upload',
  created() {
    fetch("http://127.0.0.1:8000/")
    .then(async response => await response.json())
    .then(async response => {    
        console.log(response)
    })
  }
}
</script>

<style>

</style>