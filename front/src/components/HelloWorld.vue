<template>
<div style="background-color: #f9d048;"   class="hello">

<div  style="background-color: #f9d048;">
        <br> 
          <span class="text-4xl font-thin">Buscador de Director </span>
           <span class="hidden sm:inline-block align-bottom text-xs">IMDb</span>
       
</div>



<section style="background-color: #f9d048;"  class="bg-indigo-dark h-50 p-8">
              <div class="container mx-auto py-8">
                <input  v-model="message" class="w-full h-16 px-3 rounded mb-8 focus:outline-none focus:shadow-outline text-xl px-8 shadow-lg" type="search" placeholder="Buscar Pelicula">
          </div>
</section>

<section class="h-50 p-8">
<button v-on:click="traer" style="background-color: #E8CA80;" class="bg-blue text-black font-bold py-2 px-4 border-b-4 hover:border-b-2 hover:border-t-2 border-blue-dark hover:border-blue rounded">
  buscar
</button>
</section>

<nav class="font-sans flex flex-col text-center content-center sm:flex-row sm:text-left sm:justify-between py-2 px-6 bg-white shadow sm:items-baseline w-full">
 

  <div class="mb-2 sm:mb-0 inner">
    <a  class="text-2xl no-underline text-grey-darkest hover:text-blue-dark font-sans font-bold">Director: {{info}} </a>   <br>
    <span class="text-xs text-grey-dark">Resultados de la busqueda</span> 
  </div>

</nav>

</div>
  
  
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data(){
    return {
      algo: false,
    };
  },
};

new Vue({
  el: '#app',
  data () {
    return {
      info: null
    }
  },
  mounted () {
    axios
      .get('198.199.82.99:3000/{message}')
      .then(response => (this.info = response))
  }
})


</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
