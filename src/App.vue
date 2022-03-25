<template>
  <div class="container">
    <div class="row">
      <h1 class="d-flex justify-content-center mt-3 fs-7">Simulador FTP</h1>

      <fieldset class="border p-3 rounded-3 mb-3 shadow">
        <legend class="float-none fs-6 w-auto">Usuário</legend>

        <div class="row g-2">
          <div class="col-md-3">
            <label class="form-label">Host</label>
            <input type="text" id="host" class="form-control"/>
          </div>
          <div class="col-md-3">
            <label class="form-label">Usuário</label>
            <input type="text" id="usuario" class="form-control"/>
          </div>
          <div class="col-md-3">
            <label class="form-label">Senha</label>
            <input type="password" id="senha" class="form-control"/>
          </div>
          <div class="col-md-3">
            <button class="btn btn-primary btnUsuario" @click="this.conectaUsuario()">Enviar</button>
          </div>
        </div>
      </fieldset>

      <fieldset class="border p-3 rounded-3 mb-3 shadow">
        <legend class="float-none fs-6 w-auto">Cliente</legend>

        <form class="row g-2">
          <div class="col-md-6">
            <label class="mb-3">Escolhe um arquivo para envio:</label><br>
            <input type="file" class="form-control-file" @change="validaArquivo">
          </div>
          <div class="col-md-6">
            <label class="form-label">Informações arquivo</label>
            <textarea class="form-control" id="textAreaLocal" rows="7" :value="infoArquivoLocal" disabled></textarea>
          </div>
        </form>
      </fieldset>

      <fieldset class="border p-3 rounded-3 mb-3 shadow">
        <legend class="float-none fs-6 w-auto">Servidor</legend>

        <form class="row g-2">
          <div class="col-md-12">
            <button type="button" class="btn btn-success m-2" @click="this.statusServidor = 'Ligado'">Ligar</button>
            <button type="button" class="btn btn-danger m-2" @click="this.statusServidor = 'Desligado'">Desligar</button>
            <label class="form-label">Situação do Servidor: {{this.statusServidor}}</label>
          </div>

          <div class="col-md-6">
            <label class="form-label">Respostas</label>
            <textarea class="form-control" id="respostaServidor" rows="7" disabled></textarea>
          </div>
          <div class="col-md-6">
            <label class="form-label">Arquivos</label>
            <textarea class="form-control" id="arquivosServidor" rows="7" disabled></textarea>
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
      infoArquivoLocal: '',
      logado: false,
    }
  },
  methods: {
    validaArquivo(event) {
      const file = event.target.files[0];

      if (this.statusServidor === 'Ligado') {
        if (this.logado) {
          const tamanho = this.calculaTamanhoArquivo(file);

          this.infoArquivoLocal = 'Nome: ' + file.name + '\nTamanho: ' + tamanho + '\nTipo: ' + file.type;
          
          this.enviarArquivo(file);
        } else {
          alert('Usuário não logado! Favor conectar-se!');
        }
      } else {
        alert('Servidor está desligado! Favor ligar o mesmo para continuar com as operações');
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
    },
    conectaUsuario() {
      this.requestServidor('usuario', document.getElementById('usuario').value);

      if (this.requestServidor('senha', document.getElementById('senha').value)) {
        this.logado = true;
        this.requestServidor('host', document.getElementById('host').value);
      }
    },
    requestServidor(input, elemento) {
      let resultado;

      if (this.statusServidor === 'Ligado') {
        switch (input) {
          case "usuario":
            resultado = "220 Serviço pronto para novo usuário. \n";
            if (elemento !== null) {
              resultado = "331 Nome de usuário ok, precisa de senha. \n";
            } else {
              resultado = "332 Precisa de conta para login. \n";
            }
            break;
          case "senha":
            if (elemento === 'a') {
              resultado = "230 Usuário logado. \n";
              return true;
            }

            resultado = "430 Senha inválida. \n";
            break;
          case "host":
            resultado = "200 A ação solicitada foi concluída com sucesso. \n";
            break;
          case "envio":
            resultado = elemento === 'aberto'
              ? "150 Status do arquivo ok, aberta a conexão de dados. \n"
              : "226 Fechando a conexão de dados. Ação de arquivo solicitada bem-sucedida. \n";
            break;
          default:
            resultado = "502 Comando não implementado. \n";
            break;
        }
      } else {
        resultado = "10060 Não foi possível se conectar com o servidor. \n";
      }

      document.getElementById("respostaServidor").value += resultado;
    },
    enviarArquivo(file) {
      if (this.statusServidor === 'Ligado') {
        this.requestServidor('envio', 'aberto');
        document.getElementById("arquivosServidor").value += file.name + '\n';

        this.requestServidor('envio', 'sucesso');
      } else {
        this.requestServidor('', '');
      }
    },
  }
}
</script>

<style lang="scss">
body {
  background-color: #f9f9f9;
  font-family: sans-serif;
}

.btnUsuario {
  margin-top: 30px;
  float: right;
}

h1 {
  text-shadow: 3px 2px 3px #999999;
}
</style>
