# Style Guide - Dinheirow


Este é o guia de estilo oficial para códigos específicos do Vue. É uma ótima referência para evitar erros, bikeshedding e antipadrões.

### Informações importantes

```diff
+ O uso de "aspas duplas" SEMPRE.
+ O uso de 'aspas simples' SEMPRE.
+ Lembrar sempre virgula, e ponto e virgula ;   
```
### Categorias de regra


  * Prioridade A: Essencial
  * Prioridade B: Altamente Recomendado
  * Prioridade C: Recomendada
  * Prioridade D: use com cuidado

## Regras da prioridade A: essenciais (prevenção de erros)


Os nomes dos componentes sempre devem ter várias palavras, exceto os Appcomponentes raiz e os componentes internos fornecidos pelo Vue, como "transition" ou "component".

>Isso evita conflitos com elementos HTML existentes e futuros, pois todos os elementos HTML são uma única palavra.

### Nome do componente (name)
O campo name do componente deve conter informações suficientes que descrevam o que é o mesmo.
```js
export default {
  name: "TodoItem",
  //...
}
```

### Dados do componente (data)
O componente data deve ser uma função, pois assim a cada instancia do mesmo se tenha um novo conjunto de dados.
```js
export default {
  data: () => ({
   username:""
}
```

### Propriedades (props)
As definições de propriedade devem ser o mais detalhadas possível.

```js
export default {
  props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
}
```

### Key no v-for
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
### V-if com v-for

Nunca use v-ifno mesmo elemento que v-for.

Existem dois casos comuns em que isso pode ser tentador:

* Para filtrar itens em uma lista (por exemplo v-for="user in users" v-if="user.isActive"). **Nesses casos, substitua userspor uma nova propriedade computada que retorne sua lista filtrada (por exemplo activeUsers).**

* Para evitar a renderização de uma lista, se ela estiver oculta (por exemplo v-for="user in users" v-if="shouldShowUsers"). **Nesses casos, mova o v-ifelemento para um contêiner (por exemplo ul, ol).**

```js
<ul>
  <li
    v-for="user in activeUsers"
    :key= "user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

```js
<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key= "user.id"
  >
    {{ user.name }}
  </li>
</ul>
```
### Escopo do estilo de componente

Para aplicativos, os estilos em um Appcomponente de nível superior e nos componentes de layout podem ser globais, mas todos os outros componentes devem sempre ter escopo definido.

>Ou seja, fora o componente App, todos os outros devem sempre ter escopo definido.

```js 
<template>
  <button class="button button-close">X</button>
</template>

<!-- Using the `scoped` attribute -->
<style scoped>
.button {
  border: none !important;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

```js
<template>
  <button :class="[$style.button, $style.buttonClose]">X</button>
</template>

<!-- Using CSS modules -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

```js
<template>
  <button class="c-Button c-Button--close">X</button>
</template>

<!-- Using the BEM convention -->
<style>
@import '@/... .scss'
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```


### Nome de propriedades privadas

Use o escopo do módulo para manter as funções privadas inacessíveis do lado de fora. Se isso não for possível, sempre use o $_prefixo para propriedades particulares personalizadas em um plug-in, mixin etc. que não devem ser considerados API pública. Para evitar conflitos com o código de outros autores, inclua também um escopo nomeado (por exemplo $_yourPluginName_).

```js
var myGreatMixin = {
  // ...
  methods: {
    $_myGreatMixin_update: function () {
      // ...
    }
  }
}

```

```js
// Even better!
var myGreatMixin = {
  // ...
  methods: {
    publicMethod() {
      // ...
      myPrivateFunction()
    }
  }
}

function myPrivateFunction() {
  // ...
}

export default myGreatMixin
```


## Prioridade B: Altamente Recomendado (Melhoram a Legibilidade)


### Cada componente em um arquivo

Sempre que houver um sistema de compilação para concatenar arquivos, cada **componente** deve estar em seu **próprio arquivo**.

```js
components/
|- TodoList.js
|- TodoItem.js
```
```js
components/
|- TodoList.vue
|- TodoItem.vue
```
#### Notação de nomes de componentes single-file

Nomes de arquivos de componentes Single-File devem ser sempre PascalCase ou kebab-case.


Notação com PascalCase garante melhor auto-completação nos editores de código, pois é consistente com a forma como nós referenciamos os componentes no JS(X) e templates, onde for possível. 

```js
components/
|- MyComponent.vue
```


### Nomes de componentes de instância única

Componentes que devem ter somente uma única instância ativa devem começar com o prefixo The, para determinar que eles podem ser somente um.

>Isto não quer dizer que o componente é somente utilizado em uma única página, mas somente utilizado uma vez a cada página. 

Este tipo de componente não aceita qualquer prop, uma vez que são específicos de sua aplicação, não ao contexto deles dentro do app. Se você achar que precisa adicionar props, pode ser uma boa indicação que este é um componente reusável que somente é usado uma vez por página, por enquanto.


### Nomes de componentes fortemente acoplados

Componentes filho que são fortemente acoplados com o componente pai devem incluir, como prefixo, o nome do componente pai.

```js
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```
```js
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```

#### Ordem das palavras nos nomes de componentes

Nomes de componentes devem começar com a palavra de mais alto nível (muitas vezes a mais geral) e terminar com palavras de modificações descritivas.

```js
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue  
```

### Componentes com auto-fechamento

Componentes sem conteúdo devem ser **auto-fechados** em componentes Single-File, templates baseados em Strings e JSX - mas nunca em templates do DOM.

```js
<!-- Em componentes Single-File, templates String e JSX -->
<MyComponent/>
```


### Notação do nome dos componentes em templates

Na maioria dos projetos, nomes de componentes devem ser sempre PascalCase em componentes Single-File e templates String - mas kebab-case nos templates DOM. 

```js
<!-- Em componentes Single-File e templates String -->
<MyComponent/>
```


### Notação de nome de componentes JS/JSX

Nomes de componentes em JS/JSX devem ser sempre PascalCase.

```js
import MyComponent from './MyComponent.vue'
```
```js
export default {
  name: 'MyComponent',
  // ...
}
```
### Componentes com nomes completos

Nomes de componentes devem ter **palavras completas**, ao invés de abreviações.

O preenchimento automático nos editores de código torna o custo de escrever nomes mais longos muito baixo, enquanto a clareza que eles fornecem é inestimável. Abreviações incomuns, em particular, devem ser sempre evitadas.

```js
//Bom Exemplo
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue

//Mal Exemplo
components/
|- SdSettings.vue
|- UProfOpts.vue
```
### Notação do nome de propriedades

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

### Elementos multi-atributo

No JavaScript, dividir objetos que possuem várias propriedades em **várias linhas**, é considerado uma boa convenção, porque é muito mais fácil de ler.

```js
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
/>
```

```js
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```

### Expressões simples em templates

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

### Propriedades computadas simples

Propriedades computadas complexas devem ser quebradas em várias propriedades mais simples, sempre que possível.

```js
//Antes
computed: {
  price: function () {
    var basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```

```js
//Depois
computed: {
  basePrice: function () {
    return this.manufactureCost / (1 - this.profitMargin)
  },
  discount: function () {
    return this.basePrice * (this.discountPercent || 0)
  },
  finalPrice: function () {
    return this.basePrice - this.discount
  }
}
```

### Aspas em valores de atributos

Os valores de atributos HTML não vazios devem sempre estar dentro de aspas (simples ou duplas, o que não for usado em JS).

>Ajuda na legibilidade(os espaçanentos) afim de necessitar o uso das aspas, portanto, é expressamente necessário.

```js
<input type="text">
<AppSidebar :style= "{ width: sidebarWidth + 'px' }">
```

### Abreviação de diretivas

Abreviações de diretiva ( ":" para v-bind:, "@" para v-on: e "#" para v-slot) devem ser sempre usadas ou nunca usadas, matendo-se um **PADRÃO**.

>Foi definido o uso de ":", "@" e "#" pela menor quantidade de código.

```js
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

```js
<input
  @input="onInput"
  @focus="onFocus"
>
```

```js
<template #header>
  <h1>Aqui poderia ser um título de página</h1>
</template>

<template #footer>
  <p>Aqui alguma informação de contato</p>
</template>
```

## Prioridade C: Recomendadas (Minimizando Escolhas Arbitrárias e Sobrecarga Cognitiva)


### Ordem das opções de componente/instância
Opções de componentes/instâncias devem ser ordenadas consistentemente.

Esta é a ordem padrão que recomendamos para opções dos componentes. Eles são divididos em categorias, assim você saberá onde adicionar novas propriedades de plug-ins.

1. Efeitos colaterais (desencadeia efeitos fora do componente
    * el


2. Consciência Global (requer conhecimento além do componente)
    * name
    * parent


3. Tipo de componente (altera o tipo de componente)
    * functional


4. Modificadores de template (muda a forma como o template é compilado)
    * delimiters
    * comments


5. Dependências de template (assets usados no templae)
    * components
    * directives
    * filters

6. Composição (mescla propriedades nas opções)

    * extends
    * mixins

7. Interface (a interface para o componente)

    * inheritAttrs
    * model
    * props/propsData


8. Estado Local (propriedades reativas locais)

    * data
    * computed


9. Eventos (callbacks disparados por eventos reativos)

    * watch
    * Eventos do Ciclo de Vida (na ordem em que são chamados)
        * beforeCreate
        * created
        * beforeMount
        * mounted
        * beforeUpdate
        * updated
        * activated
        * deactivated
        * beforeDestroy
        * destroyed


10. Propriedades não reativas (propriedades de instância independentes do sistema de reatividade)

    * methods
11. Renderização (a descrição declarativa da saída do componente)

    * template/render
    * renderError

```js
//Exemplo de usabilidade

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
```

## Linhas vazias em opções de componentes/instâncias

  Como método a facilitar a legibilidade do código

```js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue: function () {
    // ...
  },

  inputClasses: function () {
    // ...
  }
}
```

## Ordem dos elementos de nível superior do componente single-file
Componentes single-file devem sempre ordenar as tags.

```html
<template>... </template>
<script>/* ... */</script>
<style>/* ... */</style>
```

## Passar info de filho para pai

```js
//filho
this.$emit(teste) //passar info pai



//pai
<
@test="meuMetodo"
>

methods:
  meuMetodo(text){
    alert(texttttt)
  }
```