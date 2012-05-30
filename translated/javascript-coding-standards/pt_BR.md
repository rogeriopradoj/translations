Convenções de Código para a Linguagem de Programação JavaScript
===============================================================

>Tradução livre do artigo "Code Conventions for the JavaScript Programming
>Language", disponível no site http://javascript.crockford.com/code.html.

Esse é um conjunto de convenções e regras de código para usar na
programação com JavaScript. Ele foi inspirado no documento [Code Conventions
for the Java Programming Language](http://java.sun.com/docs/codeconv/) da
[Sun](http://www.sun.com/). É claro que ele foi modificado profundamente
porque
[o JavaScript não é o Java](http://javascript.crockford.com/javascript.html).

O valor de um software para uma organização é diretamente
proporcional a qualidade da sua base de código. Durante seu tempo de vida,
um programa será mexido por muitos pares de mãos e de olhos. Se um programa
for capaz de comunicar claramente sua estrutura e suas características,
será menos provável que ele quebre quando for modificado num futuro
nunca-muito-distante.

Convenções de código podem ajudar a reduzir a fragilidade dos programas.

Todo seu código JavaScript é enviado diretamente ao público. Esse código
deveria ter sempre uma qualidade de publicação.

Isso faz diferença.

Arquivos JavaScript
-------------------

Arquivos JavaScript devem ser armazenados e disponibilizados como arquivos
`.js`.

O código JavaScript não deve ser embutido em arquivos HTML a menos que o
código seja específico de uma sessão única. Código dentro do HTML aumenta
significativamente o tamanho da página e impede que se faça otimização
por meio de cache ou compressão.

As tags `<script src=`filename`.js>` devem ser colocadas no corpo do documento
o mais tarde possível. Isso diminui os efeitos do atraso no carregamento dos outros
componentes da página devido ao carregamento do script.
Não há necessidade de usar os atributos `language` ou `type`. É o servidor,
e não a tag script, que determina o tipo MIME.


Recuo
-----

O tamanho do recuo é quatro espaços. O uso de tabulação deve ser evitado
porque (isto foi escrito no século XXI) ainda não existe um padrão para o
posicionamento das paradas de tabulação. O uso de espaços pode gerar um
arquivo maior, mas o tamanho não é significativo em redes locais, e a
diferença é eliminada por meio de
[minification](http://yuiblog.com/blog/2006/03/06/minification-v-obfuscation/).


Tamanho da Linha
----------------

Evite linhas maiores que 80 caracteres. Quando uma declaração não couber em
uma única linha, é necessário que ela seja quebrada. Coloque a quebra depois
de um operador, idealmente depois de uma vírgula. Uma quebra depois de um
operador diminui a probabilidade de que um erro de "copia-e-cola" seja
mascarado pela inserção de um ponto e vírgula. A linha seguinte deve ser
identada com 8 espaços.

Comentários
-----------

Seja generoso com os comentários. Eles são úteis para passar informações
que serão lidas daqui um tempo por pessoas (possivelmente você mesmo) que
terão que entender o que você fez. Os comentários devem ser bem escritos
e claros, da mesma forma que o código que eles estão anotando. Eventuais
pitadas de humor podem ser bem-vindas. Frustações e ressentimentos não.

É importante que os comentários sejam mantidos atualizados. Comentários
incorretos podem deixar ainda mais difícil a leitura e o entendimento dos
programas.

Deixe comentários que façam sentido. Se foque no que não for visível de forma
imediata. Não gaste o tempo do leitor com coisas desse tipo

        i = 0; // Define i como zero.

Em geral use comentários de linha. Guarde os comentários de bloco para
documentação formal e outros.

Declaração de Variáveis
-----------------------

Todas as variáveis devem ser declaradas antes de serem utilizadas. O
Javascript não obriga você a fazer isso, mas dessa forma o programa fica
mais fácil de ler e facilita a detecção de variáveis não declaradas
que podem se tornar
[globais](http://yuiblog.com/blog/2006/06/01/global-domination/)
implícitas. Variáveis globais implícitas nunca devem ser usadas.

As declarações `var` devem ser as primeiras a aparecer no corpo de uma
função.

É preferível que cada variável receba sua própria linha e comentário. Elas
devem ser listadas em ordem alfabética.

        var currentEntry; // entrada atualmente selecionada da tabela
        var level;        // nível do recuo
        var size;         // tamanho da tabela

O JavaScript não possui escopo de bloco, assim definir variáveis dentro
de blocos pode confundir programadores que tem experiência com outras
linguagens da família C. Define todas as variáveis no topo da função.

A utilização de variáveis globais deve ser minimizado. Varíaveis globais
implícitas nunca devem ser utilizadas.

Declarações de Funções
----------------------

Todas as funções devem ser declaradas antes de serem utilizadas.
Funções internas devem ser precedidas da declaração `var`. Isso ajuda
a deixar claro que as variáveis estão incluídas nesse escopo.

Não deve ter nenhum espaço entre o nome da função e o `(`
(parêntese da esquerda) da sua lista de parâmetros. Deve ter um espaço
entre o `)` (parêntese da direita) e a `{` (chave da esquerda) que
inicia o corpo da declaração. O próprio corpo deve ser recuado com
quatro espaços. A `}` (chave da direita) é alinhado com a linha que contém
o início da declaração da função.

        function outer(c, d) {
            var e = c * d;

            function inner(a, b) {
                return (e * a) + b;
            }

            return inner(0, 1);
        }

Essa convenção funciona bem com o JavaScript porque nele as funções e objetos
literais podem ser colocados em qualquer lugar onde uma expressão seja
permitida. Isso proporciona melhor legibilidade em funções embutidas
e em estruturas complexas. 

        function getElementsByClassName(className) {
            var results = [];
            walkTheDOM(document.body, function (node) {
                var a;                  // array dos nomes das classes
                var c = node.className; // o nome da classe nó
                var i;                  // contador do loop

    // Se o nó tiver um nome de classe, divida-o numa lista de nomes simplificados.
    // Se algum deles corresponder com o nome requisitado, então acrescente o nó ao conjunto de resultados.

                if (c) {
                    a = c.split(' ');
                    for (i = 0; i < a.length; i += 1) {
                        if (a[i] === className) {
                            results.push(node);
                            break;
                        }
                    }
                }
            });
            return results;
        }

Se uma função literal for anônima, deve ter um espaço entre a palavra
`function` e o `(` (parêntese da esquerda). Se o espaço for omitido, pode
parecer que o nome da função é `function`, que é uma leitura errada.

        div.onclick = function (e) {
            return false;
        };

        that = {
            method: function () {
                return this.datum;
            },
            datum: 0
        };

A utilização de funções globais deve ser minimizado.

Quando uma função é invocada imediatamente, a expressão de invocação deve
ser completamente envolvida por parênteses para que seja claro que o valor
a ser produzido é resultado da função e não a própria função.

    var collection = (function () {
        var keys = [], values = [];

        return {
            get: function (key) {
                var at = keys.indexOf(key);
                if (at >= 0) {
                    return values[at];
                }
            },
            set: function (key, value) {
                var at = keys.indexOf(key);
                if (at < 0) {
                    at = keys.length;
                }
                keys[at] = key;
                values[at] = value;
            },
            remove: function (key) {
                var at = keys.indexOf(key);
                if (at >= 0) {
                    keys.splice(at, 1);
                    values.splice(at, 1);
                }
            }
        };
    }());

Nomes
-----

Os nomes devem ser formados pelas 26 letras maísculas ou mínusculas
(`A` .. `Z`, `a` .. `z`), os 10 dígitos (`0` .. `9`) e `_` (sublinhado).
Evite usar caracteres internacionais porque eles podem não ser bem lidos
ou entendidos em todos os lugares. Não use o `$` (cifrão) nem a `\`
(contrabarra) nos nomes.

Não use `_` (sublinhado) como o primeiro caractere de um nome. Algumas vezes
isso é usado para indicar privacidade, mas não fornece isso de verdade.
Se a privacidade for importante, utilize mecanismos para implementar
[membros privados](http://javascript.crockford.com/private.html). Evite
convenções que indiquem incompetência.

A maior parte das variáveis e funções deve começar com letra minúscula.

Funções construtoras que precisam ser utilizadas com o
[prefixo](http://yuiblog.com/blog/2006/11/13/javascript-we-hardly-new-ya/)
`new` devem começar com uma letra maíscula. O Javascript mostra erros
de tempo de compilação ou de tempo de execução se um `new` obrigatório for
omitido. Coisas ruins pode acontecer se `new`  não for utilizado, então
a convenção das maiúsculas é a única defesa que nós temos.

As variáveis globais devem ser com todas as letras maiúsculas.
(O JavaScript não tem macros nem constantes, por isso não faz muito sentido
utilizar todas as letras maiúsculas para indicar funcionalidades que o
JavaScript não tem).

Declarações
-----------

### Declarações Simples

Cada linha deve conter no máximo uma declaração. Coloque um `;` (ponto
e vírgula) no fim de cada declaração simples. Perceba que uma declaração
de atribuição que está atribuindo uma função literal ou um objeto literal
continua sendo uma declaração de atribuição e precisa terminar com um
ponto e vírgula.

O JavaScript permite que uma expressão seja usada como uma declaração.
Isso pode mascarar alguns erros, particularmente quando for inserido
um ponto e vírgula. As únicas expressões que devem ser utilizadas como
declarações são as atribuições e as invocações.

### Declarações Compostas

As declarações compostas são declarações que contém listas de declarações
colocadas entre `{ }` (chaves).

-   As declarações embutidas devem ser recuadas com mais 4 espaços.
-   A `{` (chave da esquerda) deve ficar no fim da linha que inicia
    a declaração composta.
-   A `}` (chave da direita) deve começar uma linha e ser recuada
    para se alinhar com o início da linha que contém a `{` (chave da esquerda)
    correspondente.
-   As chaves devem ser utilizadas em volta de todas as declarações, mesmo nas
    declarações simples, quando elas forem parte de uma estrutura de controle,
    como um declaração `if` ou `for`. Isso facilita a adição de declarações
    sem acidentalmente colocar novos bugs.

### Rótulos

Os rótulos nas declarações são opcionais. Apenas essas declarações devem
ser rotuladas: `while`, `do`, `for`, `switch`.

### Declaração `return`

Uma declaração `return` com um valor não deve usar `( )` (parênteses)
em volta do valor. A expressão que retorna o valor precisa iniciar
na mesma linha que a palavra-chave `return` para que se evite uma
inserção de ponto e vírgula.

### Declaração `if`

A classe de declarações `if` deve ter o seguinte formato:

    if (`condition`) {
        `statements`
    }

    if (`condition`) {
        `statements`
    } else {
        `statements`
    }

    if (`condition`) {
        `statements`
    } else if (`condition`) {
        `statements`
    } else {
        `statements`
    }

### Declaração `for`

A classe de declarações `for` deve ter o seguinte formato:

    for (`initialization`; `condition`; `update`) {
        `statements`
    }

    for (`variable` in `object`) {
        if (`filter`) {
            `statements`
        }
    }

O primeiro formato deve ser usado com arrays e loops com um número
predeterminado de repetições.

O segundo formato deve ser utilizado com objetos. Esteja ciente que membros
que forem adicionados ao protótipo do objeto serão incluídos na enumeração.
É aconselhável programar defensivamente usando o método `hasOwnProperty`
para distinguir os membros reais do objeto:

    for (`variable` in `object`) {
        if (`object`.hasOwnProperty(`variable`)) {
            `statements`
        }
    }

### Declaração `while`

Uma declaração `while` deve ter o seguinte formato:

    while (`condition`) {
        `statements`
    }

### Declaração `do`

Uma declaração `do` deve ter o seguinte formato:

    do {
        `statements`
    } while (`condition`);

Ao contrário das outras declarações compostas, a declaração `do` sempre
termina com um `;` (ponto e vírgula).

### Declaração `switch`

Uma declaração `switch` deve ter o seguinte formato:

    switch (`expression`) {
    case `expression`:
        `statements`
    default:
        `statements`
    }

Todo `case` é alinhado com o `switch`. Isso evita excesso de recuo.

Todo grupo de declarações (exceto a `default`) deve terminar com `break`,
`return` ou `throw`. Não faça ele passar por vários blocos.

### Declaração `try`

A classe de declarações `try` deve ter o seguinte formato:

    try {
        `statements`
    } catch (`variable`) {
        `statements`
    }

    try {
        `statements`
    } catch (`variable`) {
        `statements`
    } finally {
        `statements`
    }

### Declaração `continue`

Evite o uso da declaração `continue`. Ela tende a obscurecer o fluxo de
controle da função.

### Declaração `with`

A declaração `with` [não deve ser
usada](http://yuiblog.com/blog/2006/04/11/with-statement-considered-harmful/).

Espaço em branco
----------------

Linhas em branco aumentam a legibilidade separando seções de código que
são relacionadas logicamente.

Espaços em branco devem ser utilizados nas seguintes circunstâncias:

-   Uma palavra-chave seguida de `(` (parêntese da esquerda) devem ser
    separados por um espaço.

            while (true) {

-   Um espaço em branco não deve ser usado entre um valor de função e seu
    `(` (parêntese da esquerda). Isso ajuda na distinção entre palavras-chave
    e invocações de função.
-   Todos os operadores binários exceto `.` (ponto), `(` (parêntese da esquerda)
    e `[` (colchete da esquerda) devem ser separados de seus operandos por um
    espaço.
-   Nenhum espaço deve separar um operador unário e seu operando exceto quando
    o operador é uma palavra como `typeof`.
-   Todo ; (ponto e vírgula) na parte de controle de uma declaração `for` deve
    ser seguida por um espaço.
-   Um espaço em branco deve ser colocado depois de toda , (vírgula).

Sugestões Bônus
---------------

### `{}` e `[]`

Use `{}` em vez de `new Object()`. Use `[]` em vez de `new Array()`.

Use arrays quando os nomes dos membros puderem ser uma sequência de
inteiros. Use objetos quando os nomes dos membros forem strings ou nomes
arbitrários.

### Operador `,` (vírgula)

Evite o uso do operador vírgula exceto para utilização muito disciplinada na parte
de controle das declarações `for`. (Isso não se aplica ao separador vírgula,
que é usado nos objetos literais, arrays literais, declarações `var` e listas
de parâmetros).

### Escopo de Bloco

Nos blocos JavaScript não existe escopo. Apenas funções tem escopo. Não
utilize blocos a menos que seja exigido pelas declarações compostas.

### Expressões de Atribuição

Evite fazer atribuições nas partes condicionais das declarações `if` e
`while`.

        if (a = b) {

é uma declaração correta? Ou

        if (a == b) {

era o pretendido? Evite construções que não podem ser facilmente
entendidas como sendo corretas.

### Operadores `===` e `!==`.

É quase sempre melhor usar os operadores `===` e `!==`. Os operadores
`==` e `!=` fazem conversão de tipo. Em especial, não use `==`
para comparar valores falsos.

### Confundindo Mais e Menos

Tome cuidado para não colocar depois de um `+` outro `+` ou `++`. Esse
padrão pode ser confuso. Coloque parênteses entre eles para deixar
clara a sua intenção.

        total = subtotal + +myInput.value;

é melhor escrito assim

        total = subtotal + (+myInput.value);

pois o `+ +` não será entendido como `++`.

### `eval` is Evil (`eval` é do mal!)

A função `eval` é a funcionalidade mais mal utilizada do JavaScript. Evite-a.

`eval` tem pseudônimos. Não use o construtor `Function`. Não passe
strings para `setTimeout` ou `setInterval`.