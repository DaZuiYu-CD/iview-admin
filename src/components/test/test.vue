<template>

  <Button type="primary"  @click="test">test</Button>
</template>

<script>
  import axios from 'axios'
  export default {
    name: 'test',
    methods:{
      test(){
        // console.log(axios)
          axios.get('http://localhost:8081/user/getAllUser')
            .then(response => {
              console.log('response')
              console.log(response)
            })
            .catch(error => {
              console.log(error)
            })
      },

    },

  }
</script>

<style scoped>

</style>
