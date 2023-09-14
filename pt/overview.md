# Observações gerais

✨ 🇧🇷 🇵🇹 🇦🇴 🇲🇿 🇸🇹 🇨🇻 🇹🇱 🇲🇴 ✨

Bem-vindo à página do Português!

Se você está lendo esta página, é bem possível que você esteja considerando fazer contribuições ao módulo português do LanguageTool – o que nos alegra muito!

Esperamos que as informações aqui te ajudem a contribuir da maneira mais eficaz possível. Se estiver faltando algo, ou se algo não estiver claro, não hesite em registrar um *issue*.

## Dialetos

Em primeiro lugar – nós sabemos que o português é uma língua *global* falada em vários continentes, com dialetos diferentes, cada um com histórias, dinâmicas e peculiaridades próprias.

É capaz de você já ter percebido que quem escreveu este artigo é um falante do português do Brasil... Como essa é a variedade com o maior número de falantes, a equipe interna do LanguageTool para português é composta por brasileiros e, via de regra, esse é o dialeto que priorizamos para desenvolvimento interno. Mas **todos** têm a possibilidade de contribuir, não importa o dialeto.

Existem dois níveis de contribuição:
- `pt`: regras que se aplicam (ao menos em teoria!) a **todos** os dialetos do português;
    - por exemplo, não há nenhum padrão do português no qual o plural de *tubarão* seja \**tubarães*..!
- `pt-XX`: aqui, o `XX` se refere a um país (de acordo com o alpha-2 da [ISO 3166](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes));
    - por exemplo, no Brasil se usa _detectar_, enquanto que, em Portugal, prefere-se _detetar_ – `pt-BR` considerará _detetar_ incorreto e `pt-PT` recomendará aos nossos usuários que evitem _detectar_.

Se você observar a estrutura das pastas dentro do diretório `pt`, você vai ver pastas como `pt-BR`, `pt-PT`, `pt-AO`, etc. Essas pastas contêm regras específicas aos dialetos que as nomeiam.

### Dialetos e estilo

O LanguageTool não é apenas uma ferramenta de correção gramatical e ortográfica. Nós também oferecemos regras de **estilo** para que nossos usuários escrevam de maneira que condiga melhor com o tipo de texto que estejam compondo. Não se trata de regras rígidas, e sim de sugestões.

Em dialetos diferentes, essas sugestões podem ser bem diferentes. O que soa formal em Portugal pode ser coloquial em Moçambique, e vice-versa. Procure pensar bem em suas regras de estilo antes de propô-las para que não o LanguageTool não acabe por recomendar estruturas que *piorem* um texto em alguns dialetos enquanto o melhore em outros.
