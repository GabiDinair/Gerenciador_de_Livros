# Gerenciador_de_Livros

1. Introdução
Este projeto é uma aplicação React que permite adicionar, remover e listar livros, utilizando Redux para gerenciamento de estado e a Context API do React para alternar entre temas claro e escuro.
2. Estrutura do Projeto
A estrutura de diretórios do projeto é a seguinte:
bash
Copiar código
/src
  /components
    FormularioLivro.js
    ListaLivros.js
  /redux
    reducer.js
    store.js
  /ThemeContext
    ThemeContext.js
  App.js
  App.css
  index.js

3. Componentes
3.1. components/FormularioLivro.js
javascript
Copiar código
import React, { useState } from 'react'; 
import { useDispatch } from 'react-redux'; 

const FormularioLivro = () => {
  const [titulo, setTitulo] = useState(''); 
  const dispatch = useDispatch(); 

  const adicionarLivro = () => {
    if (!titulo) return; 

    dispatch({
      type: 'ADICIONAR_LIVRO', 
      payload: { id: Date.now(), titulo }, 
    });

    setTitulo(''); 
  };

  return (
    <div>
      <input
        type="text"
        value={titulo}
        onChange={(e) => setTitulo(e.target.value)} 
        placeholder="Título do Livro"
      />
      <button onClick={adicionarLivro}>Adicionar Livro</button>
    </div>
  );
};

export default FormularioLivro; 

Explicação:
Imports: Importa React, useState para gerenciar o estado local e useDispatch do Redux para despachar ações.
Estado Local: Usa useState para manter o título do livro que está sendo adicionado.
Função adicionarLivro:
Verifica se o título está vazio; se sim, retorna sem fazer nada.
Despacha uma ação ADICIONAR_LIVRO com um payload que inclui um id único (usando Date.now()) e o título.
Limpa o campo de entrada após adicionar.
Renderização: Renderiza um campo de entrada e um botão para adicionar livros.
3.2. components/ListaLivros.js
javascript
Copiar código
import React from 'react'; 
import { useSelector, useDispatch } from 'react-redux'; 

const ListaLivros = () => {
  const livros = useSelector((state) => state.livros); 
  const dispatch = useDispatch(); 

  const removerLivro = (id) => {
    dispatch({ type: 'REMOVER_LIVRO', payload: id }); 
  };

  return (
    <div>
      <h2>Lista de Livros</h2>
      {livros.map((livro) => (
        <div key={livro.id}>
          <span>{livro.titulo}</span>
          <button onClick={() => removerLivro(livro.id)}>Remover</button>
        </div>
      ))}
    </div>
  );
};

export default ListaLivros; 

Explicação:
Imports: Importa React, useSelector e useDispatch do Redux.
Seleção de Estado: Usa useSelector para acessar a lista de livros do estado global.
Função removerLivro: Despacha a ação REMOVER_LIVRO com o id do livro a ser removido.
Renderização: Renderiza a lista de livros, exibindo o título e um botão para remover cada livro.
4. Redux
4.1. redux/reducer.js
javascript
Copiar código
const initialState = {
  livros: [], 
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'ADICIONAR_LIVRO':
      return { ...state, livros: [...state.livros, action.payload] }; 
    case 'REMOVER_LIVRO':
      return { ...state, livros: state.livros.filter(livro => livro.id !== action.payload) }; 
    default:
      return state; 
  }
};

export default reducer; 

Explicação:
Estado Inicial: Define um estado inicial com uma lista vazia de livros.
Função Reducer:
Recebe o estado atual e uma ação.
Dependendo do tipo de ação, atualiza a lista de livros (adiciona ou remove).
Retorno do Estado: Retorna o novo estado ou o estado atual se a ação não for reconhecida.
4.2. redux/store.js
javascript
Copiar código
import { createStore } from 'redux'; 
import reducer from './reducer'; 

const store = createStore(reducer); 

export default store; 

Explicação:
Imports: Importa a função createStore do Redux e o reducer.
Criação da Store: Cria a store usando o reducer e a exporta.
5. Context API para Tema
5.1. ThemeContext/ThemeContext.js
javascript
Copiar código
import React, { createContext, useState, useContext } from 'react'; 

const ThemeContext = createContext(); 

export const ThemeProvider = ({ children }) => {
  const [tema, setTema] = useState('claro'); 

  const toggleTema = () => {
    setTema((prevTema) => (prevTema === 'claro' ? 'escuro' : 'claro')); 
  };

  return (
    <ThemeContext.Provider value={{ tema, toggleTema }}> 
      {children}
    </ThemeContext.Provider>
  );
};

export const useTema = () => {
  return useContext(ThemeContext); 
};

Explicação:
Contexto de Tema: Cria um contexto para gerenciar o tema da aplicação (claro ou escuro).
Provider: O ThemeProvider utiliza o useState para definir o tema inicial como 'claro' e uma função para alternar entre os temas.
Hook useTema: Um hook customizado que facilita o uso do contexto em outros componentes.
6. Estilos
6.1. App.css
css
Copiar código
.claro {
  background-color: white;
  color: black;
}

.escuro {
  background-color: black;
  color: white;
}

.app-container {
  padding: 20px; 
}

Explicação:
Estilos de Tema: Define classes para o tema claro e escuro, alterando as cores de fundo e do texto.
Estilo Geral: Aplica um espaçamento interno à aplicação.
7. Aplicação Principal
7.1. App.js
javascript
Copiar código
import React from 'react'; 
import { Provider } from 'react-redux'; 
import store from './redux/store'; 
import { ThemeProvider, useTema } from './ThemeContext/ThemeContext'; 
import FormularioLivro from './components/FormularioLivro'; 
import ListaLivros from './components/ListaLivros'; 
import './App.css'; 

const App = () => {
  const { tema, toggleTema } = useTema(); 

  return (
    <div className={tema}> 
      <h1>Gerenciador de Livros</h1>
      <button onClick={toggleTema}>Alternar Tema</button> 
      <FormularioLivro /> 
      <ListaLivros /> 
    </div>
  );
};

const Main = () => (
  <Provider store={store}> 
    <ThemeProvider> 
      <App /> 
    </ThemeProvider>
  </Provider>
);

export default Main; 

Explicação:
Provider do Redux: O Provider envolve a aplicação, fornecendo a store do Redux para todos os componentes filhos.
Provider de Tema: O ThemeProvider envolve a aplicação, permitindo que todos os componentes acessem o contexto de tema.
Componente App:
Usa o hook useTema para acessar o tema atual e a função para alternar o tema.
Renderiza a interface, incluindo o formulário e a lista de livros.
8. Conclusão
Este projeto demonstra como usar React com Redux para gerenciamento de estado e a Context API para temas dinâmicos. A aplicação é modular, com componentes bem definidos que se comunicam com o estado global através do Redux, enquanto a troca de temas é tratada de forma simples e eficaz com a Context API.
9. Melhorias Futuras
Validação de Entrada: Implementar validações adicionais no campo de entrada para garantir que o título não contenha caracteres inválidos.
Persistência de Dados: Adicionar funcionalidade para armazenar a lista de livros em Local Storage, permitindo que os dados sejam mantidos após o recarregamento da página.
Estilização Avançada: Melhorar a interface com bibliotecas de UI como Material-UI ou Bootstrap para um design mais responsivo e moderno.
Esta documentação deve ajudá-lo a entender e explicar seu projeto de forma detalhada.

