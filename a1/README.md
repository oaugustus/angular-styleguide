# Guia de Estilo AngularJS

## Aprovado pelo Time do Angular
Agradecimentos especiais a Igor Minar, lider no time do Angular, por revisar, contribuir com feedbacks, e confiar em mim para pastorear este guia. 

## Propósito
*Guia de estilo Angular para equipes [@john_papa](//twitter.com/john_papa)*

Se você está procurando por um guia de estilo para sintaxe, convenções, e estruturação de aplicações AngularJS, então está no passo certo. Esses estilos são baseados em minha experiência com [AngularJS](//angularjs.org), apresentações, [Cursos de treinamento na Pluralsight](http://app.pluralsight.com/author/john-papa)e trabalhando com equipes.

O propósito deste guia de estilo é fornecer uma orientação na construção de aplicações Angular, mostrando as convenções que eu uso e, mais importante, porque ela foi escolhida.

>Se você gostar deste guia, confira o meu curso na Pluralsight [Angular Patterns: Clean Code](http://jpapa.me/ngclean) que é um complemento deste guia.

  [![Angular Patterns: Clean Code](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/ng-clean-code-banner.png)](http://jpapa.me/ngclean)

## Comunidade incrível e crédidos
Nunca trabalhe no vácuo. Eu penso que a comunidade AngularJS é um grupo incrível que é apaixonado por compartilhar suas experiências. Como tal, o expert AngularJS Todd Motto e eu colaboramos em muitos estilos e convenções. Nós concordamos na maioria deles, e em alguns, nós divergimos. Eu enconrajo você a conferir [O guia do Todd](https://github.com/toddmotto/angular-styleguide) para ter uma noção dessa abordagem e como compará-las.


Muitos dos meus estilos vieram de muitas seções de programação em par que eu tive com [Ward Bell](https://twitter.com/wardbell). Mey amigo Ward certamente me influenciou na evolucão final deste guia.


## Veja os estilos em uma aplicação de exemplo
Enquanto este guia explica o *o que*, *porque* e *como*, eu acho que  muito útil ver isso na prática. Este guia é acomanhado por uma aplicação de exemplo que segue esses estilos e padrões. Você pode encontrar  [a aplicação de exemplo (chamada modular) aqui](https://github.com/johnpapa/ng-demos) na pasta `modular`. Sinta-se livre para pegá-lo, cloná-lo, ou fazer o fork. [Instruções na execução estão neste readme](https://github.com/johnpapa/ng-demos/tree/master/modular).

## Traduções

[Traduções deste guia de estilo do AngularJS](https://github.com/johnpapa/angular-styleguide/tree/master/a1/i18n) são mantidadas pela comunidade e podem ser encontradas aqui.

## Indice

1. [Responsabilidade unica](#responsabilidade-unica)
1. [IIFE](#iife)
1. [Modulos](#modulos)
1. [Controladores](#controladores)
1. [Serviços](#servicos)
1. [Factories](#factories)
1. [Serviços de dados](#servicos-de-dados)
  1. [Directives](#directives)
  1. [Resolving Promises for a Controller](#resolving-promises-for-a-controller)
  1. [Manual Annotating for Dependency Injection](#manual-annotating-for-dependency-injection)
  1. [Minification and Annotation](#minification-and-annotation)
  1. [Exception Handling](#exception-handling)
  1. [Naming](#naming)
  1. [Application Structure LIFT Principle](#application-structure-lift-principle)
  1. [Application Structure](#application-structure)
  1. [Modularity](#modularity)
  1. [Angular $ Wrapper Services](#angular--wrapper-services)
  1. [Testing](#testing)
  1. [Animations](#animations)
  1. [Comments](#comments)
  1. [JSHint](#js-hint)
  1. [Constants](#constants)
  1. [File Templates and Snippets](#file-templates-and-snippets)
  1. [Angular Docs](#angularjs-docs)
  1. [Contributing](#contributing)
  1. [License](#license)


## Responsabilidade unica

### Regra do 1
###### [Estilo [Y001](#style-y001)]

  - Defina 1 componente por arquivo, preferenciamente tendo menos que 400 linhas de código.

  *Por que?*: Um componente por arquivo promove uma unidade de teste e mocking mais fácil.

  *Por que?*: Um componente por arquivo facilita a legibilidade, manutenção, e evita colisões com equipes no controle do código fonte.

  *Por que?*: Um componente por arquivo evita bugs ocultos que frequentemente surgem ao combinar componentes em um arquivo onde eles podem compartilhar variáveis, criar clousures indesejadas, ou acomplamento indesejáveis com dependências.

  O seguinte exemplo define um módulo `app` e suas dependências, define um controlador, e define uma factory, tudo num mesmo arquivo.

  ```javascript
  /* evite */
  angular
      .module('app', ['ngRoute'])
      .controller('SomeController', SomeController)
      .factory('someFactory', someFactory);

  function SomeController() { }

  function someFactory() { }
  ```

  Os mesmos componentes agora esto separados em seus próprios arquivos.

  ```javascript
  /* recomendado */

  // app.module.js
  angular
      .module('app', ['ngRoute']);
  ```

  ```javascript
  /* recomentado */

  // some.controller.js
  angular
      .module('app')
      .controller('SomeController', SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* recomendado */

  // some.factory.js
  angular
      .module('app')
      .factory('someFactory', someFactory);

  function someFactory() { }
  ```

**[Voltar ao topo](#indice)**

### Pequenas funções
###### [Estilo [Y002](#style-y002)]

  - Defina pequenas funções, no mais do que 75 LOC (menos é melhor).

  *Por que?*: Pequenas funções so mais fáceis de testar, especialmente quando elas faze, uma coisa e servem para um único propósito.

  *Por que?*: Pequenas funções promovem reuso.

  *Por que?*: Pequenas funções são fáceis de ler.

  *Por que?*: Pequenas funções são mais fáceis de manter.

  *Por que?*: Pequenas funções ajudam a evitar bugs ocultos que surgem com funções grandes que compartilham variáveis com escopo externo, cria clouseres indesejadas, ou acomplamento com dependências indesejáveis.

**[Voltar ao topo](#indice)**

## IIFE
### Escopos Javascript
###### [Style [Y010](#style-y010)]

  - Envolva os componentes angular em uma Expresso de Função Imediatamente Invocada(IIFE).

  *Por que?*: Uma IIFE remove variáveis do escopo global. Isso ajuda a previnir declaração de funções e variáveis de viverem mais do que o esperado no escopo global, que também ajuda a evitar as colisões de variáveis.

  *Por que?*: Quando seu código é minificado e empacotado em um único arquivo para implantação em um servidor de produção, você pode ter colisões de muitas variáveis globais. Uma IIFE protege você contra ambas as situações fornecendo um escopo de variável para cada arquivo.
  
  ```javascript
  /* evite */
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  // a função logger é adicionada como uma variável de escopo global
  function logger() { }

  // storage.js
  angular
      .module('app')
      .factory('storage', storage);

  // a funço storage é adicionada como uma variável de escopo global
  function storage() { }
  ```

  ```javascript
  /**
   * recomendado
   *
   * nenhuma global deixada para trás
   */

  // logger.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('logger', logger);

      function logger() { }
  })();

  // storage.js
  (function() {
      'use strict';

      angular
          .module('app')
          .factory('storage', storage);

      function storage() { }
  })();
  ```

  - Nota: Por questões de rapidez, o resto dos exemplos deste guia podem omitir a sintaxe IIFE.

  - Nota: IIFE's não permite o teste de código de acessar métodos privados como uma exressão regular ou funções utilitárias que so frequentemente boas unidades para ter seus próprios testes. Entretanto, voc pode testar esses através de membros acessíveis ou expondo-os através de seu próprio componente. Por exemplo, colocar funções utilitárias, expressões regulares ou constantes em sua própria factory ou constante.

**[Voltar ao topo](#indice)**

## Modulos

### Evite colisões de nome
###### [Estilo [Y020](#estilo-y020)]

  - Use convenções de nomes com separadores para sub-módulos.

  *Por que?*: Nomes unicos ajudam a evitar colisão de nomes de módulos. Separadores ajudam a definir módulos e suas hierarquias. Por exemplo, `app` pode ser um módulo raiz enquanto `app.dashboard` e `app.users` podem ser módulos que são usados como dependências de `app`.

### Definições (mais conhecidos como Setters)
###### [Estilo [Y021](#estilo-y021)]

  - Declare módulos sem uma variável usando a sintaxe de setter.

  *Por que?*: Com 1 componente por arquivo, é raramente necessário indtroduzir uma variável para o módulo.

  ```javascript
  /* evite */
  var app = angular.module('app', [
      'ngAnimate',
      'ngRoute',
      'app.shared',
      'app.dashboard'
  ]);
  ```

  Ao invés, use a sintaxe simples de setter.

  ```javascript
  /* recomendado */
  angular
      .module('app', [
          'ngAnimate',
          'ngRoute',
          'app.shared',
          'app.dashboard'
      ]);
  ```

### Getters
###### [Estilo [Y022](#estilo-y022)]

  - Ao usar um módulo, evite usar uma variável e ao invés disso, utilize encadeamento com a sintaxe getter.

  *Por que?*: Isso produz um código mais legível, e evita colisões de variáveis ou vazamentos.

  ```javascript
  /* evite */
  var app = angular.module('app');
  app.controller('SomeController', SomeController);

  function SomeController() { }
  ```

  ```javascript
  /* recomendado */
  angular
      .module('app')
      .controller('SomeController', SomeController);

  function SomeController() { }
  ```

### Setting vs Getting
###### [Estilo [Y023](#estilo-y023)]

  - Use set apenas uma vez e get para todas as outras instâncias.

  *Por que?*: Um módulo deve ser criado apenas uma vez, e então recuperado a partir deste ponto e adiante.

  ```javascript
  /* recomendado */

  // para setar um módulo
  angular.module('app', []);

  // para pegar um módulo
  angular.module('app');
  ```

### Funções nomeadas vs anônimas
###### [Estilo [Y024](#style-y024)]

  - Use funções nomeadas ao invés de passar funções anônimas como um callback.

  *Por que?*: Isso produz um código mais legível, é muito mais fácil e debugar e reduz a quantidade de código de callback encadeado.

  ```javascript
  /* evite */
  angular
      .module('app')
      .controller('DashboardController', function() { })
      .factory('logger', function() { });
  ```

  ```javascript
  /* recomendado */

  // dashboard.js
  angular
      .module('app')
      .controller('DashboardController', DashboardController);

  function DashboardController() { }
  ```

  ```javascript
  // logger.js
  angular
      .module('app')
      .factory('logger', logger);

  function logger() { }
  ```

**[Voltar ao topo](#indice)**

## Controladores

### Sintaxe view controllerAs
###### [Estilo [Y030](#estilo-y030)]

  - Use a sintaxe [`controllerAs`](http://www.johnpapa.net/do-you-like-your-angular-controllers-with-or-without-sugar/) ao invés da sintaxe `controlador clássico com $scope`.

  *Por que?*: Controladores são construídos, novos, e forneça uma única instância, e a sintaxe `controllerAs` é mais próximo de um construtor Javascript do que `a sintaxe clássica de $scope`.

  *Por que?*: Isso promove o reuso de vinculação com um objeto "apontado" na view (exemplo `customer.name` ao invés de `name`), que é muito mais contextual, fácil de ler, e evita qualquer questões de referência que podem ocorrer sem um "apontamento".

  *Por que?*: Ajuda a evitar o uso de chamadas `$parent`nas views com controladores encadeados.

  ```html
  <!-- evite -->
  <div ng-controller="CustomerController">
      {{ name }}
  </div>
  ```

  ```html
  <!-- recomendado -->
  <div ng-controller="CustomerController as customer">
      {{ customer.name }}
  </div>
  ```

### Sintaxe de controlador controllerAs
###### [Estilo [Y031](#estilo-y031)]

  - Use a sintaxe `controllerAs` ao invés da sintaxe `controlador clássico com $scope`.

  - A sintaxe `controllerAs` usa o `this` dentro dos controladores para ter acesso ao `$scope`

  *Por que?*: `controllerAs` é sintaticamente mais suave que `$scope`. Você ainda pode vincular a view e ter acesso aos métodos do `$scope` .

  *Por que?*: Ajuda a evitar a tentação de utilizar métodos de `$scope` dentro de um controlador quando isso pode ser feito de outra forma melhor para evitá-los ou mover o método para uma factory, e referenciá-lo de um controlador. Considere utilizar `$scope` em um controlador apenas quando necessário. Por exemplo ao publicar e subscrever para eventos usando [`$emit`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$emit), [`$broadcast`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$broadcast), or [`$on`](https://docs.angularjs.org/api/ng/type/$rootScope.Scope#$on).

  ```javascript
  /* evite */
  function CustomerController($scope) {
      $scope.name = {};
      $scope.sendMessage = function() { };
  }
  ```

  ```javascript
  /* recomendado - mas veja a próxima seção */
  function CustomerController() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

### controllerAs com vm
###### [Estilo [Y032](#estilo-y032)]

  - Use uma variável para capturar o `this` ao utilizar a sintaxe `controllerAs`. Escolha um nome de variável consistente assim como `vm`, que significa ViewModel.

  *Por que?*: A palavra chave `this` é contextual e quando usada dentro de uma função dentro de um controlador pode ter seu contexto modificado. Captuar o contexto de `this` evita encontrar esse tipo de problema.

  ```javascript
  /* evite */
  function CustomerController() {
      this.name = {};
      this.sendMessage = function() { };
  }
  ```

  ```javascript
  /* recomendado */
  function CustomerController() {
      var vm = this;
      vm.name = {};
      vm.sendMessage = function() { };
  }
  ```

  Nota: Você pode evitar qualquer alerta do [jshint](http://jshint.com/) colocando um comentário acima da linha de código. Entretanto isso no é necessário quando a função é nomeada usando UpperCasing, como esta convenção siginifica que ela é o construtor da função, que é o que o controlador é no AngularJS.

  ```javascript
  /* jshint validthis: true */
  var vm = this;
  ```

  Nota: Ao criar observadores ($watch) com a sintaxe `controller as`, você pode observar o membro `vm.*` usando a seguinte sintaxe. (Crie observadores com caultela uma vez que eles adicionam mais carregamento no ciclo de digest.)

  ```html
  <input ng-model="vm.title"/>
  ```

  ```javascript
  function SomeController($scope, $log) {
      var vm = this;
      vm.title = 'Some Title';

      $scope.$watch('vm.title', function(current, original) {
          $log.info('vm.title was %s', original);
          $log.info('vm.title is now %s', current);
      });
  }
  ```

  Nota:Ao trabalhar com grandes códigos base, usar um nome mais descritivo pode ajudar na pesquisa cognitiva e na localização. Evite nomes exagerados que so difíceis de digitar.

  ```html
  <!-- evite -->
  <input ng-model="customerProductItemVm.title">
  ```

  ```html
  <!-- recomendado -->
  <input ng-model="productVm.title">
  ```

### Membros vinculáveis no topo
###### [Style [Y033](#style-y033)]

  - Coloque membros vinculáveis no topo do controlador, em ordem alfabética, e não espalhados ao longo do código do controlador.

    *Por que?*: Colocar membros vinculáveis no topo torna fácil ler e ajuda você a instantaneamente identificar quais membros do controlador podem ser vinculados e usados na View.

    *Por que?*: Definir funções anônimas in-line pode ser fácil, mas quando essas funções são maiores do que 1 linha de código elas podem reduzir a legibilidade. Definindo funções abaixo dos membros vinculáveis (as funções serão içadas) movendo os detalhes de implementaço para baixo, mantenha os membros vinculáveis no topo, isso facilita a leitura.

  ```javascript
  /* evite */
  function SessionsController() {
      var vm = this;

      vm.gotoSession = function() {
        /* ... */
      };
      vm.refresh = function() {
        /* ... */
      };
      vm.search = function() {
        /* ... */
      };
      vm.sessions = [];
      vm.title = 'Sessions';
  }
  ```

  ```javascript
  /* recomentado */
  function SessionsController() {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = refresh;
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';

      ////////////

      function gotoSession() {
        /* */
      }

      function refresh() {
        /* */
      }

      function search() {
        /* */
      }
  }
  ```

![Controlador usando "Declaraço acima"](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/above-the-fold-1.png)

  Nota: Se a função possui 1 linha de código, considere mantê-la no topo, uma vez que não irá afetar a legibilidade.

  ```javascript
  /* evite */
  function SessionsController(data) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = function() {
          /**
           * lines
           * of
           * code
           * affects
           * readability
           */
      };
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  }
  ```

  ```javascript
  /* recomendado */
  function SessionsController(sessionDataService) {
      var vm = this;

      vm.gotoSession = gotoSession;
      vm.refresh = sessionDataService.refresh; // 1 liner is OK
      vm.search = search;
      vm.sessions = [];
      vm.title = 'Sessions';
  }
  ```

### Declaração de funções para esconder detalhes de implementação
###### [Estilo [Y034](#estilo-y034)]

  - Use declaração de funções para esconder detalhes de implementação. Mantenha seus membros vinculáveis no topo. Quando você precisar vincular uma função em um controlador, aponte-a para uma declaração de função que aparece depois no arquivo. Isso está vinculado diretamente à seção de membros vinculáveis no topo. Para maiores detalhes veja [este post](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code/).

    *Por que?*: Colocar membros vinculáveis no topo torna fácil a leitura e ajuda você a instantaneamente identificar quais membros do controlador podem ser vinculados e usados em uma View. (Assim como acima.)

    *Por que?*: Colocar os detalhes de implementação de uma função no final do arquivo move a sua complexidade para fora da visão, assim você pode ver as coisas importantes no topo.

    *Por que?*: Declaração de funções são içadas assim no há preocupação de usar uma função antes que ela seja definida (como haveria com as expressões de função).

    *Por que?*: Você nunca terá que se preocupar que declarações de funções que movimentam `var a` antes de `var b` irão quebrar seu código por que `a` depende de `b`.

    *Por que?*: Ordem é crítica com expressões de função

  ```javascript
  /**
   * evite
   * Usar expressões de função.
   */
  function AvengersController(avengersService, logger) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      var activate = function() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      var getAvengers = function() {
          return avengersService.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }

      vm.getAvengers = getAvengers;

      activate();
  }
  ```

  Observe que as coisas importantes estão espalhadas no exemplo anterior. No exemplo abaixo, observe que as coisas importantes estão no topo. Por exemplo, os membros vinculádos ao controlador tais como `vm.avengers` e `vm.title`. Os detalhes de implementação estão na parte de baixo. Isso é apenas muito mais fácil de ler.

  ```javascript
  /*
   * recomendado
   * Usar declarações de funções
   * e membros vinculáveis no topo.
   */
  function AvengersController(avengersService, logger) {
      var vm = this;
      vm.avengers = [];
      vm.getAvengers = getAvengers;
      vm.title = 'Avengers';

      activate();

      function activate() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      function getAvengers() {
          return avengersService.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Designe lógicas do controlador para Serviços
###### [Estilo [Y035](#estilo-y035)]

  - Designe lógica em um controlador ao delegá-las para serviços de factories.

    *Por que?*: Lógica pode ser reutilizada por múltiplos controladores quando colocadas dentro de um serviço e exposta via função.

    *Por que?*: Lógica em um serviço pode ser mais facilmente isolada em um teste unitário, enquanto a chamada da lógica no controlador pode ser facilmente simulada.

    *Por que?*: Remova dependências e esconda detalhes de implementação do controlador.

    *Por que?*: Mantenha o controlador magro, aparado e focado.

  ```javascript

  /* evite */
  function OrderController($http, $q, config, userInfo) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.isCreditOk;
      vm.total = 0;

      function checkCredit() {
          var settings = {};
          // Pega a URL base do serviço de cartão de credido de uma configuração
          // Seta os cabeçalhos obrigatórios do serviço de cartão de crédito
          // Prepara a Query String da URL ou objeto de dados com os dados da requisição
          // Adiciona a identificação do usuário assim o servio pega o limite correto do cartão de crédido para este usurio.
          // Usa JSONP para este navegador se ele no suportar CORS
          return $http.get(settings)
              .then(function(data) {
               // Desempacota os dados JSON no objeto de resposta
                 // para encontrar maxRemainingAmount
                 vm.isCreditOk = vm.total <= maxRemainingAmount
              })
              .catch(function(error) {
                 // Interpreta o erro
                 // Cope w/ timeout? retry? try alternate service?
                 // Rejeita com a mensagem apropriada para o usuário
              });

      };
  }
  ```

  ```javascript
  /* recomendado */
  function OrderController(creditService) {
      var vm = this;
      vm.checkCredit = checkCredit;
      vm.isCreditOk;
      vm.total = 0;

      function checkCredit() {
         return creditService.isOrderTotalOk(vm.total)
            .then(function(isOk) { vm.isCreditOk = isOk; })
            .catch(showError);
      };
  }
  ```

### Mantenha os controladores focados
###### [Estilo [Y037](#estilo-y037)]

  - Defina um controlador para uma view, e tente não reutilizar o controlador para outras views. Ao invés disso, mova as lógicas reutilizáveis para factories e mantenha o controlador simples e focado na sua própria view.

    *Por que?*: Reutilizar controladores em muitas é frágil um bom teste de ponto a ponto será necessário para certificar da estabilidade ao longo de grandes aplicações.

### Atribuição de controladores
###### [Estilo [Y038](#estilo-y038)]

  - Quando um controlador precisa ser pareado com uma view e o componente pode ser reutilizado por outros controladores ou views, defina controladores com suas próprias rotas.

    Nota: Se uma view é carregada por outro meio além de uma rota, então use a sintaxe `ng-controller="Avengers as vm"`.

    *Por que?*: Para o controlador com uma rota permite que diferentes rotas invoquem diferentes pares de controladores e views. Quando controladores são atribuídos a uma view usando [`ng-controller`](https://docs.angularjs.org/api/ng/directive/ngController), esta view será sempre associada com o mesmo controlador.

 ```javascript
  /* evite - ao usar com uma rota, o emparelhamento dinâmico é desejável */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
            templateUrl: 'avengers.html'
          });
  }
  ```

  ```html
  <!-- avengers.html -->
  <div ng-controller="AvengersController as vm">
  </div>
  ```

  ```javascript
  /* recomendado */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'Avengers',
              controllerAs: 'vm'
          });
  }
  ```

  ```html
  <!-- avengers.html -->
  <div>
  </div>
  ```

**[Voltar ao topo](#indice)**

## Servicos

### Singletons
###### [Estilo [Y040](#estilo-y040)]

  - Serviços são instanciados com a palavra chave `new`, use `this` para métodos e variáveis públicas. Uma vez que eles são similares a factories, utilize uma factory em seu lugar para ter consistência.

    Nota: [Todos os serviços em AngularJS são singletons](https://docs.angularjs.org/guide/services). Isso significa que há apenas uma instância de um dado serviço por injetor.

  ```javascript
  // serviço
  angular
      .module('app')
      .service('logger', logger);

  function logger() {
    this.logError = function(msg) {
      /* */
    };
  }
  ```

  ```javascript
  // factory
  angular
      .module('app')
      .factory('logger', logger);

  function logger() {
      return {
          logError: function(msg) {
            /* */
          }
     };
  }
  ```

**[Voltar ao topo](#indice)**

## Factories

### Responsabilidade única
###### [Estilo [Y050](#estilo-y050)]

  - Factories devem ter uma [responsabilidade única](https://en.wikipedia.org/wiki/Single_responsibility_principle), isso é encapsulado pelo seu contexto. Uma vez que uma factory começa a exceder o seu propósito únic, uma nova factory deverá ser crida.

### Singletons
###### [Style [Y051](#style-y051)]

  - Factories são singletons e retornam um objeto que contém os membros do serviço.

    Nota: [Todos os serviços são singletons](https://docs.angularjs.org/guide/services).

### Membros acessíveis no topo
###### [Estilo [Y052](#estilo-y052)]

  - Exponha os membros chamáveis do serviço (sua interface) no topo, usando uma técnica derivada [Padrão do módulo de revelação](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript).

    *Por que?*: Colocar membros chamáveis no topo torna fácil a leitura e ajuda você a instantaneamente identificar quais membros do serviço podem ser chamados e precisam ter testes unitrios (ou simulações).

    *Por que?*: Isso é especialmente útil quando o arquivo se torna grande uma vez que ele ajuda a evitar a rolagem para ver o que está exposto.

    *Por que?*: Definir funções à medida que você precisa pode ser fácil, mas quando essas funções possuem mais de uma linha de código elas podem reduzir a legibilidade e causar mais rolagem. Definir a interface chamável via os serviço retornado movendo os detalhes de implementaço para baixo, mantém a interface chamável no topo, e torna a leitura mais fácil.

  ```javascript
  /* evite */
  function dataService() {
    var someValue = '';
    function save() {
      /* */
    };
    function validate() {
      /* */
    };

    return {
        save: save,
        someValue: someValue,
        validate: validate
    };
  }
  ```

  ```javascript
  /* recomendado */
  function dataService() {
      var someValue = '';
      var service = {
          save: save,
          someValue: someValue,
          validate: validate
      };
      return service;

      ////////////

      function save() {
          /* */
      };

      function validate() {
          /* */
      };
  }
  ```

  Desta forma, as ligações são espelhadas em todo o objeto host, os valores primitivos não podem ser atualizados sozinhos usando o padrão de módulo revelador.

![Factories usando "Declaraço acima"](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/above-the-fold-2.png)

### Declaração de funções para esconder detalhes de implementação
###### [Estilo [Y053](#estilo-y053)]

  - Use declarações de função para esconder detalhes de implementação. Mantenha seus membros acessíveis no topo da factory. Aponte eles para declarações de função que aparecem no final do arquivo. Para mais detalhes veja [este post](http://www.johnpapa.net/angular-function-declarations-function-expressions-and-readable-code).

    *Por que?*: Colocar membros acessíveis no topo, facilita a leitura e ajuda você a instantaneamente identificar quais funções da factory você pode acessar externamente.

    *Por que?*: Colocar os detalhes da implementação de uma função no final do arquivo move sua complexidade para fora da visão assim você pode ver as coisas importantes no topo.

    *Por que?*: Declarações de função são içadas assim no há preocupação sobre utilizar uma função antes que ela seja definida (como você teria com expressões de função).

    *Por que?*: Voc nunca precisará se preocupar que declarações de função que movimentam `var a` antes de `var b` quebrem seu código porque `a` depende de `b`.

    *Por que?*: Ordem é crítico com expressões de função

  ```javascript
  /**
   * evite
   * Usar expressões de função
   */
   function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var getAvengers = function() {
          // implementation details go here
      };

      var getAvengerCount = function() {
          // implementation details go here
      };

      var getAvengersCast = function() {
         // implementation details go here
      };

      var prime = function() {
         // implementation details go here
      };

      var ready = function(nextPromises) {
          // implementation details go here
      };

      var service = {
          getAvengersCast: getAvengersCast,
          getAvengerCount: getAvengerCount,
          getAvengers: getAvengers,
          ready: ready
      };

      return service;
  }
  ```

  ```javascript
  /**
   * recomendado
   * Usar declaração de funções 
   * e membros acessíveis no topo.
   */
  function dataservice($http, $location, $q, exception, logger) {
      var isPrimed = false;
      var primePromise;

      var service = {
          getAvengersCast: getAvengersCast,
          getAvengerCount: getAvengerCount,
          getAvengers: getAvengers,
          ready: ready
      };

      return service;

      ////////////

      function getAvengers() {
          // implementation details go here
      }

      function getAvengerCount() {
          // implementation details go here
      }

      function getAvengersCast() {
          // implementation details go here
      }

      function prime() {
          // implementation details go here
      }

      function ready(nextPromises) {
          // implementation details go here
      }
  }
  ```

**[Voltar ao topo](#indice)**

## Servicos de dados

### Separe chamadas de dados
###### [Style [Y060](#style-y060)]

  - Refatore a lógica para atribuir operações de dados e interação com dados para uma factory. Faa os serviços de dados responsáveis por chamadas XHR, armazenamento local, dados em memória, ou qualquer outra operação de dados.

    *Por que?*: A responsabilidade do controlador é para a apresentação e agrupamento de informações para a view. Ela não deve se preocupar sobre como pegar os dados, ela sabe apenas a quem perguntar por eles. Separar os serviços de dados move a lógica de como pegá-los para o serviço de dados, e deixa o controlador ser mais simples e mais focado na view.

    *Por que?*: Isso torna mais fácil testar (simulado ou real) a chamada de dados ao testar um controlador que usa um serviço de dados.

    *Por que?*: Implementação de serviços de dados podem ter códigos muito específicos para manipular um repositório de dados. Isso pode incluír cabeçalhos, como solicitar os dados, ou outros serviços tais como `$http`. Separar a lógica em um serviço de dados encapsula essa lógica em um lugar único escondendo a implementação dos consumidores de fora (talvez um controlador, também torna mais fácil mudar a implementação.

  ```javascript
  /* recomendado */

  //  factory de um serviço de dados
  angular
      .module('app.core')
      .factory('dataservice', dataservice);

  dataservice.$inject = ['$http', 'logger'];

  function dataservice($http, logger) {
      return {
          getAvengers: getAvengers
      };

      function getAvengers() {
          return $http.get('/api/maa')
              .then(getAvengersComplete)
              .catch(getAvengersFailed);

          function getAvengersComplete(response) {
              return response.data.results;
          }

          function getAvengersFailed(error) {
              logger.error('XHR Failed for getAvengers.' + error.data);
          }
      }
  }
  ```

Nota: O serviço de dados é chamado por consumidores, tais como um controlador, escondendo a implementaço do consumidor como é mostrado abaixo.

  ```javascript
  /* recomendado */

  // controlador chamadno uma factory de um serviço de dados
  angular
      .module('app.avengers')
      .controller('AvengersController', AvengersController);

  AvengersController.$inject = ['dataservice', 'logger'];

  function AvengersController(dataservice, logger) {
      var vm = this;
      vm.avengers = [];

      activate();

      function activate() {
          return getAvengers().then(function() {
              logger.info('Activated Avengers View');
          });
      }

      function getAvengers() {
          return dataservice.getAvengers()
              .then(function(data) {
                  vm.avengers = data;
                  return vm.avengers;
              });
      }
  }
  ```

### Retorne uma promessa para as chamadas de Dados
###### [Estilo [Y061](#estilo-y061)]

  - Ao chamar um serviço de dados que retorna uma promessa assim como `$http`, retorne uma promessa na sua chamada de função também.

    *Por que?*: Você pode encadear as promessas juntas e tomar uma ação futura depois que a chamada de dados resolve ou rejeita a promessa.

  ```javascript
  /* recomendado */

  activate();

  function activate() {
      /**
       * Passo 1
       * Chama a função getAvengers para o 
       * dados do vingador e espera pela promessa
       */
      return getAvengers().then(function() {
          /**
           * Passo 4
           * Executa uma ação na resolução do final da promessa
           */
          logger.info('Activated Avengers View');
      });
  }

  function getAvengers() {
        /**
         * Passo 2
         * Chama o serviço de dados pelos dados e espera pela promessa
         */
        return dataservice.getAvengers()
            .then(function(data) {
                /**
                 * Passo 3
                 * define os dados e resolve a promessa
                 */
                vm.avengers = data;
                return vm.avengers;
        });
  }
  ```

**[Voltar ao topo](#indice)**

## Diretivas
### Limite de 1 diretiva por arquivo
###### [Estilo [Y070](#estilo-y070)]

  - Crie uma diretiva por arquivo. Nome do arquivo é o nome da diretiva.

    *Por que?*: É fácil colocar todas as diretivas em um arquivo, mas dificulta dividí-las para usar em algumas aplicações, alguns módulos, ou apenas para um módulo específico.

    *Por que?*: Uma diretiva por arquivo é fácil de manter.

    > Nota: "**Melhor prática**: Diretivas devem limpar a si mesmas. Você pode usar `element.on('$destroy', ...)` ou `scope.$on('$destroy', ...)` para executar uma função de limpeza quando a diretiva é removida " ... da documentação do AngularJS.

  ```javascript
  /* evite */
  /* diretivas.js */

  angular
      .module('app.widgets')

      /* diretiva de pedido que é especfica do módulo de pedidos order directive that is specific to the order module */
      .directive('orderCalendarRange', orderCalendarRange)

      /* diretiva de vendas que pode ser usada em qualquer lugar na aplicaço de vendas */
      .directive('salesCustomerInfo', salesCustomerInfo)

      /* diretiva spinner que pode ser usada em qualquer lugar em qualquer aplicação */
      .directive('sharedSpinner', sharedSpinner);

  function orderCalendarRange() {
      /* detalhes de implementação */
  }

  function salesCustomerInfo() {
      /* detalhes de implementação */
  }

  function sharedSpinner() {
      /* detalhes de implementação */
  }
  ```

  ```javascript
  /* recomendado */
  /* calendar-range.directive.js */

  /**
   * @desc Diretiva de pedido que é especfica do módulo de pedidos da empresa chamada Acme
   * @example <div acme-order-calendar-range></div>
   */
  angular
      .module('sales.order')
      .directive('acmeOrderCalendarRange', orderCalendarRange);

  function orderCalendarRange() {
      /* Detalhes de implementação */
  }
  ```

  ```javascript
  /* recomendado */
  /* customer-info.directive.js */

  /**
   * @desc Diretivas de venda que pode ser usada em qualquer lugar na aplicaço de vendas da empresa chamada Acme
   * @example <div acme-sales-customer-info></div>
   */
  angular
      .module('sales.widgets')
      .directive('acmeSalesCustomerInfo', salesCustomerInfo);

  function salesCustomerInfo() {
      /* implementation details */
  }
  ```

  ```javascript
  /* recomendado */
  /* spinner.directive.js */

  /**
   * @desc Diretiva spinner que pode ser usada em qualquer lugar de qualquer aplicação da empresa Acme
   * @example <div acme-shared-spinner></div>
   */
  angular
      .module('shared.widgets')
      .directive('acmeSharedSpinner', sharedSpinner);

  function sharedSpinner() {
      /* implementation details */
  }
  ```

Nota: Há muitas opções de nome para diretivas, especialmente uma vez que elas podem ser usadas num escopo limitado ou amplo. Escolha um que faz a diretiva e seu nome de arquivo serem distintos e claros. Alguns exemplos abaixo, mas veja a seção [Nomeação](#nomeacao) para maiores recomendações.

### Manipule DOM em uma Diretiva
###### [Estilo [Y072](#estilo-y072)]

  - Ao manipular a DOM diretamente, use uma diretiva. Se maneiras alternativas podem ser usadas tais como usar CSS para definir estilos ou os [serviços de animação](https://docs.angularjs.org/api/ngAnimate), template Angular, [`ngShow`](https://docs.angularjs.org/api/ng/directive/ngShow) ou [`ngHide`](https://docs.angularjs.org/api/ng/directive/ngHide), então use esses no lugar. Por exemplo, se a diretiva simplesmente esconde e mostra, use ngHide/ngShow.

    *Por que?*: Manipulaço DOM pode ser difícil de testar, debugar e há frequentemente maneiras melhores de fazer isso (e.g. CSS, animtações, templates)

### Forneça um prefixo de diretivas único
###### [Estilo [Y073](#estilo-y073)]

  - Forneça um prefixo pequeno, único e descritivo como `acmeSalesCustomerInfo` que pode ser declado no HTML como `acme-sales-customer-info`.

    *Por que?*: O prefixo único e curto identifica o contexto e origem da diretiva. Por exemplo um prefixo `cc-` pode indicar que a diretiva é parte de uma aplicaço CodeCamper enquanto `acme-` pode indicar que a diretiva é da empresa Acme.

    Nota: Evite o prefixo `ng-` uma vez que ele é reservado para as diretivas do Angular. Pesquisa diretivas usadas amplamente para evitar conflitos de nomeação, tais como `ion-` para o [Ionic Framework](http://ionicframework.com/).

### Restrinja a Elementos e Atributos
###### [Estilo [Y074](#estilo-y074)]

  - Ao criar uma diretiva que funciona como um elemento stand-alone, permita restringir a `E` (elemento customizado) e opcionalmente restrito a `A` (atributo customizado). Geralmente, se ele pode ser seu próprio controle, `E` é apropriado. O guia geral é permitir `EA` mas a tendêcia é implementar como um elemento quando é um componente autônomo e como um atributo quando ele melhora um elemento DOM existente.

    *Por que?*: Isto faz sentido.

    *Por que?*: Por mais que você possa permitir que a diretiva seja usada como uma classe, se a diretiva está realmente agindo como um elemento, faz mais sentido como um elemento ou como um atributo.

    Nota: EA é o default para Angular 1.3 +

  ```html
  <!-- evite -->
  <div class="my-calendar-range"></div>
  ```

  ```javascript
  /* avoid */
  angular
      .module('app.widgets')
      .directive('myCalendarRange', myCalendarRange);

  function myCalendarRange() {
      var directive = {
          link: link,
          templateUrl: '/template/is/located/here.html',
          restrict: 'C'
      };
      return directive;

      function link(scope, element, attrs) {
        /* */
      }
  }
  ```

  ```html
  <!-- recomendado -->
  <my-calendar-range></my-calendar-range>
  <div my-calendar-range></div>
  ```

  ```javascript
  /* recommended */
  angular
      .module('app.widgets')
      .directive('myCalendarRange', myCalendarRange);

  function myCalendarRange() {
      var directive = {
          link: link,
          templateUrl: '/template/is/located/here.html',
          restrict: 'EA'
      };
      return directive;

      function link(scope, element, attrs) {
        /* */
      }
  }
  ```

### Diretivas e ControllerAs
###### [Estilo [Y075](#estilo-y075)]

  - Use a sintaxe `controllerAs` com uma diretiva para ser consistente com o uso de `controller as` com os pares view e controlador.

    *Por que?*: Isso faz sentido e não é difícil.

    Nota: A diretiva abaixo mostra algumas das maneiras que voc pode usar o escopo dentro de um link e controladores da diretiva, usando controllerAs. Eu coloquei o template inline para manter tudo em um único lugar.

    Nota: A respeito da injeção de dependência, veja [Identifique as dependências manualmente](#anotacao-manual-para-injecao-de-dependencia).

    Nota: Observe que o controlador da diretiva está fora da clousure da diretiva. Este estilo elimina questões onde a injeção é criada com um código inalcançavel após um `return`.
    
    Nota: Hooks do Lifecycle foram introduzidos no Angular 1.5. Lógica de inicializaço que confia nos bindings que estão presentes devem ser colocadas no método $onInit() do controlador, que é sempre chamado depois dos bindings terem sido atribuídos.

  ```html
  <div my-example max="77"></div>
  ```

  ```javascript
  angular
      .module('app')
      .directive('myExample', myExample);

  function myExample() {
      var directive = {
          restrict: 'EA',
          templateUrl: 'app/feature/example.directive.html',
          scope: {
              max: '='
          },
          link: linkFunc,
          controller: ExampleController,
          // nota: Isso deve ser 'ExampleController' (o nome do controlador exportado, como string)
          // se a referência for feita há um controlador que está em um arquivo separado.
          controllerAs: 'vm',
          bindToController: true // porque o escopo está isolado
      };

      return directive;

      function linkFunc(scope, el, attr, ctrl) {
          console.log('LINK: scope.min = %s *** should be undefined', scope.min);
          console.log('LINK: scope.max = %s *** should be undefined', scope.max);
          console.log('LINK: scope.vm.min = %s', scope.vm.min);
          console.log('LINK: scope.vm.max = %s', scope.vm.max);
      }
  }

  ExampleController.$inject = ['$scope'];

  function ExampleController($scope) {
      // Injetando $scope apenas para comparação
      var vm = this;
      vm.min = 3;
      vm.$onInit = onInit;
      
      //////////
      
      console.log('CTRL: $scope.vm.min = %s', $scope.vm.min);
      console.log('CTRL: $scope.vm.max = %s', $scope.vm.max); // indefinido em Angular 1.5+
      console.log('CTRL: vm.min = %s', vm.min);
      console.log('CTRL: vm.max = %s', vm.max); // indefinido em Angular 1.5+
      
      // Angular 1.5+ não vincula atributos até que seja chamado o método $onInit();
      function onInit() {
          console.log('CTRL-onInit: $scope.vm.min = %s', $scope.vm.min);
          console.log('CTRL-onInit: $scope.vm.max = %s', $scope.vm.max);
          console.log('CTRL-onInit: vm.min = %s', vm.min);
          console.log('CTRL-onInit: vm.max = %s', vm.max);
      }
  }
  ```

  ```html
  <!-- example.directive.html -->
  <div>hello world</div>
  <div>max={{vm.max}}<input ng-model="vm.max"/></div>
  <div>min={{vm.min}}<input ng-model="vm.min"/></div>
  ```

Nota: Você pode também nomear o controlador quando você injetá-lo na função link e acessar os atributos da diretiva como propriedades do controlador.

  ```javascript
  // Alternativa ao exemplo acima
  function linkFunc(scope, el, attr, vm) {
      console.log('LINK: scope.min = %s *** deve ser indefinido', scope.min);
      console.log('LINK: scope.max = %s *** deve ser indefinido', scope.max);
      console.log('LINK: vm.min = %s', vm.min);
      console.log('LINK: vm.max = %s', vm.max);
  }
  ```

###### [Estilo [Y076](#estilo-y076)]

  - Use `bindToController = true` ao usar a sintaxe `controller as` com uma diretiva quando você quer vincular um escopo externo no escopo do controlador da diretiva.

    *Por que?*: Isso torna fácil a vinculação com um escopo externo com o escopo do controlador da diretiva.

    Nota: `bindToController` foi introduzido em Angular 1.3.0.

  ```html
  <div my-example max="77"></div>
  ```

  ```javascript
  angular
      .module('app')
      .directive('myExample', myExample);

  function myExample() {
      var directive = {
          restrict: 'EA',
          templateUrl: 'app/feature/example.directive.html',
          scope: {
              max: '='
          },
          controller: ExampleController,
          controllerAs: 'vm',
          bindToController: true
      };

      return directive;
  }

  function ExampleController() {
      var vm = this;
      vm.min = 3;
      vm.$onInit = onInit;
      
      function onInit() = {
          console.log('CTRL: vm.min = %s', vm.min);
          console.log('CTRL: vm.max = %s', vm.max);
      }
  }
  ```

  ```html
  <!-- example.directive.html -->
  <div>hello world</div>
  <div>max={{vm.max}}<input ng-model="vm.max"/></div>
  <div>min={{vm.min}}<input ng-model="vm.min"/></div>
  ```

**[Voltar ao topo](#indice)**

## Resolvendo promessas
### Ativação de promessas do controlador
###### [Style [Y080](#style-y080)]

  - Resolva lógicas de inicialização para um controlador na função `activate`.

    *Por que?*: Colocar lógica de inicialização em um lugar consistente no controlador torna mais fácil localizar, mais consistente de testar e ajuda a evitar espalhamento da lógica de ativação pelo controlador.

    *Por que?*: A função `activate` do controlador torna conveniente a reutilização da lógica para um recarregamento para um controlador/view, mantém a lógica junta, leva o usuário para a View mais rápido e torna as animações fáceis no `ng-view` ou `ui-view`.

    Nota: Se você precisa condicionalmente cancelar a rota antes que você comece a usar o controlador, use [resolvedor de rota](#estilo-y081).

  ```javascript
  /* evite */
  function AvengersController(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      dataservice.getAvengers().then(function(data) {
          vm.avengers = data;
          return vm.avengers;
      });
  }
  ```

  ```javascript
  /* recomendado */
  function AvengersController(dataservice) {
      var vm = this;
      vm.avengers = [];
      vm.title = 'Avengers';

      activate();

      ////////////

      function activate() {
          return dataservice.getAvengers().then(function(data) {
              vm.avengers = data;
              return vm.avengers;
          });
      }
  }
  ```

### Promessas de resolução de rotas
###### [Estilo [Y081](#estilo-y081)]

  - Quando um controlador depende que uma promessa seja resolvida antes do controlador ser ativado, resolva essas dependências no  `$routeProvider` antes que a lógica do controlador seja executada. Se você precisar condicionalmente de cancelar uma rota antes que o controlador seja ativado, use um route resolver.

  - Use um route resolve quando você quiser decidir cancelar a rota antes da sua transição para a View.

    *Por que?*: Um controlador pode requisitar dados antes que eles sejam carregados. Esses dados podem vir de uma promessa via uma factory customizada ou [$http](https://docs.angularjs.org/api/ng/service/$http). Usar um [route resolve](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider) permite que a promessa seja resolvida antes que o controlador execute a lógica, assim pode executar uma ação baseado nos dados que vieram da promessa.

    *Por que?*: O código executa depois de uma rota e na função de ativação do controlador. A View comea a carregar da forma correta. A vinculação de dados é rápida quando a promessa ativada se resolve. Uma animação “carregando” pode ser exibida durante a transiço da view(via `ng-view` or `ui-view`)

    Nota: O código executa antes da rota via promessa. Rejeitar uma promessa cancela a rota. Resolver faz a nova view esperar pela rota para resolver. Uma animação "carregando" pode ser mostrada antes da resolução e através da transição da view. Se você quer que a view seja renderizada mais rápido e não quer requisitar um ponto de checagem para decidir se você pode entrar na view, considere [técnica de `ativação` de controlador](#estilo-y080).

  ```javascript
  /* evite */
  angular
      .module('app')
      .controller('AvengersController', AvengersController);

  function AvengersController(movieService) {
      var vm = this;
      // unresolved
      vm.movies;
      // resolved asynchronously
      movieService.getMovies().then(function(response) {
          vm.movies = response.movies;
      });
  }
  ```

  ```javascript
  /* melhor */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'AvengersController',
              controllerAs: 'vm',
              resolve: {
                  moviesPrepService: function(movieService) {
                      return movieService.getMovies();
                  }
              }
          });
  }

  // avengers.js
  angular
      .module('app')
      .controller('AvengersController', AvengersController);

  AvengersController.$inject = ['moviesPrepService'];
  function AvengersController(moviesPrepService) {
      var vm = this;
      vm.movies = moviesPrepService.movies;
  }
  ```

Nota: O exemplo abaixo mostra a resolução de rota apontando para uma função nomeada, que é mais fácil de debugar e fácil de manipular injeção de dependência.

  ```javascript
  /* ainda melhor */

  // route-config.js
  angular
      .module('app')
      .config(config);

  function config($routeProvider) {
      $routeProvider
          .when('/avengers', {
              templateUrl: 'avengers.html',
              controller: 'AvengersController',
              controllerAs: 'vm',
              resolve: {
                  moviesPrepService: moviesPrepService
              }
          });
  }

  function moviesPrepService(movieService) {
      return movieService.getMovies();
  }

  // avengers.js
  angular
      .module('app')
      .controller('AvengersController', AvengersController);

  AvengersController.$inject = ['moviesPrepService'];
  function AvengersController(moviesPrepService) {
        var vm = this;
        vm.movies = moviesPrepService.movies;
  }
  ```
Nota: A dependência do exemplo no serviço `movieService` não é segura para minificação. Para detalhes de como fazer esse código seguro para minificação, veja as seções em [injeção de dependência](#anotacao-manual-para-injecao-de-dependencia) e em [anotação e minificação](#anotacao-e-minificacao).

**[Voltar ao topo](#indice)**

### Manipulando exceções com Promessas
###### [Estilo [Y082](#estilo-y082)]

  - O bloco `catch` de uma promessa precisa retornar uma promessa rejeitada para manter a exceção na sequencia da promessa.

  - Sempre manipule exceções nos serviços/factories.

    *Por que?*: Se o bloco `catch` não retorna uma promessa rejeitada, o acionador da promessa não irá saber que uma exceção ocorreu. O acionador irá executar o `then`. Desta forma, o usuário pode nunca saber o que aconteceu.

    *Por que?*: Para evitar a omissão de erros e falta de informação para o usuário.

    Nota: Considere colocar qualquer manipulação de exceção em uma função em um módulo e serviço compartilhado.

  ```javascript
  /* evite */

  function getCustomer(id) {
      return $http.get('/api/customer/' + id)
          .then(getCustomerComplete)
          .catch(getCustomerFailed);

      function getCustomerComplete(data, status, headers, config) {
          return data.data;
      }

      function getCustomerFailed(e) {
          var newMessage = 'XHR Failed for getCustomer'
          if (e.data && e.data.description) {
            newMessage = newMessage + '\n' + e.data.description;
          }
          e.data.description = newMessage;
          logger.error(newMessage);
          // ***
          // Observe que não h retorno da promessa rejeitada
          // ***
      }
  }
  ```

  ```javascript
  /* recomendado */
  function getCustomer(id) {
      return $http.get('/api/customer/' + id)
          .then(getCustomerComplete)
          .catch(getCustomerFailed);

      function getCustomerComplete(data, status, headers, config) {
          return data.data;
      }

      function getCustomerFailed(e) {
          var newMessage = 'XHR Failed for getCustomer'
          if (e.data && e.data.description) {
            newMessage = newMessage + '\n' + e.data.description;
          }
          e.data.description = newMessage;
          logger.error(newMessage);
          return $q.reject(e);
      }
  }
  ```

**[Voltar ao topo](#indice)**

## Anotacao manual para injecao de dependencia

### Inseguro de minificação
###### [Estilo [Y090](#estilo-y090)]

  - Evite usar a sintaxe reduzida de declaração de dependências sem usar uma abordagem segura para minificação.

    *Por que?*: Os parâmetros do componente (ex. controlador, factory, etc) serão convertidos para variáveis multiladas. Por exemplo, `common` e `dataservice` podem se tornar `a` ou `b` e não serem encontrados pelo Angular.

    ```javascript
    /* evite - não seguro para minificação*/
    angular
        .module('app')
        .controller('DashboardController', DashboardController);

    function DashboardController(common, dataservice) {
    }
    ```

    Este código pode produzir variáveis multiladas quando minificado e desta forma causar erros de execução.

    ```javascript
    /* evite - não seguro para minificação*/
    angular.module('app').controller('DashboardController', d);function d(a, b) { }
    ```

### Manualmente identifique as dependencias
###### [Estilo [Y091](#estilo-y091)]

  - Use `$inject` para manualmente identificar as dependências dos seus componentes Angular.

    *Por que?*: Esta técnica espelha a técnica usada pelo [`ng-annotate`](https://github.com/olov/ng-annotate), que eu recomendo para criação automática de minificação de dependências de forma segura. Se `ng-annotate` detecta que uma injeção já foi feita, ele não irá duplicá-la.

    *Por que?*: Isso irá proteger suas dependências de questões de vulnerabilidade quando os parâmetros podem ser multilados. Por exemplo, `common` e `dataservice` podem se tornar `a` e `b` e não serem encontradas no Angular.

    *Por que?*: Evite criar dependências in-line, uma vez que longas listas podem dificultar a leitura em um array. Também pode ser confuso um arrau como uma série de strings e o último item da lista ser a função do componente.

    ```javascript
    /* evite */
    angular
        .module('app')
        .controller('DashboardController',
            ['$location', '$routeParams', 'common', 'dataservice',
                function Dashboard($location, $routeParams, common, dataservice) {}
            ]);
    ```

    ```javascript
    /* evite */
    angular
      .module('app')
      .controller('DashboardController',
          ['$location', '$routeParams', 'common', 'dataservice', Dashboard]);

    function Dashboard($location, $routeParams, common, dataservice) {
    }
    ```

    ```javascript
    /* recomendado */
    angular
        .module('app')
        .controller('DashboardController', DashboardController);

    DashboardController.$inject = ['$location', '$routeParams', 'common', 'dataservice'];

    function DashboardController($location, $routeParams, common, dataservice) {
    }
    ```

    Nota: Quando sua função está abaixo de uma declaração de retorno o `$inject` pode ser inalcansável (isso pode acontecer em uma diretiva). Você pode resolver isso movento o controlador para fora da diretiva.

    ```javascriptcá
    /* evite */
    // dentro da definiço de uma diretiva
    function outer() {
        var ddo = {
            controller: DashboardPanelController,
            controllerAs: 'vm'
        };
        return ddo;

        DashboardPanelController.$inject = ['logger']; // Inalcansável
        function DashboardPanelController(logger) {
        }
    }
    ```

    ```javascript
    /* recomendado */
    // fora da definiço de uma diretiva
    function outer() {
        var ddo = {
            controller: DashboardPanelController,
            controllerAs: 'vm'
        };
        return ddo;
    }

    DashboardPanelController.$inject = ['logger'];
    function DashboardPanelController(logger) {
    }
    ```

### Manualmente Identifique as dependências do Route Resolver
###### [Estilo [Y092](#estilo-y092)]

  - Use `$inject` para manualmente identificar as dependências do seu route resolver para os componentes do Angular.

    *Por que?*: Essa técnica quebra a utilização de funções anônimas para o route resolver, facilitando a leitura.

    *Por que?*: Uma declaração `$inject` pode facilmente preceder o resolver para manipular qualquer minificação de dependência de forma segura.

    ```javascript
    /* recomendado */
    function config($routeProvider) {
        $routeProvider
            .when('/avengers', {
                templateUrl: 'avengers.html',
                controller: 'AvengersController',
                controllerAs: 'vm',
                resolve: {
                    moviesPrepService: moviesPrepService
                }
            });
    }

    moviesPrepService.$inject = ['movieService'];
    function moviesPrepService(movieService) {
        return movieService.getMovies();
    }
    ```

**[Voltar para o topo](#indice)**

## Minificação e Anotação

### ng-annotate
###### [Estilo [Y100](#estilo-y100)]

  - Use [ng-annotate](//github.com/olov/ng-annotate) para o [Gulp](http://gulpjs.com) ou [Grunt](http://gruntjs.com) e comente funções que precisam de dependencia automatizada utilizando `/* @ngInject */`

    *Por que?*: Isso protege seu código de qualquer dependência que pode não estar utilizando praticas de minificação seguras.

    *Por que?*: [`ng-min`](https://github.com/btford/ngmin) foi descontinuado

    >Eu prefiro Gulp pois eu sinto que ele é mais fácil de escrever, ler e debugar.

    O código a seguir no está utilizando minificação segura de dependências.

    ```javascript
    angular
        .module('app')
        .controller('AvengersController', AvengersController);

    /* @ngInject */
    function AvengersController(storage, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero() {
            var hero = avengerService.find(vm.heroSearch);
            storage.save(hero.name, hero);
        }
    }
    ```

    Quando o código acima está executando através do ng-annotate isso irá produzir a seguinte saída com a anotação `$inject` e se tornar segura a minificação.

    ```javascript
    angular
        .module('app')
        .controller('AvengersController', AvengersController);

    /* @ngInject */
    function AvengersController(storage, avengerService) {
        var vm = this;
        vm.heroSearch = '';
        vm.storeHero = storeHero;

        function storeHero() {
            var hero = avengerService.find(vm.heroSearch);
            storage.save(hero.name, hero);
        }
    }

    AvengersController.$inject = ['storage', 'avengerService'];
    ```

    Nota: Se `ng-annotate` detecta injeção que já foi feita (e.g. `@ngInject` foi detectado), ele no irá duplicar o código`$inject`.

    Nota: Ao usar um route resolver você pode prefixar a função resolver com `/* @ngInject */` e isso ir produzir um código anotado adequadamente, mantendo qualquer minificação de dependências injetadas seguras.

    ```javascript
    // Usando anotações @ngInject
    function config($routeProvider) {
        $routeProvider
            .when('/avengers', {
                templateUrl: 'avengers.html',
                controller: 'AvengersController',
                controllerAs: 'vm',
                resolve: { /* @ngInject */
                    moviesPrepService: function(movieService) {
                        return movieService.getMovies();
                    }
                }
            });
    }
    ```

    > Nota: A partir do Angular 1.3 você pode usar a o parâmetro `ngStrictDi` da diretiva [`ngApp`](https://docs.angularjs.org/api/ng/directive/ngApp) `ngStrictDi` para detectar qualquer minificação de dependência segura potencialmente esquecida. Quando presente o injector será criado no modo "strict-di" provocando uma falha na aplicação ao invocar funções que não utilizam a anotação da função explicitamente. O debug irá ser registrado no log para o console para ajudar a rastrear o código problemático. Eu prefiro realmente usar `ng-strict-di` apenas para propósitos de debug.
    `<body ng-app="APP" ng-strict-di>`

### Use Gulp or Grunt for ng-annotate
###### [Style [Y101](#style-y101)]

  - Use [gulp-ng-annotate](https://www.npmjs.com/package/gulp-ng-annotate) or [grunt-ng-annotate](https://www.npmjs.com/package/grunt-ng-annotate) in an automated build task. Inject `/* @ngInject */` prior to any function that has dependencies.

    *Why?*: ng-annotate will catch most dependencies, but it sometimes requires hints using the `/* @ngInject */` syntax.

    The following code is an example of a gulp task using ngAnnotate

    ```javascript
    gulp.task('js', ['jshint'], function() {
        var source = pkg.paths.js;

        return gulp.src(source)
            .pipe(sourcemaps.init())
            .pipe(concat('all.min.js', {newLine: ';'}))
            // Annotate before uglify so the code get's min'd properly.
            .pipe(ngAnnotate({
                // true helps add where @ngInject is not used. It infers.
                // Doesn't work with resolve, so we must be explicit there
                add: true
            }))
            .pipe(bytediff.start())
            .pipe(uglify({mangle: true}))
            .pipe(bytediff.stop())
            .pipe(sourcemaps.write('./'))
            .pipe(gulp.dest(pkg.paths.dev));
    });

    ```

**[Back to top](#table-of-contents)**

## Exception Handling

### decorators
###### [Style [Y110](#style-y110)]

  - Use a [decorator](https://docs.angularjs.org/api/auto/service/$provide#decorator), at config time using the [`$provide`](https://docs.angularjs.org/api/auto/service/$provide) service, on the [`$exceptionHandler`](https://docs.angularjs.org/api/ng/service/$exceptionHandler) service to perform custom actions when exceptions occur.

    *Why?*: Provides a consistent way to handle uncaught Angular exceptions for development-time or run-time.

    Note: Another option is to override the service instead of using a decorator. This is a fine option, but if you want to keep the default behavior and extend it a decorator is recommended.

    ```javascript
    /* recommended */
    angular
        .module('blocks.exception')
        .config(exceptionConfig);

    exceptionConfig.$inject = ['$provide'];

    function exceptionConfig($provide) {
        $provide.decorator('$exceptionHandler', extendExceptionHandler);
    }

    extendExceptionHandler.$inject = ['$delegate', 'toastr'];

    function extendExceptionHandler($delegate, toastr) {
        return function(exception, cause) {
            $delegate(exception, cause);
            var errorData = {
                exception: exception,
                cause: cause
            };
            /**
             * Could add the error to a service's collection,
             * add errors to $rootScope, log errors to remote web server,
             * or log locally. Or throw hard. It is entirely up to you.
             * throw exception;
             */
            toastr.error(exception.msg, errorData);
        };
    }
    ```

### Exception Catchers
###### [Style [Y111](#style-y111)]

  - Create a factory that exposes an interface to catch and gracefully handle exceptions.

    *Why?*: Provides a consistent way to catch exceptions that may be thrown in your code (e.g. during XHR calls or promise failures).

    Note: The exception catcher is good for catching and reacting to specific exceptions from calls that you know may throw one. For example, when making an XHR call to retrieve data from a remote web service and you want to catch any exceptions from that service and react uniquely.

    ```javascript
    /* recommended */
    angular
        .module('blocks.exception')
        .factory('exception', exception);

    exception.$inject = ['logger'];

    function exception(logger) {
        var service = {
            catcher: catcher
        };
        return service;

        function catcher(message) {
            return function(reason) {
                logger.error(message, reason);
            };
        }
    }
    ```

### Route Errors
###### [Style [Y112](#style-y112)]

  - Handle and log all routing errors using [`$routeChangeError`](https://docs.angularjs.org/api/ngRoute/service/$route#$routeChangeError).

    *Why?*: Provides a consistent way to handle all routing errors.

    *Why?*: Potentially provides a better user experience if a routing error occurs and you route them to a friendly screen with more details or recovery options.

    ```javascript
    /* recommended */
    var handlingRouteChangeError = false;

    function handleRoutingErrors() {
        /**
         * Route cancellation:
         * On routing error, go to the dashboard.
         * Provide an exit clause if it tries to do it twice.
         */
        $rootScope.$on('$routeChangeError',
            function(event, current, previous, rejection) {
                if (handlingRouteChangeError) { return; }
                handlingRouteChangeError = true;
                var destination = (current && (current.title ||
                    current.name || current.loadedTemplateUrl)) ||
                    'unknown target';
                var msg = 'Error routing to ' + destination + '. ' +
                    (rejection.msg || '');

                /**
                 * Optionally log using a custom service or $log.
                 * (Don't forget to inject custom service)
                 */
                logger.warning(msg, [current]);

                /**
                 * On routing error, go to another route/state.
                 */
                $location.path('/');

            }
        );
    }
    ```

**[Back to top](#table-of-contents)**

## Naming

### Naming Guidelines
###### [Style [Y120](#style-y120)]

  - Use consistent names for all components following a pattern that describes the component's feature then (optionally) its type. My recommended pattern is `feature.type.js`. There are 2 names for most assets:
    * the file name (`avengers.controller.js`)
    * the registered component name with Angular (`AvengersController`)

    *Why?*: Naming conventions help provide a consistent way to find content at a glance. Consistency within the project is vital. Consistency with a team is important. Consistency across a company provides tremendous efficiency.

    *Why?*: The naming conventions should simply help you find your code faster and make it easier to understand.

### Feature File Names
###### [Style [Y121](#style-y121)]

  - Use consistent names for all components following a pattern that describes the component's feature then (optionally) its type. My recommended pattern is `feature.type.js`.

    *Why?*: Provides a consistent way to quickly identify components.

    *Why?*: Provides pattern matching for any automated tasks.

    ```javascript
    /**
     * common options
     */

    // Controllers
    avengers.js
    avengers.controller.js
    avengersController.js

    // Services/Factories
    logger.js
    logger.service.js
    loggerService.js
    ```

    ```javascript
    /**
     * recommended
     */

    // controllers
    avengers.controller.js
    avengers.controller.spec.js

    // services/factories
    logger.service.js
    logger.service.spec.js

    // constants
    constants.js

    // module definition
    avengers.module.js

    // routes
    avengers.routes.js
    avengers.routes.spec.js

    // configuration
    avengers.config.js

    // directives
    avenger-profile.directive.js
    avenger-profile.directive.spec.js
    ```

  Note: Another common convention is naming controller files without the word `controller` in the file name such as `avengers.js` instead of `avengers.controller.js`. All other conventions still hold using a suffix of the type. Controllers are the most common type of component so this just saves typing and is still easily identifiable. I recommend you choose 1 convention and be consistent for your team. My preference is `avengers.controller.js` identifying the `AvengersController`.

    ```javascript
    /**
     * recommended
     */
    // Controllers
    avengers.js
    avengers.spec.js
    ```

### Test File Names
###### [Style [Y122](#style-y122)]

  - Name test specifications similar to the component they test with a suffix of `spec`.

    *Why?*: Provides a consistent way to quickly identify components.

    *Why?*: Provides pattern matching for [karma](http://karma-runner.github.io/) or other test runners.

    ```javascript
    /**
     * recommended
     */
    avengers.controller.spec.js
    logger.service.spec.js
    avengers.routes.spec.js
    avenger-profile.directive.spec.js
    ```

### Controller Names
###### [Style [Y123](#style-y123)]

  - Use consistent names for all controllers named after their feature. Use UpperCamelCase for controllers, as they are constructors.

    *Why?*: Provides a consistent way to quickly identify and reference controllers.

    *Why?*: UpperCamelCase is conventional for identifying object that can be instantiated using a constructor.

    ```javascript
    /**
     * recommended
     */

    // avengers.controller.js
    angular
        .module
        .controller('HeroAvengersController', HeroAvengersController);

    function HeroAvengersController() { }
    ```

### Controller Name Suffix
###### [Style [Y124](#style-y124)]

  - Append the controller name with the suffix `Controller`.

    *Why?*: The `Controller` suffix is more commonly used and is more explicitly descriptive.

    ```javascript
    /**
     * recommended
     */

    // avengers.controller.js
    angular
        .module
        .controller('AvengersController', AvengersController);

    function AvengersController() { }
    ```

### Factory and Service Names
###### [Style [Y125](#style-y125)]

  - Use consistent names for all factories and services named after their feature. Use camel-casing for services and factories. Avoid prefixing factories and services with `$`. Only suffix service and factories with `Service` when it is not clear what they are (i.e. when they are nouns).

    *Why?*: Provides a consistent way to quickly identify and reference factories.

    *Why?*: Avoids name collisions with built-in factories and services that use the `$` prefix.

    *Why?*: Clear service names such as `logger` do not require a suffix.

    *Why?*: Service names such as `avengers` are nouns and require a suffix and should be named `avengersService`.

    ```javascript
    /**
     * recommended
     */

    // logger.service.js
    angular
        .module
        .factory('logger', logger);

    function logger() { }
    ```

    ```javascript
    /**
     * recommended
     */

    // credit.service.js
    angular
        .module
        .factory('creditService', creditService);

    function creditService() { }

    // customer.service.js
    angular
        .module
        .service('customerService', customerService);

    function customerService() { }
    ```

### Directive Component Names
###### [Style [Y126](#style-y126)]

  - Use consistent names for all directives using camelCase. Use a short prefix to describe the area that the directives belong (some example are company prefix or project prefix).

    *Why?*: Provides a consistent way to quickly identify and reference components.

    ```javascript
    /**
     * recommended
     */

    // avenger-profile.directive.js
    angular
        .module
        .directive('xxAvengerProfile', xxAvengerProfile);

    // usage is <xx-avenger-profile> </xx-avenger-profile>

    function xxAvengerProfile() { }
    ```

### Modules
###### [Style [Y127](#style-y127)]

  - When there are multiple modules, the main module file is named `app.module.js` while other dependent modules are named after what they represent. For example, an admin module is named `admin.module.js`. The respective registered module names would be `app` and `admin`.

    *Why?*: Provides consistency for multiple module apps, and for expanding to large applications.

    *Why?*: Provides easy way to use task automation to load all module definitions first, then all other angular files (for bundling).

### Configuration
###### [Style [Y128](#style-y128)]

  - Separate configuration for a module into its own file named after the module. A configuration file for the main `app` module is named `app.config.js` (or simply `config.js`). A configuration for a module named `admin.module.js` is named `admin.config.js`.

    *Why?*: Separates configuration from module definition, components, and active code.

    *Why?*: Provides an identifiable place to set configuration for a module.

### Routes
###### [Style [Y129](#style-y129)]

  - Separate route configuration into its own file. Examples might be `app.route.js` for the main module and `admin.route.js` for the `admin` module. Even in smaller apps I prefer this separation from the rest of the configuration.

**[Back to top](#table-of-contents)**

## Application Structure LIFT Principle
### LIFT
###### [Style [Y140](#style-y140)]

  - Structure your app such that you can `L`ocate your code quickly, `I`dentify the code at a glance, keep the `F`lattest structure you can, and `T`ry to stay DRY. The structure should follow these 4 basic guidelines.

    *Why LIFT?*: Provides a consistent structure that scales well, is modular, and makes it easier to increase developer efficiency by finding code quickly. Another way to check your app structure is to ask yourself: How quickly can you open and work in all of the related files for a feature?

    When I find my structure is not feeling comfortable, I go back and revisit these LIFT guidelines

    1. `L`ocating our code is easy
    2. `I`dentify code at a glance
    3. `F`lat structure as long as we can
    4. `T`ry to stay DRY (Don’t Repeat Yourself) or T-DRY

### Locate
###### [Style [Y141](#style-y141)]

  - Make locating your code intuitive, simple and fast.

    *Why?*: I find this to be super important for a project. If the team cannot find the files they need to work on quickly, they will not be able to work as efficiently as possible, and the structure needs to change. You may not know the file name or where its related files are, so putting them in the most intuitive locations and near each other saves a ton of time. A descriptive folder structure can help with this.

    ```
    /bower_components
    /client
      /app
        /avengers
        /blocks
          /exception
          /logger
        /core
        /dashboard
        /data
        /layout
        /widgets
      /content
      index.html
    .bower.json
    ```

### Identify
###### [Style [Y142](#style-y142)]

  - When you look at a file you should instantly know what it contains and represents.

    *Why?*: You spend less time hunting and pecking for code, and become more efficient. If this means you want longer file names, then so be it. Be descriptive with file names and keeping the contents of the file to exactly 1 component. Avoid files with multiple controllers, multiple services, or a mixture. There are deviations of the 1 per file rule when I have a set of very small features that are all related to each other, they are still easily identifiable.

### Flat
###### [Style [Y143](#style-y143)]

  - Keep a flat folder structure as long as possible. When you get to 7+ files, begin considering separation.

    *Why?*: Nobody wants to search 7 levels of folders to find a file. Think about menus on web sites … anything deeper than 2 should take serious consideration. In a folder structure there is no hard and fast number rule, but when a folder has 7-10 files, that may be time to create subfolders. Base it on your comfort level. Use a flatter structure until there is an obvious value (to help the rest of LIFT) in creating a new folder.

### T-DRY (Try to Stick to DRY)
###### [Style [Y144](#style-y144)]

  - Be DRY, but don't go nuts and sacrifice readability.

    *Why?*: Being DRY is important, but not crucial if it sacrifices the others in LIFT, which is why I call it T-DRY. I don’t want to type session-view.html for a view because, well, it’s obviously a view. If it is not obvious or by convention, then I name it.

**[Back to top](#table-of-contents)**

## Application Structure

### Overall Guidelines
###### [Style [Y150](#style-y150)]

  - Have a near term view of implementation and a long term vision. In other words, start small but keep in mind on where the app is heading down the road. All of the app's code goes in a root folder named `app`. All content is 1 feature per file. Each controller, service, module, view is in its own file. All 3rd party vendor scripts are stored in another root folder and not in the `app` folder. I didn't write them and I don't want them cluttering my app (`bower_components`, `scripts`, `lib`).

    Note: Find more details and reasoning behind the structure at [this original post on application structure](http://www.johnpapa.net/angular-app-structuring-guidelines/).

### Layout
###### [Style [Y151](#style-y151)]

  - Place components that define the overall layout of the application in a folder named `layout`. These may include a shell view and controller may act as the container for the app, navigation, menus, content areas, and other regions.

    *Why?*: Organizes all layout in a single place re-used throughout the application.

### Folders-by-Feature Structure
###### [Style [Y152](#style-y152)]

  - Create folders named for the feature they represent. When a folder grows to contain more than 7 files, start to consider creating a folder for them. Your threshold may be different, so adjust as needed.

    *Why?*: A developer can locate the code, identify what each file represents at a glance, the structure is flat as can be, and there is no repetitive nor redundant names.

    *Why?*: The LIFT guidelines are all covered.

    *Why?*: Helps reduce the app from becoming cluttered through organizing the content and keeping them aligned with the LIFT guidelines.

    *Why?*: When there are a lot of files (10+) locating them is easier with a consistent folder structures and more difficult in flat structures.

    ```javascript
    /**
     * recommended
     */

    app/
        app.module.js
        app.config.js
        components/
            calendar.directive.js
            calendar.directive.html
            user-profile.directive.js
            user-profile.directive.html
        layout/
            shell.html
            shell.controller.js
            topnav.html
            topnav.controller.js
        people/
            attendees.html
            attendees.controller.js
            people.routes.js
            speakers.html
            speakers.controller.js
            speaker-detail.html
            speaker-detail.controller.js
        services/
            data.service.js
            localstorage.service.js
            logger.service.js
            spinner.service.js
        sessions/
            sessions.html
            sessions.controller.js
            sessions.routes.js
            session-detail.html
            session-detail.controller.js
    ```

      ![Sample App Structure](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/modularity-2.png)

      Note: Do not structure your app using folders-by-type. This requires moving to multiple folders when working on a feature and gets unwieldy quickly as the app grows to 5, 10 or 25+ views and controllers (and other features), which makes it more difficult than folder-by-feature to locate files.

    ```javascript
    /*
    * avoid
    * Alternative folders-by-type.
    * I recommend "folders-by-feature", instead.
    */

    app/
        app.module.js
        app.config.js
        app.routes.js
        directives.js
        controllers/
            attendees.js
            session-detail.js
            sessions.js
            shell.js
            speakers.js
            speaker-detail.js
            topnav.js
        directives/
            calendar.directive.js
            calendar.directive.html
            user-profile.directive.js
            user-profile.directive.html
        services/
            dataservice.js
            localstorage.js
            logger.js
            spinner.js
        views/
            attendees.html
            session-detail.html
            sessions.html
            shell.html
            speakers.html
            speaker-detail.html
            topnav.html
    ```

**[Back to top](#table-of-contents)**

## Modularity

### Many Small, Self Contained Modules
###### [Style [Y160](#style-y160)]

  - Create small modules that encapsulate one responsibility.

    *Why?*: Modular applications make it easy to plug and go as they allow the development teams to build vertical slices of the applications and roll out incrementally. This means we can plug in new features as we develop them.

### Create an App Module
###### [Style [Y161](#style-y161)]

  - Create an application root module whose role is to pull together all of the modules and features of your application. Name this for your application.

    *Why?*: Angular encourages modularity and separation patterns. Creating an application root module whose role is to tie your other modules together provides a very straightforward way to add or remove modules from your application.

### Keep the App Module Thin
###### [Style [Y162](#style-y162)]

  - Only put logic for pulling together the app in the application module. Leave features in their own modules.

    *Why?*: Adding additional roles to the application root to get remote data, display views, or other logic not related to pulling the app together muddies the app module and make both sets of features harder to reuse or turn off.

    *Why?*: The app module becomes a manifest that describes which modules help define the application.

### Feature Areas are Modules
###### [Style [Y163](#style-y163)]

  - Create modules that represent feature areas, such as layout, reusable and shared services, dashboards, and app specific features (e.g. customers, admin, sales).

    *Why?*: Self contained modules can be added to the application with little or no friction.

    *Why?*: Sprints or iterations can focus on feature areas and turn them on at the end of the sprint or iteration.

    *Why?*: Separating feature areas into modules makes it easier to test the modules in isolation and reuse code.

### Reusable Blocks are Modules
###### [Style [Y164](#style-y164)]

  - Create modules that represent reusable application blocks for common services such as exception handling, logging, diagnostics, security, and local data stashing.

    *Why?*: These types of features are needed in many applications, so by keeping them separated in their own modules they can be application generic and be reused across applications.

### Module Dependencies
###### [Style [Y165](#style-y165)]

  - The application root module depends on the app specific feature modules and any shared or reusable modules.

    ![Modularity and Dependencies](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/modularity-1.png)

    *Why?*: The main app module contains a quickly identifiable manifest of the application's features.

    *Why?*: Each feature area contains a manifest of what it depends on, so it can be pulled in as a dependency in other applications and still work.

    *Why?*: Intra-App features such as shared data services become easy to locate and share from within `app.core` (choose your favorite name for this module).

    Note: This is a strategy for consistency. There are many good options here. Choose one that is consistent, follows Angular's dependency rules, and is easy to maintain and scale.

    > My structures vary slightly between projects but they all follow these guidelines for structure and modularity. The implementation may vary depending on the features and the team. In other words, don't get hung up on an exact like-for-like structure but do justify your structure using consistency, maintainability, and efficiency in mind.

    > In a small app, you can also consider putting all the shared dependencies in the app module where the feature modules have no direct dependencies. This makes it easier to maintain the smaller application, but makes it harder to reuse modules outside of this application.

**[Back to top](#table-of-contents)**

## Startup Logic

### Configuration
###### [Style [Y170](#style-y170)]

  - Inject code into [module configuration](https://docs.angularjs.org/guide/module#module-loading-dependencies) that must be configured before running the angular app. Ideal candidates include providers and constants.

    *Why?*: This makes it easier to have less places for configuration.

  ```javascript
  angular
      .module('app')
      .config(configure);

  configure.$inject =
      ['routerHelperProvider', 'exceptionHandlerProvider', 'toastr'];

  function configure (routerHelperProvider, exceptionHandlerProvider, toastr) {
      exceptionHandlerProvider.configure(config.appErrorPrefix);
      configureStateHelper();

      toastr.options.timeOut = 4000;
      toastr.options.positionClass = 'toast-bottom-right';

      ////////////////

      function configureStateHelper() {
          routerHelperProvider.configure({
              docTitle: 'NG-Modular: '
          });
      }
  }
  ```

### Run Blocks
###### [Style [Y171](#style-y171)]

  - Any code that needs to run when an application starts should be declared in a factory, exposed via a function, and injected into the [run block](https://docs.angularjs.org/guide/module#module-loading-dependencies). 
  
  - Consider using manual bootstrapping techniques, as an alternative for logic that must run prior to running the Angular app.

    *Why?*: Code directly in a run block can be difficult to test. Placing in a factory makes it easier to abstract and mock.

    *Why?*: Code directly in a run block can cause race conditions for startup logic, as it does not have a way to communicate when asynchronous code in the run block has completed.

  ```javascript
  angular
      .module('app')
      .run(runBlock);

  runBlock.$inject = ['authenticator', 'translator'];

  function runBlock(authenticator, translator) {
      authenticator.initialize();
      translator.initialize();
  }
  ```

**[Back to top](#table-of-contents)**

## Angular $ Wrapper Services

### $document and $window
###### [Style [Y180](#style-y180)]

  - Use [`$document`](https://docs.angularjs.org/api/ng/service/$document) and [`$window`](https://docs.angularjs.org/api/ng/service/$window) instead of `document` and `window`.

    *Why?*: These services are wrapped by Angular and more easily testable than using document and window in tests. This helps you avoid having to mock document and window yourself.

### $timeout and $interval
###### [Style [Y181](#style-y181)]

  - Use [`$timeout`](https://docs.angularjs.org/api/ng/service/$timeout) and [`$interval`](https://docs.angularjs.org/api/ng/service/$interval) instead of `setTimeout` and `setInterval` .

    *Why?*: These services are wrapped by Angular and more easily testable and handle Angular's digest cycle thus keeping data binding in sync.

**[Back to top](#table-of-contents)**

## Testing
Unit testing helps maintain clean code, as such I included some of my recommendations for unit testing foundations with links for more information.

### Write Tests with Stories
###### [Style [Y190](#style-y190)]

  - Write a set of tests for every story. Start with an empty test and fill them in as you write the code for the story.

    *Why?*: Writing the test descriptions helps clearly define what your story will do, will not do, and how you can measure success.

    ```javascript
    it('should have Avengers controller', function() {
        // TODO
    });

    it('should find 1 Avenger when filtered by name', function() {
        // TODO
    });

    it('should have 10 Avengers', function() {
        // TODO (mock data?)
    });

    it('should return Avengers via XHR', function() {
        // TODO ($httpBackend?)
    });

    // and so on
    ```

### Testing Library
###### [Style [Y191](#style-y191)]

  - Use [Jasmine](http://jasmine.github.io/) or [Mocha](http://mochajs.org) for unit testing.

    *Why?*: Both Jasmine and Mocha are widely used in the Angular community. Both are stable, well maintained, and provide robust testing features.

    Note: When using Mocha, also consider choosing an assert library such as [Chai](http://chaijs.com). I prefer Mocha.

### Test Runner
###### [Style [Y192](#style-y192)]

  - Use [Karma](http://karma-runner.github.io) as a test runner.

    *Why?*: Karma is easy to configure to run once or automatically when you change your code.

    *Why?*: Karma hooks into your Continuous Integration process easily on its own or through Grunt or Gulp.

    *Why?*: Some IDE's are beginning to integrate with Karma, such as [WebStorm](http://www.jetbrains.com/webstorm/) and [Visual Studio](https://visualstudiogallery.msdn.microsoft.com/02f47876-0e7a-4f6c-93f8-1af5d5189225).

    *Why?*: Karma works well with task automation leaders such as [Grunt](http://gruntjs.com/) (with [grunt-karma](https://github.com/karma-runner/grunt-karma)) and [Gulp](http://gulpjs.com/). When using Gulp, use [Karma](https://github.com/karma-runner/karma) directly and not with a plugin as the API can be called directly.

    ```javascript
    /* recommended */

    // Gulp example with Karma directly
    function startTests(singleRun, done) {
        var child;
        var excludeFiles = [];
        var fork = require('child_process').fork;
        var Server = require('karma').Server;
        var serverSpecs = config.serverIntegrationSpecs;

        if (args.startServers) {
            log('Starting servers');
            var savedEnv = process.env;
            savedEnv.NODE_ENV = 'dev';
            savedEnv.PORT = 8888;
            child = fork(config.nodeServer);
        } else {
            if (serverSpecs && serverSpecs.length) {
                excludeFiles = serverSpecs;
            }
        }

        var karmaOptions = {
          configFile: __dirname + '/karma.conf.js',
          exclude: excludeFiles,
          singleRun: !!singleRun
        };

        let server = new Server(karmaOptions, karmaCompleted);
        server.start();

        ////////////////

        function karmaCompleted(karmaResult) {
            log('Karma completed');
            if (child) {
                log('shutting down the child process');
                child.kill();
            }
            if (karmaResult === 1) {
                done('karma: tests failed with code ' + karmaResult);
            } else {
                done();
            }
        }
    }
    ```

### Stubbing and Spying
###### [Style [Y193](#style-y193)]

  - Use [Sinon](http://sinonjs.org/) for stubbing and spying.

    *Why?*: Sinon works well with both Jasmine and Mocha and extends the stubbing and spying features they offer.

    *Why?*: Sinon makes it easier to toggle between Jasmine and Mocha, if you want to try both.

    *Why?*: Sinon has descriptive messages when tests fail the assertions.

### Headless Browser
###### [Style [Y194](#style-y194)]

  - Use [PhantomJS](http://phantomjs.org/) to run your tests on a server.

    *Why?*: PhantomJS is a headless browser that helps run your tests without needing a "visual" browser. So you do not have to install Chrome, Safari, IE, or other browsers on your server.

    Note: You should still test on all browsers in your environment, as appropriate for your target audience.

### Code Analysis
###### [Style [Y195](#style-y195)]

  - Run JSHint on your tests.

    *Why?*: Tests are code. JSHint can help identify code quality issues that may cause the test to work improperly.

### Alleviate Globals for JSHint Rules on Tests
###### [Style [Y196](#style-y196)]

  - Relax the rules on your test code to allow for common globals such as `describe` and `expect`. Relax the rules for expressions, as Mocha uses these.

    *Why?*: Your tests are code and require the same attention and code quality rules as all of your production code. However, global variables used by the testing framework, for example, can be relaxed by including this in your test specs.

    ```javascript
    /* jshint -W117, -W030 */
    ```
    Or you can add the following to your JSHint Options file.

    ```javascript
    "jasmine": true,
    "mocha": true,
    ```

  ![Testing Tools](https://raw.githubusercontent.com/johnpapa/angular-styleguide/master/a1/assets/testing-tools.png)

### Organizing Tests
###### [Style [Y197](#style-y197)]

  - Place unit test files (specs) side-by-side with your client code. Place specs that cover server integration or test multiple components in a separate `tests` folder.

    *Why?*: Unit tests have a direct correlation to a specific component and file in source code.

    *Why?*: It is easier to keep them up to date since they are always in sight. When coding whether you do TDD or test during development or test after development, the specs are side-by-side and never out of sight nor mind, and thus more likely to be maintained which also helps maintain code coverage.

    *Why?*: When you update source code it is easier to go update the tests at the same time.

    *Why?*: Placing them side-by-side makes it easy to find them and easy to move them with the source code if you move the source.

    *Why?*: Having the spec nearby makes it easier for the source code reader to learn how the component is supposed to be used and to discover its known limitations.

    *Why?*: Separating specs so they are not in a distributed build is easy with grunt or gulp.

    ```
    /src/client/app/customers/customer-detail.controller.js
                             /customer-detail.controller.spec.js
                             /customers.controller.js
                             /customers.controller.spec.js
                             /customers.module.js
                             /customers.route.js
                             /customers.route.spec.js
    ```

**[Back to top](#table-of-contents)**

## Animations

### Usage
###### [Style [Y210](#style-y210)]

  - Use subtle [animations with Angular](https://docs.angularjs.org/guide/animations) to transition between states for views and primary visual elements. Include the [ngAnimate module](https://docs.angularjs.org/api/ngAnimate). The 3 keys are subtle, smooth, seamless.

    *Why?*: Subtle animations can improve User Experience when used appropriately.

    *Why?*: Subtle animations can improve perceived performance as views transition.

### Sub Second
###### [Style [Y211](#style-y211)]

  - Use short durations for animations. I generally start with 300ms and adjust until appropriate.

    *Why?*: Long animations can have the reverse effect on User Experience and perceived performance by giving the appearance of a slow application.

### animate.css
###### [Style [Y212](#style-y212)]

  - Use [animate.css](http://daneden.github.io/animate.css/) for conventional animations.

    *Why?*: The animations that animate.css provides are fast, smooth, and easy to add to your application.

    *Why?*: Provides consistency in your animations.

    *Why?*: animate.css is widely used and tested.

    Note: See this [great post by Matias Niemelä on Angular animations](http://www.yearofmoo.com/2013/08/remastered-animation-in-angularjs-1-2.html)

**[Back to top](#table-of-contents)**

## Comments

### jsDoc
###### [Style [Y220](#style-y220)]

  - If planning to produce documentation, use [`jsDoc`](http://usejsdoc.org/) syntax to document function names, description, params and returns. Use `@namespace` and `@memberOf` to match your app structure.

    *Why?*: You can generate (and regenerate) documentation from your code, instead of writing it from scratch.

    *Why?*: Provides consistency using a common industry tool.

    ```javascript
    /**
     * Logger Factory
     * @namespace Factories
     */
    (function() {
      angular
          .module('app')
          .factory('logger', logger);

      /**
       * @namespace Logger
       * @desc Application wide logger
       * @memberOf Factories
       */
      function logger($log) {
          var service = {
             logError: logError
          };
          return service;

          ////////////

          /**
           * @name logError
           * @desc Logs errors
           * @param {String} msg Message to log
           * @returns {String}
           * @memberOf Factories.Logger
           */
          function logError(msg) {
              var loggedMsg = 'Error: ' + msg;
              $log.error(loggedMsg);
              return loggedMsg;
          };
      }
    })();
    ```

**[Back to top](#table-of-contents)**

## JS Hint

### Use an Options File
###### [Style [Y230](#style-y230)]

  - Use JS Hint for linting your JavaScript and be sure to customize the JS Hint options file and include in source control. See the [JS Hint docs](http://jshint.com/docs/) for details on the options.

    *Why?*: Provides a first alert prior to committing any code to source control.

    *Why?*: Provides consistency across your team.

    ```javascript
    {
        "bitwise": true,
        "camelcase": true,
        "curly": true,
        "eqeqeq": true,
        "esversion": 6,
        "forin": true,
        "freeze": true,
        "immed": true,
        "indent": 4,
        "latedef": "nofunc",
        "newcap": true,
        "noarg": true,
        "noempty": true,
        "nonbsp": true,
        "nonew": true,
        "plusplus": false,
        "quotmark": "single",
        "undef": true,
        "unused": false,
        "strict": false,
        "maxparams": 10,
        "maxdepth": 5,
        "maxstatements": 40,
        "maxcomplexity": 8,
        "maxlen": 120,
        "asi": false,
        "boss": false,
        "debug": false,
        "eqnull": true,
        "esnext": false,
        "evil": false,
        "expr": false,
        "funcscope": false,
        "globalstrict": false,
        "iterator": false,
        "lastsemic": false,
        "laxbreak": false,
        "laxcomma": false,
        "loopfunc": true,
        "maxerr": 50,
        "moz": false,
        "multistr": false,
        "notypeof": false,
        "proto": false,
        "scripturl": false,
        "shadow": false,
        "sub": true,
        "supernew": false,
        "validthis": false,
        "noyield": false,

        "browser": true,
        "node": true,

        "globals": {
            "angular": false,
            "$": false
        }
    }
    ```

**[Back to top](#table-of-contents)**

## JSCS

### Use an Options File
###### [Style [Y235](#style-y235)]

  - Use JSCS for checking your coding styles your JavaScript and be sure to customize the JSCS options file and include in source control. See the [JSCS docs](http://jscs.info/) for details on the options.

    *Why?*: Provides a first alert prior to committing any code to source control.

    *Why?*: Provides consistency across your team.

    ```javascript
    {
        "excludeFiles": ["node_modules/**", "bower_components/**"],

        "requireCurlyBraces": [
            "if",
            "else",
            "for",
            "while",
            "do",
            "try",
            "catch"
        ],
        "requireOperatorBeforeLineBreak": true,
        "requireCamelCaseOrUpperCaseIdentifiers": true,
        "maximumLineLength": {
          "value": 100,
          "allowComments": true,
          "allowRegex": true
        },
        "validateIndentation": 4,
        "validateQuoteMarks": "'",

        "disallowMultipleLineStrings": true,
        "disallowMixedSpacesAndTabs": true,
        "disallowTrailingWhitespace": true,
        "disallowSpaceAfterPrefixUnaryOperators": true,
        "disallowMultipleVarDecl": null,

        "requireSpaceAfterKeywords": [
          "if",
          "else",
          "for",
          "while",
          "do",
          "switch",
          "return",
          "try",
          "catch"
        ],
        "requireSpaceBeforeBinaryOperators": [
            "=", "+=", "-=", "*=", "/=", "%=", "<<=", ">>=", ">>>=",
            "&=", "|=", "^=", "+=",

            "+", "-", "*", "/", "%", "<<", ">>", ">>>", "&",
            "|", "^", "&&", "||", "===", "==", ">=",
            "<=", "<", ">", "!=", "!=="
        ],
        "requireSpaceAfterBinaryOperators": true,
        "requireSpacesInConditionalExpression": true,
        "requireSpaceBeforeBlockStatements": true,
        "requireLineFeedAtFileEnd": true,
        "disallowSpacesInsideObjectBrackets": "all",
        "disallowSpacesInsideArrayBrackets": "all",
        "disallowSpacesInsideParentheses": true,

        "jsDoc": {
            "checkAnnotations": true,
            "checkParamNames": true,
            "requireParamTypes": true,
            "checkReturnTypes": true,
            "checkTypes": true
        },

        "disallowMultipleLineBreaks": true,

        "disallowCommaBeforeLineBreak": null,
        "disallowDanglingUnderscores": null,
        "disallowEmptyBlocks": null,
        "disallowTrailingComma": null,
        "requireCommaBeforeLineBreak": null,
        "requireDotNotation": null,
        "requireMultipleVarDecl": null,
        "requireParenthesesAroundIIFE": true
    }
    ```

**[Back to top](#table-of-contents)**

## Constants

### Vendor Globals
###### [Style [Y240](#style-y240)]

  - Create an Angular Constant for vendor libraries' global variables.

    *Why?*: Provides a way to inject vendor libraries that otherwise are globals. This improves code testability by allowing you to more easily know what the dependencies of your components are (avoids leaky abstractions). It also allows you to mock these dependencies, where it makes sense.

    ```javascript
    // constants.js

    /* global toastr:false, moment:false */
    (function() {
        'use strict';

        angular
            .module('app.core')
            .constant('toastr', toastr)
            .constant('moment', moment);
    })();
    ```

###### [Style [Y241](#style-y241)]

  - Use constants for values that do not change and do not come from another service. When constants are used only for a module that may be reused in multiple applications, place constants in a file per module named after the module. Until this is required, keep constants in the main module in a `constants.js` file.

    *Why?*: A value that may change, even infrequently, should be retrieved from a service so you do not have to change the source code. For example, a url for a data service could be placed in a constants but a better place would be to load it from a web service.

    *Why?*: Constants can be injected into any angular component, including providers.

    *Why?*: When an application is separated into modules that may be reused in other applications, each stand-alone module should be able to operate on its own including any dependent constants.

    ```javascript
    // Constants used by the entire app
    angular
        .module('app.core')
        .constant('moment', moment);

    // Constants used only by the sales module
    angular
        .module('app.sales')
        .constant('events', {
            ORDER_CREATED: 'event_order_created',
            INVENTORY_DEPLETED: 'event_inventory_depleted'
        });
    ```

**[Back to top](#table-of-contents)**

## File Templates and Snippets
Use file templates or snippets to help follow consistent styles and patterns. Here are templates and/or snippets for some of the web development editors and IDEs.

### Sublime Text
###### [Style [Y250](#style-y250)]

  - Angular snippets that follow these styles and guidelines.

    - Download the [Sublime Angular snippets](assets/sublime-angular-snippets?raw=true)
    - Place it in your Packages folder
    - Restart Sublime
    - In a JavaScript file type these commands followed by a `TAB`

    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective  // creates an Angular directive
    ngfactory    // creates an Angular factory
    ngmodule     // creates an Angular module
    ngservice    // creates an Angular service
    ngfilter     // creates an Angular filter
    ```

### Visual Studio
###### [Style [Y251](#style-y251)]

  - Angular file templates that follow these styles and guidelines can be found at [SideWaffle](http://www.sidewaffle.com)

    - Download the [SideWaffle](http://www.sidewaffle.com) Visual Studio extension (vsix file)
    - Run the vsix file
    - Restart Visual Studio

### WebStorm
###### [Style [Y252](#style-y252)]

  - Angular live templates that follow these styles and guidelines.

    - Download the [webstorm-angular-live-templates.xml](assets/webstorm-angular-live-templates/webstorm-angular-live-templates.xml?raw=true)
    - Place it in your [templates folder](https://www.jetbrains.com/webstorm/help/project-and-ide-settings.html)
    - Restart WebStorm
    - In a JavaScript file type these commands followed by a `TAB`:

    ```javascript
    // These are full file snippets containing an IIFE
    ngapp     // creates an Angular module setter
    ngcontroller // creates an Angular controller
    ngdirective  // creates an Angular directive
    ngfactory    // creates an Angular factory
    ngfilter     // creates an Angular filter
    ngservice    // creates an Angular service

    // These are partial snippets intended to be chained
    ngconfig     // defines a configuration phase function
    ngmodule     // creates an Angular module getter
    ngroute      // defines an Angular ngRoute 'when' definition
    ngrun        // defines a run phase function
    ngstate      // creates an Angular UI Router state definition
    ```

  *Individual templates are also available for download within the [webstorm-angular-live-templates](assets/webstorm-angular-live-templates?raw=true) folder*

### Atom
###### [Style [Y253](#style-y253)]

  - Angular snippets that follow these styles and guidelines.
    ```
    apm install angularjs-styleguide-snippets
    ```
    or
    - Open Atom, then open the Package Manager (Packages -> Settings View -> Install Packages/Themes)
    - Search for the package 'angularjs-styleguide-snippets'
    - Click 'Install' to install the package

  - In a JavaScript file type these commands followed by a `TAB`

    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective // creates an Angular directive
    ngfactory // creates an Angular factory
    ngmodule // creates an Angular module
    ngservice // creates an Angular service
    ngfilter // creates an Angular filter
    ```

### Brackets
###### [Style [Y254](#style-y254)]

  - Angular snippets that follow these styles and guidelines.
    - Download the [Brackets Angular snippets](assets/brackets-angular-snippets.yaml?raw=true)
    - Brackets Extension manager ( File > Extension manager )
    - Install ['Brackets Snippets (by edc)'](https://github.com/chuyik/brackets-snippets)
    - Click the light bulb in brackets' right gutter
    - Click `Settings` and then `Import`
    - Choose the file and select to skip or override
    - Click `Start Import`

  - In a JavaScript file type these commands followed by a `TAB`

    ```javascript
    // These are full file snippets containing an IIFE
    ngcontroller // creates an Angular controller
    ngdirective  // creates an Angular directive
    ngfactory    // creates an Angular factory
    ngapp        // creates an Angular module setter
    ngservice    // creates an Angular service
    ngfilter     // creates an Angular filter

    // These are partial snippets intended to chained
    ngmodule     // creates an Angular module getter
    ngstate      // creates an Angular UI Router state definition
    ngconfig     // defines a configuration phase function
    ngrun        // defines a run phase function
    ngwhen      // defines an Angular ngRoute 'when' definition
    ngtranslate  // uses $translate service with its promise
    ```

### vim
###### [Style [Y255](#style-y255)]

  - vim snippets that follow these styles and guidelines.

    - Download the [vim Angular snippets](assets/vim-angular-snippets?raw=true)
    - set [neosnippet.vim](https://github.com/Shougo/neosnippet.vim)
    - copy snippets to snippet directory

  - vim UltiSnips snippets that follow these styles and guidelines.

    - Download the [vim Angular UltiSnips snippets](assets/vim-angular-ultisnips?raw=true)
    - set [UltiSnips](https://github.com/SirVer/ultisnips)
    - copy snippets to UltiSnips directory

    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective  // creates an Angular directive
    ngfactory    // creates an Angular factory
    ngmodule     // creates an Angular module
    ngservice    // creates an Angular service
    ngfilter     // creates an Angular filter
    ```

### Visual Studio Code

###### [Style [Y256](#style-y256)]

  - [Visual Studio Code](https://code.visualstudio.com/) snippets that follow these styles and guidelines.

    - Download the [VS Code Angular snippets](assets/vscode-snippets/javascript.json?raw=true)
    - copy snippets to snippet directory, or alternatively copy and paste the snippets into your existing ones

    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective  // creates an Angular directive
    ngfactory    // creates an Angular factory
    ngmodule     // creates an Angular module
    ngservice    // creates an Angular service
    ```

### Emacs
###### [Style [Y257](#style-y257)]

  - [Emacs](https://www.gnu.org/software/emacs/) snippets that follow these styles and guidelines.

    - Download the [Emacs Angular snippets](assets/emacs-angular-snippets?raw=true)

      Note that yasnippet categorizes snippets by major mode, and there are several Emacs major modes for editing Javascript code. The snippets are in `js2-mode`, and the other directories contain only a dotfile to reference them there.

    - install [yasnippet](https://github.com/capitaomorte/yasnippet) (`M-x package-install RET yasnippet RET`)
    - copy snippets to snippet directory, or modify your Emacs init to add snippet directory to `yas-snippet-dirs`

    ```javascript
    ngcontroller // creates an Angular controller
    ngdirective  // creates an Angular directive
    ngfactory    // creates an Angular factory
    ngmodule     // creates an Angular module
    ngservice    // creates an Angular service
    ngfilter     // creates an Angular filter
    ```
    
**[Back to top](#table-of-contents)**

## Yeoman Generator
###### [Style [Y260](#style-y260)]

You can use the [HotTowel yeoman generator](http://jpapa.me/yohottowel) to create an app that serves as a starting point for Angular that follows this style guide.

1. Install generator-hottowel

  ```
  npm install -g generator-hottowel
  ```

2. Create a new folder and change directory to it

  ```
  mkdir myapp
  cd myapp
  ```

3. Run the generator

  ```
  yo hottowel helloWorld
  ```

**[Back to top](#table-of-contents)**

## Routing
Client-side routing is important for creating a navigation flow between views and composing views that are made of many smaller templates and directives.

###### [Style [Y270](#style-y270)]

  - Use the [AngularUI Router](http://angular-ui.github.io/ui-router/) for client-side routing.

    *Why?*: UI Router offers all the features of the Angular router plus a few additional ones including nested routes and states.

    *Why?*: The syntax is quite similar to the Angular router and is easy to migrate to UI Router.

  - Note: You can use a provider such as the `routerHelperProvider` shown below to help configure states across files, during the run phase.

    ```javascript
    // customers.routes.js
    angular
        .module('app.customers')
        .run(appRun);

    /* @ngInject */
    function appRun(routerHelper) {
        routerHelper.configureStates(getStates());
    }

    function getStates() {
        return [
            {
                state: 'customer',
                config: {
                    abstract: true,
                    template: '<ui-view class="shuffle-animation"/>',
                    url: '/customer'
                }
            }
        ];
    }
    ```

    ```javascript
    // routerHelperProvider.js
    angular
        .module('blocks.router')
        .provider('routerHelper', routerHelperProvider);

    routerHelperProvider.$inject = ['$locationProvider', '$stateProvider', '$urlRouterProvider'];
    /* @ngInject */
    function routerHelperProvider($locationProvider, $stateProvider, $urlRouterProvider) {
        /* jshint validthis:true */
        this.$get = RouterHelper;

        $locationProvider.html5Mode(true);

        RouterHelper.$inject = ['$state'];
        /* @ngInject */
        function RouterHelper($state) {
            var hasOtherwise = false;

            var service = {
                configureStates: configureStates,
                getStates: getStates
            };

            return service;

            ///////////////

            function configureStates(states, otherwisePath) {
                states.forEach(function(state) {
                    $stateProvider.state(state.state, state.config);
                });
                if (otherwisePath && !hasOtherwise) {
                    hasOtherwise = true;
                    $urlRouterProvider.otherwise(otherwisePath);
                }
            }

            function getStates() { return $state.get(); }
        }
    }
    ```

###### [Style [Y271](#style-y271)]

  - Define routes for views in the module where they exist. Each module should contain the routes for the views in the module.

    *Why?*: Each module should be able to stand on its own.

    *Why?*: When removing a module or adding a module, the app will only contain routes that point to existing views.

    *Why?*: This makes it easy to enable or disable portions of an application without concern over orphaned routes.

**[Back to top](#table-of-contents)**

## Task Automation
Use [Gulp](http://gulpjs.com) or [Grunt](http://gruntjs.com) for creating automated tasks.  Gulp leans to code over configuration while Grunt leans to configuration over code. I personally prefer Gulp as I feel it is easier to read and write, but both are excellent.

> Learn more about gulp and patterns for task automation in my [Gulp Pluralsight course](http://jpapa.me/gulpps)

###### [Style [Y400](#style-y400)]

  - Use task automation to list module definition files `*.module.js` before all other application JavaScript files.

    *Why?*: Angular needs the module definitions to be registered before they are used.

    *Why?*: Naming modules with a specific pattern such as `*.module.js` makes it easy to grab them with a glob and list them first.

    ```javascript
    var clientApp = './src/client/app/';

    // Always grab module files first
    var files = [
      clientApp + '**/*.module.js',
      clientApp + '**/*.js'
    ];
    ```

**[Back to top](#table-of-contents)**

## Filters

###### [Style [Y420](#style-y420)]

  - Avoid using filters for scanning all properties of a complex object graph. Use filters for select properties.

    *Why?*: Filters can easily be abused and negatively affect performance if not used wisely, for example when a filter hits a large and deep object graph.

**[Back to top](#table-of-contents)**

## Angular docs
For anything else, API reference, check the [Angular documentation](//docs.angularjs.org/api).

**[Back to top](#table-of-contents)**
