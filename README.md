# Piccole Stiliste — app de membros (Itália, em italiano)

Entrega do produto **Piccole Stiliste · Collezione 2027**: 49 look para bonecas fashion de 29 cm, em 102 páginas
(capa, sommario e, para cada look, a página editoriale e o cartamodello), com os 4 bônus. Mesma interface do app das Carteiras (clubedanina.vercel.app), vinda pelo app das Guias de Contas.
Marca e cores do livro: azul-marinho #10265A, rosa #FF2F83, azul-petróleo #00B7C7 e creme #FFF8EC.

    node servidor.cjs     # http://localhost:4260

## Acesso: qualquer e-mail entra

Pedido do Heitor em 01/11/2026: ainda não há checkout nem webhook para a Itália, então o login aceita
**qualquer coisa** (até o campo vazio) e libera o livro inteiro. A chave é `ACEITA_TODOS = true` no começo do
`<script>` do `index.html` (vem do montador). A consulta à Edge Function `membros-acesso` continua, mas só para
puxar o nome de quem comprou; se ela falhar ou demorar 3 s, a pessoa entra igual.

Para voltar a exigir compra: `ACEITA_TODOS = false` no `scripts/ps-app-montar.cjs` e o webhook do gateway gravando
`{ email, item: "piccole", status: "paid" }` em `membros_compras`.

**Suporte:** o formulário fica escondido (só a FAQ aparece), porque a Edge Function `membros-suporte` recusa
e-mail sem compra (`{"erro":"sem compra"}`). A tela manda responder ao e-mail de confirmação da compra.

## Como é montado

O `index.html` **não se edita à mão**: ele é gerado.

    node scripts/ps-app-arquivos.cjs          # páginas webp, PDF, capas, fotos do login, ícones
    node scripts/ps-app-arquivos.cjs capas    # só capas, login e ícones
    node scripts/ps-app-montar.cjs            # referencia/guias.html -> index.html

(os `scripts/` ficam no repo de trabalho "Visual St - Claude")

- `referencia/guias.html`: o `index.html` do app das Guias de Contas congelado em 01/11/2026. O montador faz
  trocas com conferência: se a referência mudar e algum trecho sumir, ele para com "não achei".
- `scripts/ps-app-italiano.cjs`: a interface em italiano, em pares [português, italiano]. O montador avisa os
  pares que não acharam nada ("dicionário sem uso") e lista o que sobrou com cara de português ("sobras").
- Índice "Look": os 49 do Sommario do livro, agrupados por estação (Primavera/Estate e Autunno/Inverno),
  com o Rif. de cada um. A página é a editoriale; o cartamodello vem logo depois.

## Conteúdo

| no app | origem |
|---|---|
| Collezione 2027 (102 págs.) | `entregaveis/piccole-stiliste/it/gpt` (última versão de cada página, a correção `NNNx` quando existe) |
| PDF (`pdf/Piccole-Stiliste-Collezione-2027.pdf`) | `~/Downloads/Roupas Bonecas/Entregável IT/` |
| Bônus 1 · Mappa della Moda (6 págs.), 2 · Quaderno di disegno dei look (6 no leitor, 9 no PDF), 3 · Attestato (regras + certificado), 4 · Scatola da cucito (links da Amazon.it no PDF) | `it/gpt/b-*`, traduzidos em 01/11 com o prompt curto `prompts/bonus2.txt` (o longo era recusado). Lista em `scripts/ps-app-bonus.cjs` |

As 5 correções do livro (002x, 003x, 084x, 094x, 100x) entraram em 01/11. Página nova ou corrigida: rodar `ps-app-arquivos.cjs` e `ps-app-montar.cjs`.

O bônus "Dal Gioco a un Piccolo Marchio" da página de vendas não existe no material base: no app entrou a Scatola da cucito no lugar.

## Publicar (ainda não)

Repo próprio na Vercel (sugestão: projeto `piccolestiliste` → piccolestiliste.vercel.app, que é o endereço
escrito na aba App). Páginas ~14 MB + PDF ~25 MB ficam no repo. `.vercelignore` tira referência, servidor e README do ar.
