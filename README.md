# Painel de Pendências – Oficinas Externas

Painel web que mostra **somente os veículos não liberados** das oficinas externas.
Ele lê direto a planilha `dados/Sistema_Acompanhamento_Oficinas_Externas.xlsx`
(aba `Historico_Atualizacoes`). Não tem banco de dados nem servidor: é só subir
a planilha nova que o painel se atualiza.

## Estrutura

```
index.html                  painel (página única)
vendor/xlsx.full.min.js     leitor de Excel (SheetJS), reserva caso o CDN não carregue
dados/Sistema_Acompanhamento_Oficinas_Externas.xlsx   planilha de controle
.nojekyll                   evita processamento do GitHub Pages
```

## Como funciona

- O status de cada veículo é o do **último registro da placa** no histórico, na ordem das linhas, como na planilha.
- Veículo com último status **Liberado** sai do painel. Previsão não conta como liberação.
- Os alertas são calculados no momento em que o painel é aberto:
  - **Previsão vencida**: a previsão já passou e não há registro de liberação. Precisa confirmar com a oficina.
  - **Previsão para hoje**
  - **Sem previsão**
  - **N dias sem atualização**: o último registro tem 2 dias ou mais.
- Ordem dos cartões: vencida → hoje → previsões futuras → sem previsão.
- Clicar nos indicadores ou nas barras filtra a lista. Há também busca por placa, oficina ou serviço e um botão para ver em tabela.

## Peças e agendamentos

- Aba **Pecas_Compras**: uma linha por atualização de cada peça. O painel mostra o último status de cada combinação peça + destino até ela ficar *Recebida*, *Instalada* ou *Cancelada*, e também lista as peças dentro do cartão do veículo de destino.
- Aba **Agendamentos**: entradas em oficina já marcadas. O agendamento sai do painel quando fica *Entrada confirmada* (aí o veículo é lançado no histórico) ou *Cancelado*. Se a hora da entrada passar sem confirmação, aparece o aviso "Entrada não confirmada".

## Publicar no GitHub Pages (uma vez)

1. Crie um repositório no GitHub (ex.: `painel-oficinas`).
2. Envie todo o conteúdo desta pasta para a branch `main` (**Add file → Upload files** no site já resolve).
3. Em **Settings → Pages**, escolha *Deploy from a branch* → `main` / `(root)` → **Save**.
4. Em 1 a 2 minutos o painel fica em `https://<seu-usuario>.github.io/painel-oficinas/`.

> **Atenção à privacidade:** no plano gratuito, o GitHub Pages só funciona com repositório **público**.
> Nesse caso, qualquer pessoa com o link vê placas, oficinas e valores de orçamento.
> Para manter os dados privados, é preciso um plano pago (Pro/Team/Enterprise).

## Atualizar os dados

1. Atualize a planilha (registrando uma nova linha no histórico para cada contato).
2. No GitHub, substitua `dados/Sistema_Acompanhamento_Oficinas_Externas.xlsx` pela nova versão, mantendo **o mesmo nome**.
3. Abra o painel e clique em **Atualizar**. O GitHub Pages pode levar cerca de 1 minuto para publicar.

Pelo terminal:

```bash
cp ~/Downloads/Sistema_Acompanhamento_Oficinas_Externas.xlsx dados/
git add dados/ && git commit -m "Atualização oficinas $(date +%d/%m\ %Hh%M)" && git push
```

## Usar sem internet / sem GitHub

Abra o `index.html` no navegador e use **Abrir planilha…** ou arraste o arquivo `.xlsx` para a página.
Aberto como arquivo local, o navegador não lê a pasta `dados/` sozinho.
