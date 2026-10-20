# Análise de transações da ClearBank

Projeto do desafio final da disciplina de Análise de Dados e Inteligência de Negócios com IA, da pós da Rocketseat.

A ideia é simular o trabalho de uma analista de dados júnior numa fintech fictícia, a ClearBank. Todo mês a equipe de operações exporta um CSV com as transações dos clientes, e esse arquivo costuma chegar bagunçado: campo vazio, valor que não é número, data em formato errado, linha duplicada. O notebook lê esse arquivo, descarta o que não presta, calcula as métricas de cada mês, aponta as transações suspeitas e salva tudo num JSON.

## O que tem aqui

- `desafio_final.ipynb`: a solução principal, feita só com Python puro (`csv`, `datetime` e `json`).
- `analise_pandas.ipynb` e `analise_pandas.py`: a versão alternativa com pandas, usada para conferir se os números batem com a solução principal. O `.py` foi exportado direto do notebook.
- `transacoes.csv`: o arquivo de entrada que criei para testar. Tem 202 linhas, com transações de janeiro a junho de 2026, várias linhas com problema de propósito e algumas transações acima de R$ 10.000.
- `relatorio.json`: o resultado gerado pelo notebook principal.
- `grafico.png`: gráfico de barras com o saldo de cada mês.

## Como rodar

O jeito mais fácil é pelo Google Colab, que já vem com pandas e matplotlib instalados.

1. Abra o `desafio_final.ipynb` no Colab.
2. Faça o upload do `transacoes.csv` no painel de arquivos (ícone de pasta, na lateral).
3. Vá em **Ambiente de execução → Executar tudo**.

Isso gera o `relatorio.json`. Depois, se quiser ver a parte com pandas:

4. Abra o `analise_pandas.ipynb`.
5. Faça o upload do `transacoes.csv` e do `relatorio.json` que acabou de ser gerado.
6. Execute tudo. Ele compara os resultados com a solução principal e gera o `grafico.png`.

Para rodar localmente, é preciso Python 3.10 ou mais recente e as bibliotecas da parte opcional:

```
pip install pandas matplotlib
```

O `.py` roda com `python analise_pandas.py`, desde que o CSV e o JSON estejam na mesma pasta.

## Como funciona

**Leitura e limpeza.** O CSV é lido com `csv.DictReader`. Cada linha passa pela função `validar_transacao()`, que descarta a linha se o `id` estiver vazio ou não for número, se o `cliente_id` estiver vazio, se a data não estiver no formato `AAAA-MM-DD`, se o tipo não for `credito` ou `debito`, ou se o valor não for um número maior que zero. Linhas com `id` repetido também saem. Nada disso para o programa: a linha ruim é ignorada e a leitura continua. No fim aparece um resumo com o total de linhas lidas, válidas e inválidas.

**Métricas por mês.** As transações válidas são agrupadas por mês e, para cada um, o notebook calcula a quantidade de transações, o total de crédito e de débito, o saldo, a média por transação e os maiores e menores valores.

**Transações suspeitas.** Qualquer transação acima de R$ 10.000,00 é marcada como suspeita e aparece listada no relatório.

**Saída.** O relatório é exibido no terminal com os valores em reais, e o mesmo conteúdo é salvo em `relatorio.json`.

O tratamento de erros com `try/except` aparece em três pontos: na abertura do arquivo (caso ele não exista), na conversão do valor para `float` e na conversão da data com `strptime`.

## Resultado com o arquivo de teste

Das 202 linhas, 148 são válidas e 54 foram descartadas. O período vai de 01/01/2026 a 28/06/2026, e seis transações passaram do limite de R$ 10.000. A versão com pandas chegou exatamente aos mesmos números em todas as métricas de todos os meses.
