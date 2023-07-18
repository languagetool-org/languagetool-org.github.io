# Mensagens

Ao escrever regras, nós também precisamos redigir as **mensagens** que as acompanham. Por exemplo:

```xml
<rule id="ERRO_DE_CRASE" name="Erro de crase">
    <!-- lógica da regra, etc etc -->
    <message>Possível erro de crase detectado. Considere substituir por <suggestion>à</suggestion>.</message>
</rule>
```

## Princípios

O objetivo do LanguageTool não é apenas consertar erros. Uma das ideias por trás do LT é **ajudar** ao usuário a escrever melhor.

Com isto em vista, as mensagens do LanguageTool devem ser:

* **informativas**: sempre que possível, forneça ao usuário uma explicação acerca de qual regra gramatical foi violada;
* **amigáveis**: evite simplesmente dizer 'está errado' – dê ao usuário o benefício da dúvida;
* **contextualizadas**: algumas regras só se aplicam em determinados contextos (por exemplo, as regras de estilo) – quando for o caso, informe ao usuário;
* **neutras quanto ao dialeto** (na medida do possível): as regras do português **geral** (ou seja, os arquivos encontrados diretamente sob `pt`) não devem soar estranhas a nenhum lusófono, não importa de onde vem; se não for possível, é capaz que você esteja tentando criar uma regra para uma variedade específica do português (e.g. `pt-BR` ou `pt-PT`).
* obedecer ao **Acordo Ortográfico de 1990**.

## Entidades XML

Para conferir ainda mais consistência às nossas mensagens, alguns fragmentos que se repetem frequentemente foram extraídos para arquivos `.ent`. Esses arquivos se encontram no diretório `resource/entities`. No caso das mensagens, o arquivo utilizado é `messages.ent`. Em vez de repetir a mesma sequência de palavras, prefira usar uma entidade XML.

Ao invés de:

```xml
<rule id="ERRO_DE_ESTILO_1" name="Erro de estilo 1">
    <!-- lógica da regra, etc etc -->
    <message>Em contextos profissionais, evite usar o verbo &quot;verbar&quot;.</message>
</rule>


<rule id="ERRO_DE_ESTILO_2" name="Erro de estilo 2">
    <!-- lógica da regra, etc etc -->
    <message>Em contextos profissionais, prefira o adjetivo &quot;lindo&quot;.</message>
</rule>
```

Prefira:

No arquivo `resource/pt/entities/messages.ent`:

```xml
<!ENTITY professional_intro "Em contextos profissionais">
```

```xml
<rule id="ERRO_DE_ESTILO_1" name="Erro de estilo 1">
    <!-- lógica da regra, etc etc -->
    <message>&professional_intro;, evite usar o verbo &quot;verbar&quot;.</message>
</rule>


<rule id="ERRO_DE_ESTILO_2" name="Erro de estilo 2">
    <!-- lógica da regra, etc etc -->
    <message>&professional_intro;, prefira o adjetivo &quot;lindo&quot;.</message>
</rule>
```

## Aspas

Para evitar problemas com diferenças entre o uso de aspas em padrões diferentes, prefira usar `&quot;` no lugar de aspas literais (p. ex. `"`).

## Nomenclatura

Algumas decisões são arbitrárias. Nossa intenção é registrá-las nesta página para que ninguém fique surpreso quando alguém lhe pedir que troque uma palavra por um sinônimo.

|👎 NÃO USE 👎|👍 USE 👍|
|---|---|
|~~nome~~|substantivo|
|~~conjuntivo~~|subjuntivo|

