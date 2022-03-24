<template>
  <div class="container">
    <div class="row">
      <h1 class="d-flex justify-content-center mt-3 fs-7">Simulador FTP</h1>

      <fieldset class="border p-3 rounded-3 mb-3">
        <legend class="float-none fs-6 w-auto">Local</legend>

        <form class="row g-2">
          <div class="col-md-6">
            <label class="mb-3">Escolhe um arquivo para envio:</label><br>
            <input type="file" class="form-control-file" @change="enviarArquivo">
          </div>
          <div class="col-md-6">
            <label class="form-label">Informações arquivo</label>
            <textarea class="form-control" id="textAreaLocal" rows="7" :value="infoArquivoLocal" disabled></textarea>
          </div>
        </form>
      </fieldset>

      <fieldset class="border p-3 rounded-3 mb-3">
        <legend class="float-none fs-6 w-auto">Servidor</legend>

        <form class="row g-2">
          <div class="col-md-12">
            <button type="button" class="btn btn-success m-2" @click="this.statusServidor = 'Ligado'">Ligar</button>
            <button type="button" class="btn btn-danger m-2" @click="this.statusServidor = 'Desligado'">Desligar</button>
            <label class="form-label">Situação do Servidor: {{this.statusServidor}}</label>
          </div>

          <div class="col-md-6">
            <label class="form-label">Respostas</label>
            <textarea class="form-control" id="textAreaServidor" rows="7" disabled></textarea>
          </div>
          <div class="col-md-6">
            <label class="form-label">Arquivos</label>
            <textarea class="form-control" id="textAreaArquivosServidor" rows="7" disabled></textarea>
          </div>
        </form>
      </fieldset>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      statusServidor: 'Desligado',
      infoArquivoLocal: ''
    }
  },
  methods: {
    enviarArquivo(event) {
      const file = event.target.files[0];

      if (this.statusServidor === 'Ligado') {
        const tamanho = this.calculaTamanhoArquivo(file);

        this.infoArquivoLocal = 'Nome: ' + file.name + '\nTamanho : ' + tamanho + '\nTipo: ' + file.type;
      } else {
        alert('Servidor está desligado! Favor ligar o mesmo para continuar com as operações')
      }
    },
    calculaTamanhoArquivo(file) {
      //Calcular o tamanho do arquivo
      let tamanho = file.size / 1024;

      if (tamanho / 1024 > 1) {
        if (((tamanho / 1024) / 1024) > 1) {
          tamanho = (Math.round(((tamanho / 1024) / 1024) * 100) / 100);
          tamanho = tamanho + "Gb";
        }
        else {
          tamanho = (Math.round((tamanho / 1024) * 100) / 100)
          tamanho = tamanho + "Mb";
        }
      }
      else {
        tamanho = (Math.round(tamanho * 100) / 100)
        tamanho = tamanho + "kb";
      }
      return tamanho;
    }
  }
}
</script>

<style lang="scss">
body {
  background-color: #f9f9f9;
  font-family: sans-serif;
}
</style>
