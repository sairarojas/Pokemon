
<template>
  <div>
    <nav class="navbar" style="background-color: #edeff0f8;">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">
          <img src="https://raw.githubusercontent.com/PokeAPI/media/master/logo/pokeapi_256.png" alt="Bootstrap"
            width="150" height="100">
        </a>
        <form class="d-flex" role="search">
          <input v-model="criterioDeBusqueda" class="form-control me-2" type="search" placeholder="Search"
            aria-label="Search">
          <button @click.prevent="buscar()" class="btn btn-outline-success" type="submit">Buscar</button>
        </form>
      </div>
    </nav>
    <nav class="navbar bg-body-tertiary">
      <div class="container-fluid">
        <div class="dropdown">
          <button class="btn btn-secondary dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">
            Filtrado por tipo
          </button>
          <ul class="dropdown-menu dropdown-menu-dark">
            <li><a class="dropdown-item" @click="filtrarPorTipo(null)">Todos</a></li>
            <li><a class="dropdown-item" @click="filtrarPorTipo('grass')">Grass</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('poison')">Poison</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('flying')">Flying</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('fire')">Fire</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('water')">Water</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('bug')">Bug</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('normal')">Normal</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('electric')">Electric</a></li>
            <li><a class="dropdown-item " @click="filtrarPorTipo('ground')">Ground</a></li>
            <li>
              <hr class="dropdown-divider">
            </li>
            <li><a class="dropdown-item" href="#">Me doy</a></li>
          </ul>
        </div>
      </div>
    </nav>
    <div class="container3" style="align-items: center" v-if="busqueda && respuesta.id">
      <div class="row row-cols-1 row-cols-md-4 g-3" style="width: 94%; text-align: center; margin: 6%">
        <div class="card text-center">
          <button @click="obtenerUrlPokemon(respuesta.url)">
            <img :src="respuesta.img" class="card-img-top" alt="...">
          </button>
          <div class="card-body">
            <h6>N°{{ respuesta.id }}</h6>
            <h3>{{ respuesta.nombre }}</h3>
            <div class="tipos">
              <button v-if="respuesta && respuesta.tipos && respuesta.tipos.length" type="button" class="btn btn-primary"
                v-for="(tipo, i) in respuesta.tipos" :key="i"
                style="border: solid transparent; margin-left: 3%">
                {{ tipo }}
              </button>

            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="row row-cols-1 row-cols-md-4 g-3" style="grid-gap:10px; justify-content: center;">
      <div class="card" style="max-width: 300px; padding: 0px; text-align: center;" v-for="pokemon in pokemons"
        :key="pokemon.id">
        <img :src="pokemon.img" class="card-img-top" :alt="pokemon.name" style="background-color: gainsboro;" />
        <div class="card-body">
          <p class="card-text">N° {{ pokemon.id }}</p>
          <h5 class="card-title">{{ pokemon.name }}</h5>
          <div class="row row-cols-2 row-cols-md-2 g-2">
            <p class="tipo1-color" v-for="(tipo, id) in pokemon.tipo_pk" :key="id"
              :style="{ 'background-color': getColorByType(tipo), 'color': 'white' }">
              {{ tipo }}
            </p>
          </div>
          <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal"
            @click="verDetalle(pokemon)">
            Ver detalle
          </button>

          <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true"
            v-if="mostrarModal">
            <div class="modal-dialog modal-lg">
              <div class="modal-content">
                <div class="modal-header">
                  <h1 class="modal-title fs-5" id="exampleModalLabel">{{ pokemonSeleccionado.name }}</h1>
                  <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"
                    @click="mostrarModal = false"></button>
                </div>
                <div class="modal-body">
                  <div class="row">
                    <div class="col-6 col-sm-6">
                      <h1> #{{ pokemonSeleccionado.id }} </h1>
                      <p><b> {{ pokemonSeleccionado.name }}</b></p>
                      <button v-for="(tipo, id) in pokemonSeleccionado.tipo_pk" :key="id" :class="['btn', 'tipo1-color']"
                        :style="{ 'background-color': getColorByType(tipo), 'color': 'white', 'margin-right': '10px' }">
                        {{ tipo }}
                      </button>
                      <div class="row">
                        <div id="pokemonHeight" class="pokemonHeight"><b>Altura:</b>{{ pokemonSeleccionado.height }}</div>
                        <div id="pokemonWeight" class="pokemonWeight"><b>Peso:</b>{{ pokemonSeleccionado.weight }}</div>
                      </div>
                    </div>
                    <div class="col-3 col-sm-2">
                      <img :src="pokemonSeleccionado.img" id="imagenPokemon" style="width: 240px;height: 230px;">
                    </div>
                  </div>
                  <div class="col-6 col-sm-6" style="margin-left: 100px;">
                    <p>Estadisticas</p>
                    <div class="progress" role="progressbar" aria-label="Animated striped example" aria-valuenow="75"
                      aria-valuemin="0" aria-valuemax="100" style="margin-bottom: 10px;">
                      <div class="progress-bar progress-bar-striped bg-danger"
                        :style="{ width: pokemonSeleccionado.hp + '%' }">HP {{ pokemonSeleccionado.hp }}</div>
                    </div>
                    <div class="progress" role="progressbar" aria-label="Animated striped example" aria-valuenow="75"
                      aria-valuemin="0" aria-valuemax="100" style="margin-bottom: 10px;">
                      <div class="progress-bar progress-bar-striped" :style="{ width: pokemonSeleccionado.ataque + '%' }">
                        Ataque {{ pokemonSeleccionado.ataque }}</div>
                    </div>
                    <div class="progress" role="progressbar" aria-label="Animated striped example" aria-valuenow="75"
                      aria-valuemin="0" aria-valuemax="100" style="margin-bottom: 10px;">
                      <div class="progress-bar progress-bar-striped bg-danger"
                        :style="{ width: pokemonSeleccionado.defensa + '%' }">Defensa {{ pokemonSeleccionado.defensa }}
                      </div>
                    </div>
                    <div class="progress" role="progressbar" aria-label="Animated striped example" aria-valuenow="75"
                      aria-valuemin="0" aria-valuemax="100" style="margin-bottom: 10px;">
                      <div class="progress-bar progress-bar-striped"
                        :style="{ width: pokemonSeleccionado.ataque_especial + '%' }">Ataque Especial {{
                          pokemonSeleccionado.ataque_especial }}</div>
                    </div>
                    <div class="progress" role="progressbar" aria-label="Animated striped example" aria-valuenow="75"
                      aria-valuemin="0" aria-valuemax="100" style="margin-bottom: 10px;">
                      <div class="progress-bar progress-bar-striped bg-danger"
                        :style="{ width: pokemonSeleccionado.defensa_especial + '%' }">Defensa Especial {{
                          pokemonSeleccionado.defensa_especial }}</div>
                    </div>
                    <div class="progress" role="progressbar" aria-label="Animated striped example" aria-valuenow="75"
                      aria-valuemin="0" aria-valuemax="100" style="margin-bottom: 10px;">
                      <div class="progress-bar progress-bar-striped"
                        :style="{ width: pokemonSeleccionado.velocidad + '%' }">Velocidad {{ pokemonSeleccionado.velocidad
                        }}</div>
                    </div>
                  </div>
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-secondary" data-bs-dismiss="modal"
                    @click="mostrarModal = false">Cerrar</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="d-grid gap-2 col-1 mx-auto" style="padding: 15px;">
      <button type="button" class="btn btn-primary center-button" @click="cargarMasPokemon">Ver más</button>
    </div>
  </div>
</template>
<script>
import { ref, onMounted, computed } from "vue";
import axios from "axios";

export default {
  name: "App",
  setup() {
    const pokemons = ref([]);
    const carrito = ref([]);
    const mostrarModal = ref(false);
    const pokemonSeleccionado = ref(null);
    const filtroTipo = ref(null);
    const numActual = ref(0);
    const busqueda = ref(false);
    const criterioDeBusqueda = ref("");
    const respuesta = ref(null);


    const cargarMasPokemon = async () => {
      try {
        const offset = numActual.value + 50;
        const response = await axios.get(
          `https://pokeapi.co/api/v2/pokemon?limit=50&offset=${offset}`
        );
        const pokemonList = response.data.results;

        for (const pokemon of pokemonList) {
          const response = await axios.get(pokemon.url);
          const data = response.data;

          pokemons.value.push({
            id: data.id,
            img: data.sprites.other["official-artwork"].front_default,
            name: data.name,
            height: data.height,
            weight: data.weight,
            tipo_pk: data.types.map((pk) => pk.type.name),
            hp: data.stats[0].base_stat,
            ataque: data.stats[1].base_stat,
            defensa: data.stats[2].base_stat,
            ataque_especial: data.stats[3].base_stat,
            defensa_especial: data.stats[4].base_stat,
            velocidad: data.stats[5].base_stat,
            descripcion: "",
          });
        }
        numActual.value = offset;
      } catch (error) {
        console.error("Error al cargar más Pokémon:", error);
      }
    };

    onMounted(async () => {
      try {
        const response = await axios.get(
          "https://pokeapi.co/api/v2/pokemon?limit=50&offset=0"
        );
        const pokemonList = response.data.results;

        for (const pokemon of pokemonList) {
          const response = await axios.get(pokemon.url);
          const data = response.data;

          pokemons.value.push({
            id: data.id,
            img: data.sprites.other["official-artwork"].front_default,
            name: data.name,
            height: data.height,
            weight: data.weight,
            tipo_pk: data.types.map((pk) => pk.type.name),
            hp: data.stats[0].base_stat,
            ataque: data.stats[1].base_stat,
            defensa: data.stats[2].base_stat,
            ataque_especial: data.stats[3].base_stat,
            defensa_especial: data.stats[4].base_stat,
            velocidad: data.stats[5].base_stat,
            descripcion: "",
          });
        }
      } catch (error) {
        console.error("Error fetching Pokemon data:", error);
      }
    });

    async function buscar() {
      try {
        let criterio = await axios.get(`https://pokeapi.co/api/v2/pokemon/${criterioDeBusqueda.value.toLowerCase()}`);
        console.log("Respuesta de búsqueda:", criterio.data);
        let pokemonData = criterio.data;
        respuesta.value = {
          url: criterio.config.url,
          id: pokemonData.id,
          img: pokemonData.sprites.other["official-artwork"].front_default,
          nombre: pokemonData.name,
          altura: pokemonData.height,
          peso: pokemonData.weight,
          tipos: pokemonData.types.map((tipo) => tipo.type.name),
          estadisticas: pokemonData.stats.map((stat) => {
            return { name: stat.stat.name, cant: stat.base_stat };
          }),
        };
        busqueda.value = true;
      } catch (error) {
        console.error(error);
      }
    }
    const obtenerUrlPokemon = (url) => {
      console.log("URL del Pokemon:", url);
    };

    const verDetalle = (pokemon) => {
      pokemonSeleccionado.value = pokemon;
      mostrarModal.value = true;
    };

    const getColorByType = (type) => {
      const typeColors = {
        grass: "green",
        fire: "orange",
        water: "blue",
        poison: "purple",
      };
      return typeColors[type] || "gray";
    };

    const pokemonsFiltrados = computed(() => {
      if (!filtroTipo.value) {
        return pokemons.value;
      } else {
        return pokemons.value.filter((pokemon) =>
          pokemon.tipo_pk.includes(filtroTipo.value)
        );
      }
    });
    const filtrarPorTipo = (tipo) => {
      filtroTipo.value = tipo;
    };

    return {
      pokemons: pokemonsFiltrados,
      carrito,
      mostrarModal,
      pokemonSeleccionado,
      filtroTipo,
      busqueda,
      criterioDeBusqueda,
      respuesta,
      buscar,
      verDetalle,
      getColorByType,
      filtrarPorTipo,
      cargarMasPokemon,
      obtenerUrlPokemon,
    };
  },
};


</script>

<style scoped>
.numero {
  font-size: 150px;
  color: rgb(119, 126, 20);
}

.pokemon-id {
  position: absolute;
  top: 0;
  left: 0;
  padding: 6px 200px;
  border-radius: 5px;
  font-weight: bold;
  font-size: 200px;
  color: rgb(10, 168, 236);
}

.botones {
  float: left;
  top: 0;
  left: 0;
  padding: 6px 10px;

}

.modal-lg {
  width: 300%;
  height: 200%;
}

.amplio {
  width: 280px;
}

body {
  width: 100px;
  height: 100px;
}

.pokemon-id {
  position: absolute;
  top: 2;
  left: 0;
  padding: 6px 200px;
  border-radius: 5px;
  font-weight: bold;
  font-size: 200px;
  color: rgb(10, 236, 78);
}

.pokemonName {
  font-size: 18px;
  text-align: center;
  margin-top: 10px;
}

h1 {
  font-size: 80px;
  color: rgb(10, 190, 214);
}
</style>
