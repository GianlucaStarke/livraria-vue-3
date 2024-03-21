<script setup>
import { ref, reactive, onMounted } from "vue"
import LivrosApi from "@/api/livros"
import CategoriasApi from "@/api/categorias"
import EditorasApi from "@/api/editoras"
const livrosApi = new LivrosApi()
const categoriasApi = new CategoriasApi()
const editorasApi = new EditorasApi()

const defaultLivro = { titulo: "", isbn: null, quantidade: null, preco: null, caregoria: "", editora: "" }
const livros = ref([])
const livro = reactive({ ...defaultLivro })

const categorias = ref([])

const editoras = ref([])

onMounted(async () => {
	livros.value = await livrosApi.buscarTodosOsLivros()
	categorias.value = await categoriasApi.buscarTodasAsCategorias()
	editoras.value = await editorasApi.buscarTodasAsEditoras()
})



function limpar() {
	Object.assign(livro, { ...defaultLivro })
}

async function salvar() {
	if(livro.id)
		await livrosApi.atualizarLivro(livro)
	else
		await livrosApi.adicionarLivro(livro)

	livros.value = await livrosApi.buscarTodosOsLivros()
	limpar()
}

function editar(livro_para_editar) {
	console.log(livro_para_editar)
	Object.assign(livro, livro_para_editar)
}

async function excluir(id) {
	await livrosApi.excluirLivro(id)
	livros.value = await livrosApi.buscarTodosOsLivros()
	limpar()
}
</script>

<template>
	<h1>Livros</h1>
	<hr />
	<div class="form">
		<flex space-between>
			<div class="width-large">
				<label>Título</label><br>
				<input class="width-full" type="text" v-model="livro.titulo">
			</div>
			<div>
				<label>ISBN</label><br>
				<input type="text" v-model="livro.isbn">
			</div>
			<div>
				<label>Quantidade</label><br>
				<input type="number" v-model="livro.quantidade" placeholder="0">
			</div>
			<div>
				<label>Preço</label><br>
				<input type="number" v-model="livro.preco" placeholder="0.00">
			</div>
		</flex>
		<flex left>
			<div>
				<label>Categoria</label><br>
				<input class="width-medium" type="text" v-model="livro.categoria" list="categorias">
				<datalist id="categorias">
					<option v-for="categoria in categorias" :value="categoria.id">
						{{ categoria.descricao }}
					</option>
				</datalist>
			</div>
			<div class="margin-left-small">
				<label>Editora</label><br>
				<input class="width-medium" type="text" v-model="livro.editora" list="editoras">
				<datalist id="editoras">
					<option v-for="editora in editoras" :value="editora.id">
						{{ editora.nome }}
					</option>
				</datalist>
			</div>
		</flex>
		<button @click="salvar" class="margin-small">Salvar</button>
		<button @click="limpar" class="margin-small">Limpar</button>
	</div>
	<hr />
	<ul>
		<li v-for="livro in livros" :key="livro.id">
			<span @click="editar(livro)">
				{{ livro.titulo }} - {{ livro.preco }}
			</span>
			<button @click="excluir(livro.id)">X</button>
		</li>
	</ul>
</template>