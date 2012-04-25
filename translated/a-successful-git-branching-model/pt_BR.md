#Um modelo bem sucedido para branch no Git

>Tradução livre do artigo "A successful Git branching model", disponível no site
>http://nvie.com/posts/a-successful-git-branching-model/, publicado em 05 de
>janeiro de 2010.

Nesse post apresento o modelo de desenvolvimento que comecei a usar em todos os
meus projetos (tanto no trabalho como nos pessoais) mais ou menos um ano
atrás e que se revelou ser bem sucedido. Eu venho pensando em escrever sobre
isso já faz um tempo, mas nunca tinha encontrado o momento para fazê-lo de
forma cuidadosa, até agora.

![Screen-shot-2009-12-24-at-11.32.03][Screen-shot-2009-12-24-at-11.32.03]

Esse modelo utiliza o [Git][Git] como ferramenta para versionamento de todo o meu
código fonte.

##Por que o git?

Para uma discussão aprofundada sobre os pros e os contras do Git comparado
aos sistemas centralizados de controle de versão, [veja][see] [a][the]
[web][web]. Existem várias "guerrinhas" rolando por aí. Como desenvolvedor,
eu prefiro o Git atualmente sobre todos as outras ferramentas. O Git
realmente mudou a maneira com que os desenvolvedores pensam sobre merging e
branching. No mundo clássico do CVS/Subversion do qual eu vim,
merging/branching sempre foi considerado um pouco assustador ("cuidado com os
conflitos de merge, eles mordem!") e era algo que você fazia de vez em quando.

Mas com o Git, essas ações são extremamente simples e fáceis, e elas são
consideradas uma das partes principais do seu fluxo de trabalho diário, de
verdade. Por exemplo, nos [livros][books] de CVS/Subversion, branching e
merging começa a ser discutido nos últimos capítulos (para usuários avançados),
enquanto em [todo][every_1] [livro][book] do [Git][Git_1], o assunto já é coberto
no capítulo 3 (básico).

Por consequência de sua simplicidade e natureza repetitiva, o branching e o
merging não são mais coisas para se ter medo. As ferrramentas de controle de
versão estão aí para auxiliar nesse assunto mais do que tudo.

Tendo o suficiente sobre as ferramentas, vamos nos aprofundar no modelo de
desenvolvimento. O modelo que vou apresentar aqui não é essencialmente nada
mais do que um conjunto de procedimentos que todo membro da equipe precisa
seguir para que se tenha um processo gerenciável de desenvolvimento de
software.

##Descentralizado, mas centralizado

A configuração do repositório que usamos e que funciona bem com esse modelo de
branching é aquele com um repositório central "truth(verdade)". Perceba que esse
repositório é apenas *considerado* como central (isso porque o Git é um DVCS,
Distributed Version Control System ou Sistema Descentralizado de Controle de
Versão, e não existe nada similar tecnicamente a um reposítorio central).
Iremos nos referir a esse repositório como ``origin``, uma vez que esse nome
é familiar para todos usuários do Git.

![centr-decentr][centr-decentr]

Todo desenvolvedor faz *pulls* e *pushes* no origin. Mas paralelamente às
relações centralizadas de push-pull, cada um dos desenvolvedores também pode
fazer pull das mudanças dos outros colegas para formar subequipes. Por exemplo,
isso poderia ser útil para que dois os mais desenvolvedores trabalhassem junto
em um nova funcionalidade grande, antes de fazer push prematuramente do
trabalho em andamento para ``origin``. Na figura acima, temos subequipes da
Alice com o Bob, da Alice com o David e da Clair com o David.

Tecnicamente, isso não é nada mais do que a Alice definido um Git remote,
chamado de ``bob``, apontando para o repositório do Bob e vice-versa.

##Os branches principais

![bm002][bm002]

No núcleo, o modelo de desenvolvimento é fortemente inspirado pelos modelos
existentes lá dentro. O repositório central guarda dois branches principais
com um tempo de vida infinito:

* ``master``
* ``develop``

O branch ``master`` em ``origin`` deve ser familiar para todo usuário do Git.
Paralelamente ao branch ``master``, tem outro branch chamado ``develop``.

Consideramos o ``origin/master`` como sendo o branch principal onde o código
fonte de ``HEAD`` sempre reflete um estado pronto para ir para produção.

Consideramos ``origin/develop`` como sendo o branch principal ondeo o código
fonte de ``HEAD`` sempre reflete um estado com as últimas alterações no
desenvolvimento entregues para a próxima versão. Alguns podem chamá-lo
de "branch de integração". É a partir daqui que todos os builds noturnos são
automaticamente gerados.

Quando o código fonte no branch ``develop`` atinge um ponto estável e está
pronto para ser lançado, todas as alterações devem ser mescladas no
branch ``master`` de alguma forma e em seguida etiquetadas com um número de
versão. A forma como isso é feito será discutido mais à frente.

Dessa forma, toda vez que as alterações são mescladas em ``master``, vira
por definição uma nova versão de produção. Tendemos a ser bem
rígidos com isso pois, teoricamente, poderíamos usar um hook script do Git
para fazer o build automático e subir nosso software para os servidores de
produção cada vez que for feito um commit no ``master``.

##Os branches auxiliares

Ao lado dos branches principais ``master`` e ``develop``, nosso modelo de
desenvolvimento utiliza uma série de branches auxiliares para ajudar no
desennvolvimento em paralelo entre os membros da equipe, facilitar o rastreamento
das funcionalidades, preparar as versões de produção e para auxiliar no correção
rápida de problemas diretamente no ambiente de produção. Diferentemente dos
branches principais, esses branches sempre tem um tempo de vida limitado, uma
vez que eles serão apagados em algum momento.

Os diferentes tipos de branches que podemos usar são:

* Feature branches
* Release branches
* Hotfix branches

Cada um deles tem um objetivo específico e seguem regras estritas que diz
coisas como de qual branch eles podem se originar e quais branches serão
utilizados para fazer os merges. Vamos passar por eles em um minuto.

Não tem nada "especial" nesses branches do ponto de vista técnico. O tipo do
branch é classificado pelo modo que o utilizamos. Eles são os bons e velhos
branches do Git.

###Feature branches

![fb][fb]

* Originam de: ``develop``
* Mesclam em: ``develop``
* Convenção para nomes: qualquer um, exceto ``master``, ``develop``, ``release-*`` e ``hotfix-*``

Os feature branches (alguma vezes chamados de topic branches) são utilizados para desenvolver
novas funcionalidades para o a próxima ou para uma versão futura. Quando se inicia o
desenvolvimento de uma funcionalidade, a versão na qual essa funcionalidade será incorporada
pode muito bem ser desconhecida nesse momento. A essência de um feature branch é que ele
existe enquanto a funcionalidade está em desenvolvimento, mas irá em algum momento ser
mesclado em ``develop`` (para adicionar a nova funcionalidade de forma definitiva na
próxima versão) ou descartado (caso tenha sido uma experiência ruim).

Os feature branches existem geralmente apenas nos repositórios dos desenvolvedores, não no
``origin``.

####Criando um feature branch

Quando começa o trabalho em uma nova funcionalidade, crie ramifique a partir do branch
``develop``.

	$ git checkout -b myfeature develop
	Switched to a new branch "myfeature"
	
####Incorporando uma funcionalidade finalizada em ``develop``

As funcionalidades finalizadas podem ser mescladas no branch ``develop`` de forma definitiva
para serem adicionadas na versão seguinte a ser lançada:

	$ git checkout develop
	Switched to branch 'develop'
	$ git merge --no-ff myfeature
	Updating ea1b82a..05e9557
	(Summary of changes)
	$ git branch -d myfeature
	Deleted branch myfeature (was 05e9557).
	$ git push origin develop

O parâmetro ``--no-ff`` faz com que o merge sempre crie um novo objeto de commit, mesmo se
o merge pudesse ser feito do modo fast-forward. Isso evita que se perca informação de histórico
da existência de um feature branch e agrupa todos os commits que adicionaram aquela
funcionalidade. Compare:
	
![merge-without-ff][merge-without-ff]

No segundo caso, é impossível de enxergar a partir do histórico do Git quais dos objetos
de commit que juntos implementaram uma funcionalidade - você teria que ler manualmente
cada uma das mensagens de log. Reverter uma funcionalidade inteira (i.e. um grupo de commits),
é uma grande dor de cabeça na segunda situação, enquanto que o mesmo é facilmente feito quando se
utiliza o parâmetro ``--no-ff``.

Ok, serão criados mais alguns objetos (vazios) de commit, mas o benefício é muito maior do que
o custo.

Infelizmente, ainda não encontrei um jeito de fazer com que ``--no-ff`` seja o comportamento
padrão do ``git merge``, pois ele realmente deveria ser assim.


###Release branches

* Originam de: ``develop``
* Mesclam em: ``develop`` e ``master``
* Convenção para nomes: ``release-*``

Os release branches auxiliam na preparação de uma nova versão de produção. Eles permitem
que se faça aquela última verificação do projeto. Além disso eles são usados para os minor
bug fixes e preparação dos metadados de uma versão (número da versão, datas dos builds etc.).
Fazendo tudo isso no release branch, o branch ``develop`` fica limpo para receber funcionalidades
para o lançamento da próxima grande versão.

O momento certo para criar um novo release branch a partir de ``develop`` é quando este está
refletindo (quase) o estado desejado do lançamento da próxima versão. Pelos menos as
funcionalidades planejadas para esse build precisam estar mescladas em ``develop`` nesse ponto.
E as funcionalidades planejadas para as futuras versões não podem estar mescladas ainda - elas
devem esperar até a criação do próximo release branch.

É exatamente no início de um release branch que a versão a ser lançada recebe um número de versão - e
não em nenhuma lugar anterior. Até esse momento, o branch ``develop`` continha as alterações para a
"próxima versão", mas não estava certo ainda se essa "próxima versão" se tornaria a 0.3 ou a 1.0, até
que o release branch foi iniciado. Essa decisão é tomada no início do release branch e é feita seguindo
as regras de número de versão desse projeto.

####Criando um release branch

Os release branches são criados a partir do branch ``develop``. Por exemplo, digamos que a versão 1.1.5
seja a versão atual em produção e que tenhamos uma grande versão para sair. O estado de ``develop`` está
pronto para o "next release" e decidimos que ela se tornará a versão 1.2 (e não 1.1.6 nem 2.0). Assim
criamos um branch a partir de lá e damos um nome para ele que reflita o novo número da versão.

	$ git checkout -b release-1.2 develop
	Switched to a new branch "release-1.2"
	$ ./bump-version.sh 1.2
	Files modified successfully, version bumped to 1.2.
	$ git commit -a -m "Bumped version number to 1.2"
	[release-1.2 74d9424] Bumped version number to 1.2
	1 files changed, 1 insertions(+), 1 deletions(-)

Depois de criar um novo branch e fazer o switch nele, nós aumentamos o número da versão nos nossos arquivos.
No nosso caso, ``bump-version.sh`` é um shell script fictício que altera alguns arquivos na nossa cópia local
para refletir a nova versão. (É claro que isso pode ser feito manualmente). Assim, o número da versão
incrementada é comitada.

Esse novo branch pode existir apenas por pouco tempo, até que a versão seja disponibilizada de forma
definitiva. Durante esse período, os bug fixes pode ser aplicados nesse branch (em vez de serem feitos
no ``develop`` branch). Fazer grandes adições de novas funcionalidades aqui é estritamente proibido.
Elas devem ser mescladas em ``develop`` e assim aguardar até a próxima grande versão.

####Finalizando um release branch

Quando o estado do release branch está pronto para se tornar uma versão de verdade, algumas ações são
necessárias para o seu lançamento. Primeiro, o release branch é mesclado em ``master`` (uma vez que
todo commit no ``master`` é uma nova versão por definição, lembrou?). Em seguida, o commit em ``master``
precisam receber uma tag para serem facilmente referenciados como uma versão histórica. Finalmente,
as mudanças feitas no release branch precisam ser mesclados de volta em ``develop``, assim as versões
futuras também conterão os bug fixes.

Os dois primeiros passos no Git:

	$ git checkout master
	Switched to branch 'master'
	$ git merge --no-ff release-1.2
	Merge made by recursive.
	(Summary of changes)
	$ git tag -a 1.2

A versão agora está pronta, e etiquetada para referência futura.

Edição: Você talvez também queira usar os parâmetros ``-s`` or ``-u`` <key> para assinar as tags
com criptografia.

Para manter as alterações efetuadas no release branch, precisamos mesclá-las de volta em ``develop``.
No Git:

	$ git checkout develop
	Switched to branch 'develop'
	$ git merge --no-ff release-1.2
	Merge made by recursive.
	(Summary of changes)

Esse passo pode muito bem levar a um conflito de mesclagem (isso é bem provável, uma vez que alteramos
o número da versão em alguns arquivos). Se isso acontecer, conserte os conflitos e então faça o commit
novamente.

Agora sim estamos com tudo finalizado e o release branch pode ser removido, pois não iremos mais
precisar dele:

	$ git branch -d release-1.2
	Deleted branch release-1.2 (was ff452fe).

###Hotfix branches

![hotfix-branches1][hotfix-branches1]

* Originam de: ``master``
* Mesclam em: ``develop`` e ``master``
* Convenção para nomes: ``hotfix-*``

Os hotfixes branches se parecem muito com os release branches pois eles também tem a função de fazer a
preparação para uma nova versão de produção, embora não planejadas. Eles surgem da necessidade de agir
imediatamente em cima de uma versão que está rodando em produção. Quando um bug crítico em uma versão
de produção precisa ser resolvida imediatamente, um hotfix branch pode ser criado a partir da tag
correspondente do master branch que aponta a versão em produção.

A essência é que o trabalho dos membros da equipe (no branch ``develop``) possa continuar, enquanto
outra pessoa está preparando uma solução rápida para a produção.

####Criando o hotfix branch

Os hotfixes branches são criados a partir do branch ``master``. Por exemplo, digamos que a versão 1.2 seja a
versão atual que está rodando em produção e que esteja causando problemas devido a algum bug grave. Mas as
alterações em ``develop`` ainda são instáveis. Podemos assim criar um hotfix branch e começar a solucionar
o problema:

	$ git checkout -b hotfix-1.2.1 master
	Switched to a new branch "hotfix-1.2.1"
	$ ./bump-version.sh 1.2.1
	Files modified successfully, version bumped to 1.2.1.
	$ git commit -a -m "Bumped version number to 1.2.1"
	[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
	1 files changed, 1 insertions(+), 1 deletions(-)

Não se esqueça de incrementar o número da versão depois de criar o branch!

Depois disso, conserte o bug e faça o commit da solução em um ou mais commits separados.

	$ git commit -m "Fixed severe production problem"
	[hotfix-1.2.1 abbe5d6] Fixed severe production problem
	5 files changed, 32 insertions(+), 17 deletions(-)

####Finalizando um hotfix branch

Quando finalizada, a solução precisa ser mesclada de volta no ``master``, mas também precisa ser
mesclada em ``develop``, para que se garanta que a solução também será incluida na próxima versão.
Isso é bem semelhante a forma como os release branches são finalizado.

Primeiro, atualize o ``master`` e etiquete a versão.

	$ git checkout master
	Switched to branch 'master'
	$ git merge --no-ff hotfix-1.2.1
	Merge made by recursive.
	(Summary of changes)
	$ git tag -a 1.2.1

Edição: Você talvez também queira utilizar os parâmetros ``-s`` or ``-u`` <key> para assinar sua tag
criptograficamente.

Em seguida, inclua o bugfix também em ``develop``:

	$ git checkout develop
	Switched to branch 'develop'
	$ git merge --no-ff hotfix-1.2.1
	Merge made by recursive.
	(Summary of changes)
    
A única exceção à regra aqui é que quando um release branch ainda existe, as alterações do hotfix
precisam ser mescladas nesse release branch em de mesclar em ``develop``. Essa mesclagem do bugfix
no release branch fará que no fim esse acerto seja colocado também em ``develop``, quando o release
branch for finalizado. (Se o trabalho em ``develop`` precisar imediatamente do bugfix e não puder
esperar que o release branch finalize, você pode seguramente também mesclar o bugfix em ``develop``
agora).

Finalmente, remova o branch temporário:

	$ git branch -d hotfix-1.2.1
	Deleted branch hotfix-1.2.1 (was abbe5d6).

##Sumário

Mesmo que não tenha nada de extremamente novo nesse modelo de branching, a visão completa que esse
artigo trouxe se mostrou tremendamente útil em nossos projetos. Ele forma um modelo mental elegante
que é fácil de compreender e permite que os membros da equipe desenvolvam um entendimento compartilhado
dos processos de branching e lançamento de versões.

Uma versão PDF de alta qualidade da imagem é fornecida aqui. Vá em frente e pendure-a na parede
para uma referência rápida em qualquer tempo.

Atualização: E para todos aqueles que pediram: aqui está o [gitflow-model.src.key][gitflow-model.src.key]
da imagem principal do diagrama (Apple Keynote).

![pdf][pdf]
[Git-branching-model.pdf][Git-branching-model.pdf]

Fique a vontade para adicionar comentários!





[Screen-shot-2009-12-24-at-11.32.03]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/Screen-shot-2009-12-24-at-11.32.03.png
[centr-decentr]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/centr-decentr.png
[bm002]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/bm002.png
[fb]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/fb.png
[merge-without-ff]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/merge-without-ff.png
[hotfix-branches1]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/hotfix-branches1.png
[pdf]: http://rogeriopradoj.github.com/translations/images/a-successful-git-branching-model/pdf.png






[Git]: http://git-scm.com
[see]: http://whygitisbetterthanx.com/
[the]: http://www.looble.com/git-vs-svn-which-is-better/
[web]: http://git.or.cz/gitwiki/GitSvnComparsion
[books]: http://svnbook.red-bean.com/
[every_1]: http://book.git-scm.com/
[Git_1]: http://pragprog.com/titles/tsgit/pragmatic-version-control-using-git
[book]: http://github.com/progit/progit
[gitflow-model.src.key]: http://github.com/downloads/nvie/gitflow/Git-branching-model-src.key.zip
[Git-branching-model.pdf]: http://github.com/downloads/nvie/gitflow/Git-branching-model.pdf