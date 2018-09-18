# DDD com Symfony2: deixando as coisas claras

>Tradução livre do artigo "DDD with Symfony2: Making Things Clear", disponível no site https://williamdurand.fr/2013/08/20/ddd-with-symfony2-making-things-clear/, publicado em 20 de agosto de 2013.

[Domain Driven Design](http://en.wikipedia.org/wiki/Domain-driven_design) também conhecido como **DDD** é uma abordagem para desenvolver software para necessidades complexas conectando a implementação a um modelo evolutivo. [Esse é um mode de pensar e um conjunto de prioridades, destinada a acelerar projetos de software que precisam lidar com domínios complicados](http://dddcommunity.org/learning-ddd/what_is_ddd/).

É possível usar essa abordagem num projeto com Symfony2, e é isso que vamos introduzir numa série de artigos do blog. Você vai aprender como construir uma [aplicação que gerencia usuários por meio de uma API REST](https://williamdurand.fr/2012/08/02/rest-apis-with-symfony2-the-right-way/) usando **D**omain **D**riven **D**esign.

Esse é o segundo artigo dessa série! Você deveria ler [Folder Structure And Code First](https://williamdurand.fr/2013/08/07/ddd-with-symfony2-folder-structure-and-code-first/) antes de continuar neste aqui. Este post é uma tentativa de corrigir alguns equívocos advindos do [primeiro post](https://williamdurand.fr/2013/08/07/ddd-with-symfony2-folder-structure-and-code-first/). Eu recomendo que você de uma olhada rápida em todos os links, eles são importantes!

## DDD Is Not RAD[¶](https://williamdurand.fr/2013/08/20/ddd-with-symfony2-making-things-clear/#ddd-is-not-rad)

Domain Driven Design **não** é um modo de desenvolver software **rapidamente**. Ele não é de nenhuma forma [RAD](http://en.wikipedia.org/wiki/Rapid_application_development)! Ele implica em um monte de código repetido, um monte de classes e assim por diante. É, realmente não é. Este não é o jeito mais rápido de escrever uma aplicação. Ninguém nunca disse que DDD é simples ou fácil.

No entanto, ele é capaz de **facilitar sua via quando tiver que lidar com expectativas de negócios complexas**. *Como?* Por considerar seu domínio (também conhecido como seu negócio) como o coração da sua aplicação. Honestamente, o DDD deveria ser um aplicativo **bastante grande**, com regras de negócio e cenários complexos. Não use DDD se estiver fazendo um blog, isso não faz muito sentido (mesmo se esse for provalvemente o melhor jeito de aprender, do jeito difícil). Então ,para a pergunta [quando usar DDD?](http://shishkin.wordpress.com/2008/10/10/when-to-use-domain-driven-design/), eu diria que é quando seu domínio for muito complexo, ou quando seus requisitos de negócio mudam rapidamente.

## Não existe banco de dados[¶](https://williamdurand.fr/2013/08/20/ddd-with-symfony2-making-things-clear/#there-is-no-database)

Em DDD, nós **não levamos em consideração nenhum banco de dados**. [DDD é totalmente sobre o domínio, não sobre o banco de dado, e a Ignorância sobre a Persistência (PI - Persistence Ignorance) é um aspecto muito importante do DDD](http://devlicio.us/blogs/casey/archive/2009/02/12/ddd-there-is-no-database.aspx).


Com a **Ignorância sobre a Persistência**, nós tentamos eliminar todo o conhecimento dos nossos objetos de negócio sobre como, onde, por que e mesmo se eles seram armazenados em algum lugar. A Ignorância sobre a Persistência significa que [a própria lógica de negócio não sabe sobre a persistência](http://stackoverflow.com/questions/905498/what-are-the-benefits-of-persistence-ignorance). Em outras palavras, **suas Entidades não devem ser amarradas a nenhuma camada de persistência nem a nenhum framework**.

Assim, não espere que eu faça escolhas porque funcionaria melhor com o Doctrine, o Propel ou qualquer outro. Essa não é a forma como deveríamos usar DDD. Nós, como [desenvolvedores, precisamos nos tornar parte dos domínios dos nossos usuários de negócio, precisamos parar de pernsar em termos e construções técnicas, e precisamos mergulhar no mundo que nossos usuários de negócio vivem](http://devlicio.us/blogs/casey/archive/2008/09/10/the-tao-of-domain-driven-design.aspx).

## DDD e REST[¶](https://williamdurand.fr/2013/08/20/ddd-with-symfony2-making-things-clear/#ddd-and-rest)

Por enquanto,eu uso uma API REST como **Presentation Layer**. Isso é **perfeitamente possível** e, mesmse se [ambos os conceitos pareçam opostos, eles trabalham bem juntos](http://dontpanic.42.nl/2012/04/rest-and-ddd-incompatible.html). Lembre-se que uma das **forças** do DDD é a **separação de responsabilidades** graças às [camadas distintas](https://williamdurand.fr/2013/08/07/ddd-with-symfony2-folder-structure-and-code-first/#conclusion). Tenho receio que as pessoas pensem que não seja possível trabalhar com ambos ao mesmo tempo porque [ninguém entende nem REST nem HTTP](http://blog.steveklabnik.com/posts/2011-07-03-nobody-understands-rest-or-http).

Basicamente, [você não deveria expor seu Modelo de Domínio tal como ele é através de uma interface pública](http://stackoverflow.com/questions/10943758/is-it-good-to-return-domain-model-from-rest-api-over-a-ddd-application) como uma API REST. É por isso que você deve [usar Data Transfer Objects](http://neverstopbuilding.net/the-dto-pattern-how-to-generate-php-dtos-quickly-with-dtox/) ([DTOs](http://en.wikipedia.org/wiki/Data_transfer_object)). DTOs são **objetos simples** que **não deve conter nenhuma lógica de negócio** que precisariam de algum teste. Um DTO pode ser visto realmente como um `array` do PHP.

O que você deveria fazer aqui é escrever uma API REST que **exponha recursos que façam sentido para os clientes** (os clientes que consumam sua API), e que nem sempre são 1-1 com o seu Modelo de Domínio. Veja esses recursos como DTOs. Por exemplo, se você estiver lidando com *Orders* e *Payments*, você poderia **criar** um recurso *transcation* para executar a operação de negócio `payOrder(Order $order, Payment $payment)` como proposto por Jonathan Bensaid nos [comentários do artigo anterior]((http://williamdurand.fr/2013/08/07/ddd-with-symfony2-folder-structure-and-code-first/#comment-1006445621):

```
POST /transactions
orderId=123&paymentId=456&...

```

A **Application Layer** irá receber os dados da **Presentation Layer** e chamar a **Domain Layer**. Essa `transação` é um DTO. Ela não é parte do domínio mas é útil para trocar informações entre as camadas de Apresentação e de Aplicação.

No entanto, no artigo anterior, eu tinha como mapear diretamente minha Entidade 'User' para recursos do tipo *users*. Mas se você olhar o processo todo, eu usei um componente Serializer para expor apenas algumas propriedades. Esse é na verdade outro tipo de DTO. A entidade é transformada para expor apenas os dados que são relevantes aos clientes  (os clientes que consomem sua API). Então isso é ok (o suficiente)!

Além do mais, veja que os métodos HTTP delineam explicitamente comandos e consultas. Isso significa que **Command Query Responsibility Separation** [CQRS](http://martinfowler.com/bliki/CQRS.html) **pode ser mapeado diretamente com o HTTP**. É isso aí!

## Então, o que vem por aí[¶](https://williamdurand.fr/2013/08/20/ddd-with-symfony2-making-things-clear/#so-whats-next)

Nessa série, vou introduzir um négocio mais complexo, não se preocupe! Com sorte, vou ser claro o suficiente para explicar minhas escolhas, e resolver alguns dos entendimentos incorretos sobre DDD, RAD e REST.

No próximo artigo, vou introduzir a **Camada de Apresentação**, nos Value Objects como o `Name`, e mais sobre **DDD**! No fim do próximo post, você terá basicamente uma aplicação "tipo CRUD". Sim, eu sei... [CRUD é um anti-pattern](http://verraes.net/2013/04/crud-is-an-anti-pattern/), mas isso não significa que dva ser evitado o tempo todo. Criar novos *users* faz sentido apesar de tudo.

O terceiro post permitirá que você crie quase tudo que você precisará nas diferentes camadas DDD para construir um domínio forte e poderoso, com lógica complexa, e assim por diante. Você pode não entender por que DDD é ótimo até ali, então não entre em pânico e mantenha-se ligado!
