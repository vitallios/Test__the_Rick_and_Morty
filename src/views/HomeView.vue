<script setup>
import { ref, onMounted, watch, reactive } from 'vue';
import axios from 'axios'

const title = 'Rick & Morty';
const url = 'https://rickandmortyapi.com/api/';
const posts = ref([])
let NPage =ref(1)

const filtersPraram = reactive({
  sortStatus: '',
  sortName: '',
  sortPage: NPage.value,
})
const pageTo = async (event) => {
  filtersPraram.sortPage = event.target.innerText
}


const serchClick = async (event)=>{
  try {
      const params = {
        name: ' ',
        status: ' '
      }
        params.name = event.target.previousElementSibling.value
        params.status = event.target.previousElementSibling.previousElementSibling.value
      const { data } = await axios.get(`${url}character/`,{params})
      posts.value = data
      } catch (error) {
      console.log(error);
      console.log('1');
      const { data } = await axios.get(`${url}character/?page=${filtersPraram.sortPage? '': 1}&name=&status=`)
      posts.value = data
      }
}

const axiosGet = async()=>{
  try {
    const params = {
      page:'',
    }
    if (filtersPraram.sortPage) {
      params.page = filtersPraram.sortPage
    }
    const { data } = await axios.get(`${url}character/`, {params})
    posts.value = data
  } catch (error) {
    console.log(error);
  }
}
onMounted(axiosGet)
watch([filtersPraram,serchClick], axiosGet)

</script>






<template>
  <div class="w-[95%]  m-auto">
    <h1>{{ title }}</h1>

<!-- status  -->
    <div class="flex gap-3 items-center mt-5">
      <select class="px-2 py-2 border" >
        <option value="">Статус All</option>
        <option value="Alive">Статус Alive</option>
        <option value="Dead">Статус Dead</option>
        <option value="unknown">Статус unknown</option>
      </select>
<!-- name -->
      <select class="px-2 py-2 border">
        <option  value=''>All</option>
        <option v-for="post in posts.results" :value=post.name>{{ post.name }}</option>
      </select>
<!-- button -->
      <button class="py-2 px-5 text-white bg-slate-800" @click="serchClick">
        Применить
      </button>
<!-- paginat -->
      <ul class="flex gap-4"  >
        <li v-for="numb of 3" :key="numb">
          <span class="p-3 border" :value=numb @click="pageTo">{{ numb }}</span>
        </li>
      </ul>
    </div>




    <!-- list Card -->
    <ul class="flex flex-row flex-wrap my-10 justify-center">
      <!-- card -->
      <li v-for="post in posts.results" :key="post.id"
        class=" min-w-[500px] w-[30vw] min-h-[250px] h-[20vh] rounded-lg bg-gray-600 text-white  font-bold flex gap-5 items-center m-2"
        id="CARD">
        <img :src="`${post.image}`" :alt="`${post.name}`" class=" w-1/2.5 h-full rounded-lg">
        <div class="flex flex-col gap-1">
          <h2 class="hidden">{{ post.id }}</h2>
          <div class="flex flex-col justify-start gap-4 items-start">
            <h3>{{ post.name }}</h3>
            <div class="flex flex-row items-center gap-3">
              <span v-if="post.status == 'unknown'" class=" w-3 h-3  bg-gray-500 rounded-full"></span>
              <span v-else-if="post.status == 'Alive'" class=" w-3 h-3  bg-green-500 rounded-full"></span>
              <span v-else class="w-3 h-3 bg-red-500 rounded-full"></span>
              <h3>{{ post.status }} - {{ post.species }}</h3>
            </div>

          </div>
        </div>
      </li>
    </ul>





  </div>
</template>
