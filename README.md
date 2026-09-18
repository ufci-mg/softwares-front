# nit-ferramentas

Repositório **público**, sem credenciais — hospeda o gerador do PDF do SIPAC
usado no processo de submissão de software do NIT/CEFET-MG, publicado via
GitHub Pages.

## O que tem aqui

| Ferramenta | Arquivo | Para quê |
|---|---|---|
| Gerador do PDF do SIPAC | [`index.html`](index.html) | O docente preenche classificação e dados completos dos autores e recebe o PDF pronto para anexar ao processo no SIPAC |

Substituiu o script Python que antes vivia no repositório template e precisava
ser instalado na máquina do docente. Navegador todo mundo já tem.

## Público, processando dado sensível — como isso se sustenta

O formulário coleta **CPF, RG, endereço e telefone** dos autores. Isso não é
contradição com o repositório ser público, mas exige que uma propriedade seja
verdadeira e continue verdadeira: **nada do que é digitado sai do navegador.**

Não há backend. Não há chamada de rede. A biblioteca de geração de PDF está
*vendorizada* — colada dentro do próprio `index.html`, entre os marcadores
`BEGIN VENDOR` / `END VENDOR` — em vez de carregada de um CDN, justamente para
que nenhum terceiro possa trocar o código debaixo de nós. O PDF é montado
localmente e baixado direto.

Ser público é o que permite usar Pages em qualquer plano do GitHub (Pages a
partir de repositório privado exige plano pago) e não expõe nada de ninguém: o
que está publicado é o formulário vazio, não dado de submissão.

### Três travas, verificadas a cada push

| Trava | Onde |
|---|---|
| `Content-Security-Policy` com `connect-src 'none'` | meta tag no topo do `index.html` |
| Nenhuma referência externa fora do bloco vendorizado | [`sem-rede.yml`](.github/workflows/sem-rede.yml) |
| Bloco vendorizado íntegro | hash fixado em [`vendor-pdf-lib.sha256`](.github/vendor-pdf-lib.sha256) |

**Nunca acrescente a este arquivo:** `<script src>` de CDN, link de webfont,
analytics, ou qualquer `fetch`. O CI rejeita, e a rejeição é o ponto.

O hash fixado existe porque ninguém revisa 500 KB de código minificado num
diff — uma linha maliciosa enfiada ali passaria batida. Com o hash, atualizar a
biblioteca exige trocar código **e** hash no mesmo commit, o que torna a
intenção visível na revisão.

## Publicar no GitHub Pages

`Settings → Pages → Source: Deploy from a branch → main → / (root)`.

Sem build. Editou `index.html`, é só dar push.

## Atualizar a biblioteca de PDF

1. Baixe a versão nova do `pdf-lib`.
2. **Remova a linha `sourceMappingURL` do fim** — senão o navegador tenta
   buscar o mapa de origem, que é exatamente a chamada de rede que não pode
   existir.
3. Cole entre os marcadores `BEGIN VENDOR` / `END VENDOR`.
4. Recalcule o hash de referência, no mesmo commit:

```bash
sed -n '/BEGIN VENDOR/,/END VENDOR/p' index.html \
  | sha256sum | awk '{print $1"  index.html.vendor-block"}' > .github/vendor-pdf-lib.sha256
```

## Testar

A suíte do repositório `nit-softwares` abre esta página num Chromium de
verdade, preenche o formulário inteiro, gera o PDF e confere o anexo embutido,
o bloco sentinela e a ausência de qualquer requisição de rede:

```bash
testes/rodar.sh
```

## Manutenção

O formulário é a fonte dos dados que vão ao SIPAC e ao INPI, e não é gerado a
partir de schema nenhum — se as exigências do processo mudarem (campo novo,
validação diferente), é edição à mão aqui. O `metadata.json` do repositório de
submissão ficou propositalmente mínimo e **não** espelha este formulário; os
dois respondem perguntas diferentes.
