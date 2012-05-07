Versionamento Semântico 2.0.0-rc.1
==================================

>Tradução livre do artigo "Semantic Versioning 2.0.0-rc.1", disponível no site
>http://semver.org/

No mundo do desenvolvimento de software e seu gerenciamento, existe um lugar
assustador chamado de "dependency hell" ou inferno das dependências. Quanto
mais o seu sistema cresce e quanto mais pacotes você integra ao seu software,
mais perto você está de se encontrar, algum dia, nesse abismo dos desesperados.

Em sistemas com muitas dependências, o lançamento de novas versões dos pacotes
pode rapidamente se tornar um pesadelo. Se as especificações das dependências
forem muito amarradas, você periga entrar em um "version lock" ou travamento
por causa de versões (a impossibilidade de atualizar um pacote sem antes lançar
novas versões de todos pacotes dependentes). Se as dependências forem
especificadas de forma muito solta, você irá cair inevitavelmente numa "version
promiscuity" ou promiscuidade de versões (fazendo com que a compatibilidade
com versões anteriores passe de um ponto razoável). Dependency hell é onde você
está quando "version lock" e/ou "version promiscuity" te impedem de fazer o seu
projeto andar de forma fácil e segura.

Como uma solução para esse problema, eu proponho um conjunto simples de regras
e requisitos que determina como os números das versões são atribuídos e
incrementados. Para que esse sistema funcione, você primeiro precisa declarar
uma API pública. Isso pode ser feito com documentação ou pode ser feita no
próprio código. Independentemente disso, é importante que a API seja clara
e precisa. Uma vez que você identificou sua API pública, você indica as
mudanças dela com incrementos específicos no número da versão dela. Considere
um formato de versão assim X.Y.Z (Major.Minor.Patch, ou Maior.Menor.Correção).
As correções de bug que não afetam a API incrementam a versão patch, mudanças/
adições compatíveis com a API anterior incrementam a versão minor, e mudanças
incompatíveis com a API anterior incrementam a versão major.

Eu chamo esse sistema de "Versionamento Semântico." Nesse esqueme, os números
de versões e a maneira como eles mudam trazem sentido sobre o código ali
embaixo e sobre o que foi modificado de uma versão para outra.

Especificação do Versionamento Semântico (SemVer)
-------------------------------------------------

[en](http://www.ietf.org/rfc/rfc2119.txt) [pt](http://www.normes-internet.com/normes.php?rfc=rfc2119&lang=pt)

As palavras-chave "PRECISA", "NÃO PODE", "NECESSÁRIO", "DEVERÁ", "NÃO DEVERÁ", "DEVERIA",
"NÃO DEVERIA", "RECOMENDADO", "PODE", e "OPCIONAL" nesse documento devem ser
interpretadas como descrito na RFC 2119. 

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be
interpreted as described in RFC 2119.

1. O software que utiliza Versionamento Semântico PRECISA declarar uma API
pública. Essa API pode ser declarada no próprio código ou existir de forma
estrita na documentação. Uma vez pronta, ela deve ser precisa e compreensiva.

1. Um número de versão normal PRECISA ter o formato X.Y.Z onde X, Y e Z são
inteiros não negativos. O X é a versão maior, Y é a versão menor e Z é a
versão de correção. Cada um dos elementos PRECISA aumentar numericamente de um
em um. Por exemplo: 1.9.0 -> 1.10.0 -> 1.11.0.

1. Uma vez que o pacote versionado for lançado, os conteúdos daquela versão
NÃO PODE ser modificado. Qualquer modificação precisa ser liberado como uma
nova versão.

1. A versão maior zero (0.y.z) é para desenvolvimento inicial. Tudo pode ser
alterado a qualquer momento. A API pública não deve ser considerada estável.

1. Version 1.0.0 define a API pública. O modo que cada número de versão é
incrementado depois desse release depende da API pública e como ela é
alterada.

1. Uma versão de correção Z (x.y.Z | x > 0) PRECISA ser incrementada apenas
se forem introduzidas correções de bugs compatíveis com a versão anterior.
Um "bug fix" ou correção de bug é definido como uma mudança interna que
conserta um comportamento incorreto.

1. Uma versão menor Y (x.Y.z | x > 0) PRECISA ser incrementada se funcionalidades
novas e compatíveis com a versão anterior forem introduzidas na API pública. Ela
PRECISA ser incrementada se qualquer funcionalidade da API pública for marcada
como obsoleta. Ela PODE ser incrementada se novas funcionalidades ou melhorias
substanciais forem introduzidas no código privado. Ela PODE incluir mudanças
do nível correção. A versão de correção PRECISA ser reinicializada para 0 quando
a versão menor for incrementada.

1. Uma versão maior X (X.y.z | X > 0) PRECISA ser incrementada se qualquer mudança
incompatível com a versão anterior for introduzida na API pública. Ela PODE incluir
mudanças dos níveis menor e correção. As versões de correção e menores PRECISAM ser
reinicializadas para 0 quando a versão maior for incrementada.

1. Uma versão "pre-release" ou pré-lançamento PRECISA ser indicada por meio
do acréscimo de um hífen e uma série de pontos identificadores de separação
imediatamente depois da versão de correção. Os identificadores PRECISAM ser
compostos apenas de alfanúmericos ASCII e traço [0-9A-Za-z-]. As versões
pré-lançamento atendem como uma versão normal, mas tem uma precedência menor do
que essa versão normal associada. Exemplos: 1.0.0-alpha, 1.0.0-alpha.1, 1.0.0-0.3.7,
1.0.0-x.7.z.92.

1. Uma "build version" ou versão compilada PRECISA ser indicada pelo acréscimo
de um sinal "de mais" e uma série de pontos identificadores de separação imediatamente
depois da versão de correção ou da versão pré-lançamento. Os identificadores PRECISAM
ser compostos apenas de alfanúmericos ASCII e traço [0-9A-Za-z-]. As versões
compiladas atendem como um versão normal e tem precedência maior do que essa versão
normal associada. Examples: 1.0.0+build.1, 1.3.7+build.11.e0f985a.

1. A precedência PRECISA ser calculada separando a versão pelos identificadores
maior, menor, correção, pré-lançamento e compilada, nessa ordem. As versões
maior, menor e de correção são sempre comparadas numericamente. A precedência nas
versões pré-lançamento e compiladas PRECISAM ser determinadas comparando cada ponto
identificador de separação dessa forma: identificadores consistidos apenas de dígitos
são comparados numericamente e identificadores com letras ou pontos são comparados
lexicamente de acordo com o ordenamento ASCII. Os identificadores númericos sempre
tem precedência menor do que os identificadors não númericos. Exemplo:
1.0.0-alpha < 1.0.0-alpha.1 < 1.0.0-beta.2 < 1.0.0-beta.11 <
1.0.0-rc.1 < 1.0.0-rc.1+build.1 < 1.0.0 < 1.0.0+0.3.7 < 1.3.7+build <
1.3.7+build.2.b8f12d7 < 1.3.7+build.11.e0f985a.

Por Que Usar Versionamento Semântico?
-------------------------------------

Essa não é uma ideia nova nem revolucionária. Na verdade, provavelmente você
já faz algo próximo disso. O problema é que "próximo" não é bom o suficiente.
Sem seguir conformemente a algum tipo de especificação formal, os números de
versão não tem utilidade essencial para o gerenciamento de dependências. Dando
nomes e definições claras para as ideias acima, fica fácil comunicar suas
intenções para os usuários do seu software. Uma vez que essas intenções estão
claras e flexíveis (mas não tão flexíveis), as especificações das dependências
podem ser feitas finalmente.

Um exemplo simples demonstrará como o Versionamento Semântico pode fazer
com que o inferno das dependências vire coisa do passado. Considere uma biblioteca
chamada "Firetruck". Ela requer um pacote Versionado Semanticamente que se chama
"Ladder". No momento que a Firetruck estava sendo criada, Ladder estava na versão
3.1.0. Como Firetruck usa algumas funcionalidades que foram acrescentadas inicialmente
em 3.1.0, você pode seguramente especificar a dependência do Ladder como sendo maior
ou igual a 3.1.0, mas menor do que 4.0.0. Agora, quando as versões 3.1.1 e 3.2.0 do
Ladder ficarem disponíveis, você pode colocá-las no seu sistema de gerenciamento de
pacotes e saber que elas serão compatíveis com o seu software dependente que já
existe.

Como desenvolvedor responsável que você é, lógico, você vai querer verificar
todas as atualizações nas funções dos pacotes como foi anunciado. O mundo
real é um lugar bagunçado; não tem nada que possamos fazer sobre isso além
de ficar alerta. O que você pode fazer é deixar o Versionamento Semântico
te fornecer um modo são de lançar e atualizar pacotes em precisar criar novas
versões dos pacotes dependentes, te economizando tempo e aborrecimento.

Se tudo isso parece interessante, tudo o que você precisa fazer para começar a
usar o Versionamento Semântico é declarar que você já está fazendo isso e
então seguir as regras. Faça um link para esse site a partir do seu README
assim os outros saberão das regras e também poderão se beneficiar delas.

FAQ
---

### Como eu deveria fazer com as revisões na fase inicial de desenvolvimento 0.y.z?

A forma mais simples de fazer isso é iniciar sua versão de desenvolvimento na
versão 0.1.0 e então incrementar a versão menor em cada um dos lançamentos
seguintes.

### Como eu sei quando devo lançar a versão 1.0.0?

Se o seu software estiver sendo usado em produção, provavelmente ele já deve
estar na versão 1.0.0. Se você tem uma API estável que os usuários já dependem
dela, você deve ter a versão 1.0.0. Se você está se preocupando bastante com
compatibilidade com versões anteriores, provavelmente você já deve estar com
a versão 1.0.0.

### Isso não desencoraja o desenvolvimento rápido e a interação rápida?

A versão maior zero é toda sobre desenvolvimento rápido. Se você estiver
alterando a API todo dia você ainda deve estar na versão 0.x.x ou em um
branch de desenvolvimento separado trabalhando para a próxima versão maior.

### Se até as menores alterações incompatíveis com as versões anteriores na API pública requerem um aumento na versão maior, eu não eu vou acabar chegando muito rápido na versão 42.0.0?

Essa é uma questão de desenvolvimento responsável e visão. Alterações incompatíveis
não devem ser introduzidas levianamente em um software que já tem bastante
código dependente. O custo para atualizá-lo pode ser significativo.
Incrementar uma versão maior para liberar alterações incompatíveis significa que
você precisa pensar sobre o impacto de suas mudanças, e avaliar a relação
custo/benefício envolvida.

### Documentar toda a API pública dá muito trabalho!

É sua responsabilidade como um desenvolvedor profissional documentar
adequadamente o software que é destinado para ser utilizado por outros.
A complexidade no gerenciamento de software é uma parte imensamente importante
na manutenção da eficiência do projeto, e isso fica complicado se ninguém souber
como utilizar o seu sistema, ou quais métodos são seguros para utilizar.
No longo prazo, o Versionamento Semântico e a insistência em fazer uma API
pública bem definida pode fazer com que tudo e todos andem sem problemas.

### O que devo fazer se acidentalmente colocar uma alteração incompatível como uma versão menor?

Logo que você perceber que você quebrou a especificação do Versionamento Semântico,
corrija o problema e libere uma nova versão menor que corrija o problema e
restaure a compatibilidade com versões anteriores. Lembre-se, é inaceitável
modificar lançamentos versionados, não importa o motivo. Se for apropriado,
documente a versão incorreta e informe seus usuários sobre o problema para
que eles fiquem cientes dessa versão.

### O que devo fazer se eu atualizar minhas próprias dependências sem alterar a API pública?

Isso seria considerado possível desde que não afetasse a API pública.
O software que depende de forma explícita das mesmas dependências que
seu pacote deveria ter suas próprias especificações de dependência e
o autor iria perceber qualquer conflito. Determinar se a mudança é
de nível correção ou nível menor depende se você atualizou suas dependências
para corrigir um bug ou para introduzir uma nova funcionalidade. Eu geralmente
esperaria código adicional no último caso, o que seria obviamente um incremento
da versão menor.

### O que devo fazer se um bug que tinha sido corrigido retornar ao código para deixá-lo compatível com a API pública (i.e. o código ficou incorretamente fora de sincronia com a documentação da API pública)?

Utilize o seu melhor julgamento. Se você tiver uma audiência enorme que será
impactada drasticamente pela mudança no comportamento em relação ao que a API
pública estava projetada para fazer, então seria melhor fazer o lançamento de
uma versão maior, mesmo que a correção fosse considerada estritamente uma versão
de correção. Lembre-se, o Versionamento Semântico é baseado em transmitir significado
por meio das alterações nos números das versões. Se essas alterações forem importantes
para seus usuários, utilize o número da versão para informá-los.

### Como devo lidar com a depreciação das funcionalidades?

Depreciar funcionalidades existentes é uma parte normal do desenvolvimento de software
e é frequentemente necessário para fazer com que se tenha avanços. Quando você
deprecia parte da sua API pública, você deve fazer duas coisas: (1) atualize sua
documentação para que seus usuários saibam da mudança, (2) crie uma nova versão menor
com a depreciação aparecendo. Antes que você remova completamente a funcionalidade em
uma nova versão maior deve exister pelo menos uma versão menor que contenha a depreciação
para que os usuários possam fazer uma transição sem problemas para a nova API.

Sobre
-----

A especificação do Versioamento Semântico tem autoria de [Tom
Preston-Werner](http://tom.preston-werner.com), inventor do Gravatars
e co-fundador do Github.

Se você quiser deixar algum feedback, por favor [abra uma issue no
GitHub](https://github.com/mojombo/semver/issues).

Licença
-------

Creative Commons - CC BY 3.0
http://creativecommons.org/licenses/by/3.0/
