<template>
	<div id="app" class="container">
		<h1>HTTP com Axios</h1>
		<b-alert 
			show 
			dismissible 
			v-for="mensagem in mensagens"
			:key="mensagem.texto" 
			:variant="mensagem.tipo"
		>
			{{ mensagem.texto }}
		</b-alert>
		<b-card>
			<b-form-group label="Nome:">
				<b-form-input 
					type="text"
					size="lg"
					v-model="usuario.nome"
					placeholder="Informe o Nome">
				</b-form-input>
			</b-form-group>
			<b-form-group label="Email:">
				<b-form-input 
					type="email"
					size="lg"
					v-model="usuario.email"
					placeholder="Informe o Email">
				</b-form-input>
			</b-form-group>
			<b-button 
				@click="salvar" 
				size="lg"
				variant="primary">
				Salvar
			</b-button>
			<b-button 
				@click="obterUsuarios" 
				size="lg"
				variant="success"
				class="ml-2">
				Obter Usuários
			</b-button>
			
		</b-card>
		<b-list-group>
			<b-list-group-item v-for="(usuario, id) in usuarios" :key="id">
				<strong>Nome:</strong> {{usuario.nome}}<br>
				<strong>Email:</strong> {{usuario.email}}<br>
				<strong>ID:</strong> {{id}}<br>
				<b-button @click="carregar(id)" variant="warning" size="lg">Carregar</b-button>
				<b-button @click="excluir(id)" variant="danger" size="lg" class="ml-2">Excluir</b-button>
			</b-list-group-item>
		</b-list-group>
	</div>
</template>

<script>
// import axios from 'axios'

export default {
	data() {
		return {
			mensagens: [],
			usuarios: [],
			usuario: {
				nome: '',
				email: ''
			}
		}
	},
	methods: {
		limpar(){
			this.usuario.nome = ''
			this.usuario.email = ''
			this.id = null
		},
		obterUsuarios() {
			// axios.get('https://curso-vue-f643d.firebaseio.com/usuarios.json')
			this.$http.get('usuarios.json')
				.then( resp => {
					this.usuarios = resp.data
					console.log(this.usuarios)
				})
		},
		salvar() {
			// console.log(this.usuario);
			const metodo = this.id ? 'patch' : 'post'
			const finalUrl = this.id ? `/${this.id}.json` : '.json'
			this.$http[metodo](`/usuarios${finalUrl}`, this.usuario)
				.then(() => {
					this.limpar()
					if(metodo == 'post') {
						this.mensagens.push({
							texto: 'Usuário cadastrado com sucesso!',
							tipo: 'success'
						})
					}
					else {
						this.mensagens.push({
							texto: 'Usuário Alterado com sucesso!',
							tipo: 'info'
						})
					}
				})
				.then(() => obterUsuarios())
		},
		carregar(id) {
			this.id = id
			this.usuario = { ...this.usuarios[id]}
		},
		excluir(id) {
			this.$http.delete(`/usuarios/${id}.json`)
				.then(() => {
					this.limpar()
					this.mensagens.push({
						texto: 'Usuário excluído com sucesso!',
						tipo: 'warning'
					})
				})
				.then(() => this.obterUsuarios())
				.catch(() => {
					this.mensagens.push({
						texto: 'Não foi possível excluir',
						tipo: 'danger'
					})
				})
		}
	}
	// created() {
	// 	this.$http.post('usuarios.json', {
	// 		nome: 'Maria',
	// 		email: 'maria@email.com'
	// 	}).then(res => console.log(res))
	// }
}
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
	font-size: 1.5rem;
}

#app h1 {
	text-align: center;
	margin: 50px;
}
</style>
