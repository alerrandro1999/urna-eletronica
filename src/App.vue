<template>
  <div id="app">
    <div class="urna">

      <Tela 
        :tela="tela"
        :numeroVoto="numeroVoto"
        :quantidadeNumeros="quantidadeNumeros"
        :canditato="canditato"
      />

      <Teclado 
        :adicionarNumero="adicionarNumero"
        :corrigi="corrigi"
        :confirmar="confirmar"
        :votaEmBranco="votaEmBranco"
      />

    </div>
  </div>
</template>

<script>

import '@/css/global.css';

import Teclado from './components/Teclado';

import Tela from './components/Tela';

import confirmaAudio from '@/assets/audios/confirm.wav';

import keyAudio from '@/assets/audios/key.wav';



export default {
  name: 'App',
  components: {
    Teclado,
    Tela
  },
  methods:{
    adicionarNumero(numero){
      this.executaSom(keyAudio);

      if(this.numeroVoto.length == this.quantidadeNumeros){
        return false;
        }
        this.numeroVoto += ''+numero;

        this.verificaCanditato();
    },
    verificaCanditato(){
      if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }

      if(this.canditatos[this.tela][this.numeroVoto]){
        this.canditato = this.canditatos[this.tela][this.numeroVoto];
        return true;
      }

      this.canditato = {
        nome: 'Voto nulo',
        partido: 'Voto nulo',
        imagem: ''
      }
    },
    corrigi(){
      this.executaSom(keyAudio);
      
      this.limpar();
    },
    limpar(){
      this.canditato = {};
      this.numeroVoto = ''
    },
    confirmar(){
       if(this.numeroVoto.length < this.quantidadeNumeros){
        return false;
      }

      return this.avancaTela();
    },
    avancaTela(){
      this.executaSom(confirmaAudio);

      if(this.tela == 'prefeito'){
        this.tela = 'vereador';
        this.quantidadeNumeros = 5;
        return this.limpar();
      }

      this.tela = 'fim';

      var instancia = this

      setTimeout(function(){
        instancia.tela = 'prefeito';
        instancia.quantidadeNumeros = 2;
        return instancia.limpar();
      },3000);
    },
    votaEmBranco(){
      if(this.tela == 'fim') return false;
      
      this.limpar();
      this.avancaTela();
    },
    executaSom(arquivoSom){
      if(arquivoSom){
        var audio = new Audio(arquivoSom);
        audio.play();
      }
    }
  },
  data(){
    return{
      tela:'prefeito',
      numeroVoto: '',
      quantidadeNumeros: 2,
      canditato: {},
      canditatos:{
        "prefeito":{
          "01":{
            "nome": "Ash",
            "partido": "Pokemon",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/ash.png"
          },
          "08":{
            "nome": "Vegeta",
            "partido": "Dragon Ball",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/vegeta.png"
          }
        },
        "vereador":{
          "01234":{
            "nome": "Pikachu",
            "partido": "Pokemon",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/pikachu.png"
          },
          "08001":{
            "nome": "Goku",
            "partido": "Dragon Ball",
            "imagem": "https://raw.githubusercontent.com/william-costa/wdev-urna-eletronica-resources/master/images/goku.png"
          }
        }
      }
    }
  }
}
</script>

<style>
#app {
  background-color: var(--background-color);
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.urna{
  width: 1000px;
  height: 500px;
  background-color: var(--ballot-box-background-color);
  padding: 30px;
  border-radius: 5px;
  display: flex;
  justify-content: space-between;
  
}
</style>
