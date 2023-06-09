<template>
  <div>
    <h1 class="centralizado">{{ titulo }}</h1>

    <p v-show="mensagem" class="centralizado">{{ mensagem }}</p>
    
    <input
      type="search"
      class="filtro"
      @input="filtro = $event.target.value"
      placeholder="filtre pelo título da foto"
    />

    <ul class="lista-fotos">
      <li class="lista-fotos-item" v-for="foto of fotosComFiltro">
        <meu-painel :titulo="foto.titulo">
          <imagem-responsiva v-meu-transform:rotate.animate="15" :url="foto.url" :titulo="foto.titulo" />
          <meu-botao 
            tipo="button" 
            rotulo="REMOVER" 
            @botaoAtivado="remove(foto)"
            :confirmacao="true"  
            estilo="padrao"
          />
        </meu-painel>
      </li>
    </ul>
  </div>
</template>

<script>
import Painel from "../shared/painel/Painel.vue";
import ImagemResponsiva from "../shared/imagem-responsiva/ImagemResponsiva.vue";
import Botao from "../shared/botao/Botao.vue";
import transform from '../../directives/Transform';
import FotoService from "../../domain/foto/FotoService";

export default {
  components: {
    "meu-painel": Painel,
    "imagem-responsiva": ImagemResponsiva,
    "meu-botao": Botao,
  },
  directives: {
    'meu-transform': transform
  },
  data() {
    return {
      fotos: [],
      mensagem: "",
      titulo: "Alurapic",
      filtro: "",
    };
  },

  computed: {
    fotosComFiltro() {
      if (this.filtro) {
        let exp = new RegExp(this.filtro.trim(), "i");
        return this.fotos.filter((foto) => exp.test(foto.titulo));
      } else {
        return this.fotos;
      }
    },
  },
  methods: {
    
    remove(foto) {

      this.service
        .apaga(foto._id )
        .then(res => {
          let indice = this.fotos.indexOf(foto);
          this.fotos.splice(indice, 1);
          this.mensagem = `Foto ${foto.titulo} removida com sucesso`;
        })
        .catch(err => {
          console.log(err);
          this.mensagem = `Não foi possível remover a foto ${foto.titulo}`;
        });
    }

  },

  created() {
    this.service = new FotoService(this.$resource);

    this.service
      .lista()
      .then(
        (fotos) => (this.fotos = fotos),
        (err) => console.log(err)
      );
  }
};
</script>
<style>
.centralizado {
  text-align: center;
}

.lista-fotos {
  list-style: none;
}

.lista-fotos .lista-fotos-item {
  display: inline-block;
}

.filtro {
  display: block;
  width: 100%;
}
</style>
