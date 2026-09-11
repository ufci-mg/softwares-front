# nit-ferramentas

Repositório **público**, sem credenciais e sem dados sensíveis — hospeda
ferramentas estáticas de apoio ao processo de submissão de software do
NIT/CEFET-MG, publicadas via GitHub Pages.

É público de propósito: nenhuma ferramenta aqui depende de o visitante ser
membro da organização, e nenhuma delas envia dado a lugar nenhum — tudo roda
no navegador de quem usa. Isso também é o que permite hospedar Pages em
qualquer plano do GitHub (Pages a partir de repositório **privado** exige
plano Pro/Team; a partir de repositório público funciona sempre).

## O que tem aqui

| Ferramenta | Arquivo | Para quê |
|---|---|---|
| Gerador de `metadata.json` | [`index.html`](index.html) | Monta o `metadata.json` da submissão por formulário, em vez de editar o JSON à mão — evita o erro mais comum (array de autores mal formatado) e as listas de linguagem/área de aplicação viram checkbox em vez de texto livre inconsistente |

## Por que este repositório existe separado dos outros três

`nit-submissoes` e `template-submissao-software` são privados — cada
submissão e cada repositório de software fica visível só a quem precisa. As
ferramentas aqui não têm esse requisito (não expõem nada de ninguém), e
colocar Pages num repo privado teria o problema do plano descrito acima. Em
vez de depender de o plano da organização virar Team, isolamos o que é
genuinamente público num repo à parte.

Fica também como semente do "site do NIT" citado nos documentos de fluxo —
pode crescer com mais ferramentas (ex.: o link de prefill da issue de
submissão) sem misturar escopo com os repositórios operacionais.

## Publicar no GitHub Pages

`Settings → Pages → Source: Deploy from a branch → main → / (root)`.

Nenhuma Action, nenhum build. Editou `index.html`, é só dar push.

## Manutenção

O formulário do gerador reflete os campos de
[`metadata.schema.json`](../template-submissao-software/metadata.schema.json)
do `template-submissao-software`. Os dois não são sincronizados
automaticamente — se o schema mudar (novo campo obrigatório, novo valor de
enum), atualize `index.html` à mão. É um acoplamento aceito conscientemente
para não criar uma integração cross-repo só para isso.
