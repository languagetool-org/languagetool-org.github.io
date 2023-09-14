# Regras

Nossa ideia é que contribuintes *opensource* tenham suficiente flexibilidade para sugerir novas regras sem grandes obstáculos de natureza técnica, mas sem pôr em risco a qualidade e consistência das nossas regras. Tendo isso em vista, desenvolvemos as diretrizes nesta página.

## *Rule IDs*
Os IDs das nossas regras *atuais* não são lá muito coesos. Temos regras com IDs em inglês, outras em português, e ainda outras com as duas línguas misturadas. No momento da redação destas diretrizes, não temos a menor intenção de renomeá-las todas para que combinem com o novo esquema. As regras *legacy* ficam como estão no presente momento, mas isso não quer dizer que não possamos revisar como as nomeamos **a partir de agora**.

### Esquema

O ID de uma regra deve...

- ser escrito **em português**;
    - sabemos que é uma prática um tanto peculiar, mas isso facilita a identificação dessas regras nos *logs* internos;
    - se estiver propondo uma regra válida para todos os dialetos do português, na medida do possível evite palavras com grafias que variam de acordo com dialeto – se não for possível, pedimos que use a grafia usada **no Brasil**;
- ser escrito **em maiúsculas** – ou seja, `NOVA_REGRA`;
    - cada elemento deve ser separado por um *underscore*, ou seja, `_`;
- ser informativo – evite IDs arbitrários como `REGRA_PLURAL_2`, `PRONOME_3A`.

É possível dar um ID à regra que corresponda a um exemplo particularmente emblemático da correção executada pela regra. Por exemplo, `DUZENTAS_GRAMAS` é uma regra que corrige o erro de concordância nominal em \**duzent**as** gramas* para *duzent**os** gramas*, mas também se aplica a outros numerais relativos a centenas, como *trezentos*, *quatrocentos*, etc. Tal prática é preferível a dar um ID tecnicamente descritivo mas demasiado longo, como `CONCORDANCIA_NOMINAL_FEMININO_ENTRE_CENTENAS_E_GRAMA_QUE_DEVERIA_SER_MASCULINO` 😱

Sabemos que pode ser um desafio em alguns casos, mas tente manter o ID da regra o mais sucinto possível. Algumas abreviaturas são possíveis, p. ex. `ADJ` em vez de `ADJETIVO`, mas use o bom-senso. <sup>(`TODO`: find PT glossing standards)</sup>

Os IDs das regras, tecnicamente, aceitam qualquer caractere Unicode. Mesmo assim, evite usar letras fora do ASCII, a não ser que a sua regra especificamente esteja corrigindo um erro de acentuação. Por exemplo, `CONFUSAO_INFLUENCIA_INFLUÊNCIA` indica claramente que o problema é entre _influencia_ e _influência_. Note, no entanto, que a palavra `CONFUSAO` não precisa de til.

No fim das contas, lembre-se de que os IDs das regras são arbitrários. Se não for inteiramente possível seguir as sugestões acima, busque redigir uma pequena explicação em seu *pull request* para os revisores.
