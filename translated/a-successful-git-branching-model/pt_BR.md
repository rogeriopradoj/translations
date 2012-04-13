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

É exatamente no início de um release branch que a versão a ser lançada

It is exactly at the start of a release branch that the upcoming release gets assigned a version number—not any earlier. Up until that moment, the ``develop`` branch reflected changes for the “next release”, but it is unclear whether that “next release” will eventually become 0.3 or 1.0, until the release branch is started. That decision is made on the start of the release branch and is carried out by the project’s rules on version number bumping.

####Creating a release branch
Release branches are created from the ``develop`` branch. For example, say version 1.1.5 is the current production release and we have a big release coming up. The state of ``develop`` is ready for the “next release” and we have decided that this will become version 1.2 (rather than 1.1.6 or 2.0). So we branch off and give the release branch a name reflecting the new version number:

	$ git checkout -b release-1.2 develop
	Switched to a new branch "release-1.2"
	$ ./bump-version.sh 1.2
	Files modified successfully, version bumped to 1.2.
	$ git commit -a -m "Bumped version number to 1.2"
	[release-1.2 74d9424] Bumped version number to 1.2
	1 files changed, 1 insertions(+), 1 deletions(-)
After creating a new branch and switching to it, we bump the version number. Here, ``bump-version.sh`` is a fictional shell script that changes some files in the working copy to reflect the new version. (This can of course be a manual change—the point being that some files change.) Then, the bumped version number is committed.

This new branch may exist there for a while, until the release may be rolled out definitely. During that time, bug fixes may be applied in this branch (rather than on the ``develop`` branch). Adding large new features here is strictly prohibited. They must be merged into ``develop``, and therefore, wait for the next big release.

####Finishing a release branch
When the state of the release branch is ready to become a real release, some actions need to be carried out. First, the release branch is merged into ``master`` (since every commit on ``master`` is a new release by definition, remember). Next, that commit on ``master`` must be tagged for easy future reference to this historical version. Finally, the changes made on the release branch need to be merged back into ``develop``, so that future releases also contain these bug fixes.

The first two steps in Git:

	$ git checkout master
	Switched to branch 'master'
	$ git merge --no-ff release-1.2
	Merge made by recursive.
	(Summary of changes)
	$ git tag -a 1.2
The release is now done, and tagged for future reference.
Edit: You might as well want to use the ``-s`` or ``-u`` <key> flags to sign your tag cryptographically.

To keep the changes made in the release branch, we need to merge those back into ``develop``, though. In Git:

	$ git checkout develop
	Switched to branch 'develop'
	$ git merge --no-ff release-1.2
	Merge made by recursive.
	(Summary of changes)
This step may well lead to a merge conflict (probably even, since we have changed the version number). If so, fix it and commit.

Now we are really done and the release branch may be removed, since we don’t need it anymore:

	$ git branch -d release-1.2
	Deleted branch release-1.2 (was ff452fe).
###Hotfix branches

![hotfix-branches1][hotfix-branches1]

May branch off from: ``master``
Must merge back into: ``develop`` and ``master``
Branch naming convention: ``hotfix-*``

Hotfix branches are very much like release branches in that they are also meant to prepare for a new production release, albeit unplanned. They arise from the necessity to act immediately upon an undesired state of a live production version. When a critical bug in a production version must be resolved immediately, a hotfix branch may be branched off from the corresponding tag on the master branch that marks the production version.

The essence is that work of team members (on the ``develop`` branch) can continue, while another person is preparing a quick production fix.

####Creating the hotfix branch
Hotfix branches are created from the ``master`` branch. For example, say version 1.2 is the current production release running live and causing troubles due to a severe bug. But changes on ``develop`` are yet unstable. We may then branch off a hotfix branch and start fixing the problem:

	$ git checkout -b hotfix-1.2.1 master
	Switched to a new branch "hotfix-1.2.1"
	$ ./bump-version.sh 1.2.1
	Files modified successfully, version bumped to 1.2.1.
	$ git commit -a -m "Bumped version number to 1.2.1"
	[hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
	1 files changed, 1 insertions(+), 1 deletions(-)
Don’t forget to bump the version number after branching off!

Then, fix the bug and commit the fix in one or more separate commits.

	$ git commit -m "Fixed severe production problem"
	[hotfix-1.2.1 abbe5d6] Fixed severe production problem
	5 files changed, 32 insertions(+), 17 deletions(-)
Finishing a hotfix branch

When finished, the bugfix needs to be merged back into ``master``, but also needs to be merged back into ``develop``, in order to safeguard that the bugfix is included in the next release as well. This is completely similar to how release branches are finished.

First, update ``master`` and tag the release.

	$ git checkout master
	Switched to branch 'master'
	$ git merge --no-ff hotfix-1.2.1
	Merge made by recursive.
	(Summary of changes)
	$ git tag -a 1.2.1
Edit: You might as well want to use the ``-s`` or ``-u`` <key> flags to sign your tag cryptographically.

Next, include the bugfix in ``develop``, too:

	$ git checkout develop
	Switched to branch 'develop'
	$ git merge --no-ff hotfix-1.2.1
	Merge made by recursive.
	(Summary of changes)
The one exception to the rule here is that, when a release branch currently exists, the hotfix changes need to be merged into that release branch, instead of ``develop``. Back-merging the bugfix into the release branch will eventually result in the bugfix being merged into ``develop`` too, when the release branch is finished. (If work in ``develop`` immediately requires this bugfix and cannot wait for the release branch to be finished, you may safely merge the bugfix into ``develop`` now already as well.)

Finally, remove the temporary branch:

	$ git branch -d hotfix-1.2.1
	Deleted branch hotfix-1.2.1 (was abbe5d6).
##Summary

While there is nothing really shocking new to this branching model, the “big picture” figure that this post began with has turned out to be tremendously useful in our projects. It forms an elegant mental model that is easy to comprehend and allows team members to develop a shared understanding of the branching and releasing processes.

A high-quality PDF version of the figure is provided here. Go ahead and hang it on the wall for quick reference at any time.

Update: And for anyone who requested it: here’s the [gitflow-model.src.key][gitflow-model.src.key] of the main diagram image (Apple Keynote).

![pdf][pdf]
[Git-branching-model.pdf][Git-branching-model.pdf]

Feel free to add your comments!







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