# Git

O projeto é opensource, mas isso não quer dizer que vale tudo!

## Abra *pull requests*

**Especialmente** se você está contribuindo pela primeira vez, pedimos que você abra um *pull request* para que a comunidade do LanguageTool possa dar uma olhada no seu trabalho.

Novos *pull requests* para o português devem, na medida do possível...
- ... ser independentes e autônomos (*self-contained*) – ou seja, evite *pull requests* com várias mudanças desconexas;
- ... ser descritos e documentados **em inglês**;
- ... conter a tag `[pt]` no título do *pull request*, p. ex.: `[pt] Add grammar rule PREPOSICAO_DEPOIS_DE_ADVERBIO`;
- ... conter a etiqueta `Portuguese`;
- ... conter uma breve explicação do porquê das suas mudanças.

Para que seu *pull request* seja considerado aceitável, ele deverá ter:
- recebido no mínimo uma revisão positiva, ou seja, ter sido aprovado por alguém;
- passado em todos os testes no nosso Circle CI.

Uma vez que for aceito, você pode dar *merge* no seu próprio *pull request*. Se o seu PR contiver mais do que um *commit*, prefira a opção [*merge with squash*](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github#squashing-your-merge-commits) para manter nossa história o mais limpa possível.

## *Branching policy*

Se possível, pedimos que os nomes das suas *branches* sigam o formato `pt/$domain/$change`, onde:
- `$domain` corresponde ao conteúdo da sua mudança *sensu lato* – por exemplo, se estiver fazendo mudanças em regras de gramática, pode escrever aqui `grammar`; se forem regras de estilo, `style`; se estiver alterando [regras de ortografia](/hunspell-support), pode escrever `dict`;
- `$change` corresponde ao conteúdo da sua mudança *sensu stricto*; pode ser o nome da nova regra, uma descrição concisa de sua contribuição, o código do *GitHub issue* ao qual sua mudança se refere, etc.

Pedimos que o `$domain` seja escrito em inglês, mas a designação da sua mudança pode ser em português (se for o caso, busque evitar acentos, til, ou cedilha).

Um bom exemplo de um nome de *branch* seria, por exemplo, `pt/grammar/subjuntivo_sem_conjuncao`.

Os seguintes dados não precisam ser postos no nome da sua *branch*:
- o seu nome;
- a data.

Maus exemplos seriam: `maria_22_de_abril`, `regras`.

## [Mensagens de *commit*](https://xkcd.com/1296/)

Dentro do possível, mantenha seus *commits* bem descritos:
- comece todos as suas mensagens de *commit* com a tag `[pt]`;
- prefira escrevê-las em inglês;
- a primeira linha da sua mensagem não deve ultrapassar 50 caracteres.

O adágio "*commit early, commit often*" só se aplica ao seu trabalho **local**.
