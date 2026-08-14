<template>
  <div class="about">
    <h2>Sobre o aplicativo</h2>
    <p>
      O Registro de Atividades é um gerenciador de tarefas desenvolvido como
      Progressive Web App usando Vue.js 3 e Vite.
    </p>
    <p>
      Nele é possível adicionar, editar, concluir e remover tarefas. O aplicativo
      também pode ser instalado e possui suporte para funcionamento offline.
    </p>

    <h3>Recursos</h3>
    <ul>
      <li>Cadastro e edição de tarefas</li>
      <li>Envio de imagens</li>
      <li>Funcionamento offline</li>
      <li>Instalação no dispositivo</li>
      <li>Notificações</li>
    </ul>

    <h3>Tecnologias utilizadas</h3>
    <ul>
      <li>Vue.js 3</li>
      <li>Vite</li>
      <li>Vue Router</li>
      <li>Workbox</li>
      <li>vite-plugin-pwa</li>
    </ul>
  </div>
</template>

<style scoped>
.about {
  padding: 22px;
  background: white;
  border: 1px solid #d6d6d6;
  border-radius: 7px;
  line-height: 1.55;
}

.about h2 {
  margin: 0 0 12px;
  color: #2563eb;
  font-size: 1.35rem;
}

.about h3 {
  margin: 20px 0 8px;
  font-size: 1rem;
}

.about p {
  margin: 0 0 10px;
  color: #555;
  font-size: 0.9rem;
}

.about ul {
  margin: 0;
  padding-left: 20px;
}

.about li {
  margin-bottom: 5px;
  color: #555;
  font-size: 0.88rem;
}
</style>
