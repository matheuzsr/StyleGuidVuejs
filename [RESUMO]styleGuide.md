# Resumo Style Guide
  ### Informações importantes

```diff
+ O uso de "aspas duplas" TEMPLATE.
+ O uso de 'aspas simples' SCRYPTS.

+ Lembrar sempre virgula, e ponto e virgula;   
```

## 1. Ordem dos elementos do componente single-file
 Exemplo de código
```html
<template>
<ComponentChild/>
</template>

<script>
import ComponentChild from '...'
export default {

  name: "ExempleTest",
  components: {
    ComponentChild
  },

  props: {
    propTest: Object
  },

  data: () => ({
    userName:"",
    alert: false
  }),

  mounted() {
    //Função que é executada quando o component é montado
  },

  computed: {
    //Similar a PROCEDURE - NÃO recebe parâmetro
    procedureName() {
      //
    }
  },

  methods: {
    //Similar a FUNÇÃO - recebe parâmetro
    functionName(x) {
      //
    }
    
  }
};
</script>

<style>
/* ... */</style>
```
## 2. Nome do componente (name)
O campo name do componente deve conter **informações suficientes que descrevam o que é o mesmo** e ser escrito em **PaskalCase**.
```js
export default {
  name: "TodoItem",
  //...
}
```
## 3. Infos sobre os conceitos/parâmetros
Num componente Vue temos de ter as coisas organizadas, para isso existem alguns conceitos/parametros a ter em conta e que são a maneira de funcionar de um componente.


### props

Os dados passados ao componente. Esta é uma das poucas vias de passagem de dados, e permite controlos rígidos de tipagem. O que está no data é interno ao componente e possivelmente aos seus descendentes. O que está no props são dados para usar no componente e não podem ser mudados diretamente, só via mudança no estado do componente ou parte da aplicação que gera esses dados.



### data
Aqui deves guardas primitivos. Strings, arrays, objectos, etc. No fundo o estado do componente.


### computed(procedure)
Aqui tens uma mistura dos dois anteriores, e entra a lógica/metodologia de programação reactiva. Estes métodos são funções que retornam dados são provaveis de terem sido alterados entre renderizações. Imagina um exemplo:

Tens uma imagem no componente e queres pegar no canto para a fazer maior. Algures na lógica vais ter de dizer qual o width, height dessa imagem. Depois queres arrastar (fazer drag) dela e vais ter de controlar o left e top dessa imagem.

Ora em vêz de ter um 4 valores espalhados no data, e depois no template uma misturada com esses valores tipo:  que fica meio dificil de manter; o mellhor é ter um computed para isso:

```js
computed: 
    styles(){
        return {left: this.left, top: this.top, height: this.height, width: this.width};
    }
}
```
e no template fica bem mais limpo com somente .

Outra funcionalidade muito util é podermos importar da store os getters que vão para computed e os métodos da store que vão para os methods do componente.



### methods(functions)
Aqui deves guardar funções. Todo o tipo de funções que tratam dados e funcionalidade do componente.
Guardando essas funções no methods permite que eles sejam usados noutros componentes, por herança ou por grau de parentesco. Aqui podes também importar métodos da store.


### watch
Aqui deves guardar métodos que devem ser chamados quando o valor de uma propriedade de data ou props mudar. Pode haver casos em que funcione usar computed, outras watch. Em regra geral, se houver algo que precises saber quando mudou usa watch; quando quiseres ter sempre dados atualizados, usa computed.

### 4. Notação do nome de propriedades

Nome de propriedades devem sempre usar **camelCase** na sua declaração, mas **kebab-case** nos templates e JSX.

```js
props: {
  greetingText: String
}
```

```js
//Uso no template
<WelcomeMessage greeting-text="hi"/>
```
## 5. Key no v-for
key no v-for é sempre **necessária** em componentes, a fim de manter o estado do componente interno para baixo do sub-árvore. Mesmo para elementos, é uma boa prática manter um comportamento previsível, como constância de objetos em animações.
```js
<ul>
  <li
    v-for="(todo,index) in todos"
    :key= "index"
  >
    {{ todo.text }}
  </li>
</ul>
```

### 6. Expressões simples em templates

Templates de componentes devem incluir apenas expressões simples, com expressões mais complexas refatoradas em propriedades computadas ou métodos.

```js
<!-- Em um template -->
{{ normalizedFullName }}
// A expressão complexa foi movida para uma propriedade computada
```

```js
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```