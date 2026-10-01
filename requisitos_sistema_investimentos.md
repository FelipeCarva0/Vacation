# Sistema de Análise de Investimentos — Requisitos e Histórias de Usuário (v7)

## 1. Visão e escopo

Aplicação web para um investidor pessoa física avaliar **ações e FIIs** a partir de indicadores e fórmulas de preço-teto definidos no `Metricas.md`. Os dados entram **manualmente** no início; a integração com API só vem depois que o sistema provar maturidade.

**Fora do MVP:** ETFs, renda fixa, API de dados, multiusuário.

## 2. Persona

**Investidor individual (você).** Segue uma abordagem de valor e dividendos (Graham, Bazin, Buffett). Quer decidir com regras objetivas e semáforos, não por intuição. Digita os dados de relatórios e sites e precisa de rapidez para atualizar e comparar ativos.

## 3. Roadmap

| Fase | Entrega |
| --- | --- |
| 1 (MVP) | Cadastro manual, indicadores com **nota de 1 a 10**, notas por grupo, preços-teto lado a lado com nota |
| 2 | Histórico dos lançamentos, análise de RI, screener e ranking |
| 3 | Carteira, alertas e gráficos |
| 4 | Integração com API de dados, Índice de Sharpe e correlações (dependem de séries históricas) |

## 4. Histórias de usuário

Prioridade: **M** = Must, **S** = Should, **C** = Could.

### Épico A — Cadastro e dados (Fase 1)

- **US01 (M)** Como investidor, quero cadastrar um ativo informando ticker, tipo (ação/FII) e classificação (setor para ações: Energia, Banco, Outros; segmento para FIIs: papel/tijolo), para aplicar as regras corretas a cada um.
  - *Aceite:* ticker único; tipo e classificação obrigatórios.
- **US02 (M)** Como investidor, quero lançar os dados de um ativo (preço atual, LPA, VPA, dividendos por ano, margem líquida, dívida líquida, EBITDA, etc.) com data de referência, para calcular os indicadores.
  - *Aceite:* campos variam conforme o tipo; validação de números; indicador sem dado suficiente aparece como "dados insuficientes" e não como erro.
- **US03 (S)** Como investidor, quero editar e ver o histórico dos lançamentos de um ativo, para acompanhar a evolução e corrigir digitações. *(Fase 2)*
- **US30 (M)** Como investidor, quero entrar no sistema com **e-mail e senha**, para proteger meus dados.
  - *Aceite:* todos os dados ficam vinculados à conta; sem login, nenhuma tela de dados é acessível.

### Épico B — Análise de ações (Fase 1)

- **US04 (M)** Como investidor, quero ver o **P/L** com sua **nota de 1 a 10**, para saber rapidamente se o ativo está confortável.
  - *Aceite:* faixas de referência: 0 a 3 = atenção; 3 a 10 = confortável; 10 a 15 = atenção; acima de 15 ou negativo = alerta máximo (nota 1). A nota dentro das faixas é gradual (ver Épico I).
- **US05 (M)** Como investidor, quero ver **Dívida Líquida / EBITDA** avaliada pelo limite do setor, para identificar endividamento excessivo.
  - *Aceite:* Energia \< 5; outros \< 3; **bancos: métrica não se aplica e fica oculta**.
- **US06 (M)** Como investidor, quero ver a **margem líquida** do ativo, para avaliar a rentabilidade.
- **US07 (M)** Como investidor, quero ver o **P/VP**, para avaliar a oportunidade de risco e a ancoragem de preço.

### Épico C — Análise de FIIs (Fase 1)

- **US08 (M)** Como investidor, quero ver a **alavancagem** (Passivo Total / Ativo Total) com alerta acima de 30%.
- **US09 (M)** Como investidor, quero registrar e ver **vacância e exposição a CRI** (% do patrimônio), para avaliar o risco do fundo.
  - *Aceite:* a vacância gera nota (só FII de tijolo); o % em CRI é apenas informativo, sem nota e sem limite.
- **US10 (S)** Como investidor, quero registrar **taxa de administração, taxa de performance, liquidez, rendimento por cota e saldo acumulado**, para ter a análise completa do fundo em uma tela.

### Épico D — Preço-teto (Fase 1)

- **US11 (M)** Graham: calcular `√(22,5 · VPA · LPA)`, com `VPA = Patrimônio Líquido / nº de ações`.
  - *Aceite:* se LPA ou VPA ≤ 0, mostrar "não aplicável".
- **US12 (M)** Bazin: calcular `média dos dividendos dos últimos 3 anos / 0,06`.
  - *Aceite:* exige 3 anos de dividendos; senão, "dados insuficientes".
- **US13 (M)** Buffett ajustado: calcular `LPA · (8,5 + 2g)` e a versão com juros, `(LPA · (8,5 + 2g) · 4,4) / taxa de juros atual`, com `g` informado por mim e a taxa de juros atual vinda da configuração.
  - *Aceite:* as duas versões são calculadas e exibidas lado a lado, cada uma com sua nota.
- **US14 (M)** FII: calcular `provento esperado por cota (mensal × 12) / DY desejado`, com o DY desejado informado por mim.
- **US15 (M)** Como investidor, quero ver todas as fórmulas **lado a lado, comparadas ao preço atual**, indicando se o preço está abaixo ou acima de cada teto (margem de segurança em %).

### Épico E — Configuração (Fase 1)

- **US16 (S)** Como investidor, quero ajustar parâmetros (limite de Dív. Líq./EBITDA por setor, faixas de P/L, limites de alavancagem e de dívida, taxa de Bazin, taxa Selic/juros atual, DY desejado), para evoluir minhas regras sem alterar o sistema.

### Épico I — Pontuação (Fase 1)

- **US24 (M)** Como investidor, quero que **cada métrica gere uma nota de 1 a 10** (10 = favorável à compra, 1 = desfavorável), para comparar métricas de natureza diferente na mesma escala.
  - *Aceite:* métrica sem dado ou não aplicável (ex.: Dív. Líq./EBITDA de banco) fica "sem nota" e não entra em nenhuma média.
- **US25 (M)** Como investidor, quero que a nota seja **gradual, por interpolação** entre pontos de referência, para que P/L 5 e P/L 9 tenham notas diferentes (ex.: 9 e 7).
  - *Aceite:* cada métrica tem uma lista de pontos (valor → nota); entre dois pontos a nota é interpolada linearmente; fora do intervalo, vale a nota do ponto extremo.
- **US26 (M)** Como investidor, quero ver a **nota por grupo de métricas** (Valuation, Endividamento, Rentabilidade, Risco do FII), para avaliar cada dimensão do ativo. *Não há nota geral do ativo.*
- **US27 (M)** Como investidor, quero que cada preço-teto gere nota pela **margem de segurança** (quanto mais abaixo do teto, maior a nota), para saber o quão atrativo está o preço.
- **US28 (S)** Como investidor, quero configurar os pontos de referência de cada nota, para ajustar a estratégia sem alterar o sistema.
- **US29 (S)** Como investidor, quero ver como cada nota foi calculada (valor, ponto anterior, ponto seguinte, resultado), para confiar no número.

### Épico J — Análise de RI (Fase 2)

- **US31 (S)** Como investidor, quero registrar os resultados trimestrais (RI) de um ativo e comparar o resultado anterior com o seguinte, para ver a **tendência dos resultados**.
  - *Aceite:* resultados lançados por período; o sistema mostra a variação entre trimestres consecutivos.
- **US32 (S)** Como investidor, quero registrar **quedas de preço** (data, preço antes e depois, variação %) e relacioná-las ao RI anterior e ao seguinte, para entender o que as precedeu e o que veio depois.
  - *Aceite:* cada queda aponta para os dois resultados trimestrais mais próximos (antes e depois) e mostra a variação entre eles.

### Épico F — Screener (Fase 2)

- **US17 (S)** Como investidor, quero filtrar e ordenar os ativos cadastrados por notas de métrica e de grupo (ex.: nota de P/L ≥ 7, nota de preço-teto de Bazin ≥ 8), para achar oportunidades.

### Épico G — Carteira e risco (Fase 3)

- **US18 (S)** Registrar minha carteira (ativo, quantidade, preço médio) e ver o resultado e a distribuição.
- **US19 (S)** Receber alertas quando um ativo cruzar um preço-teto ou sair de uma faixa de segurança.
- **US20 (C, Fase 4)** Calcular o **Índice de Sharpe** `(Retorno − Taxa livre de risco) / Desvio padrão`.
- **US21 (C, Fase 4)** Ver a **correlação** entre Selic, FIIs de papel e FIIs de tijolo, e a correlação Δ Risco × Liquidez × Retorno.
- **US22 (C)** Ver gráficos de rendimento por cota, saldo acumulado e preço, com a análise gráfica descrita no arquivo de métricas.

### Épico H — Integração (Fase 4)

- **US23 (C)** Importar dados automaticamente por API, sem alterar as regras de cálculo.

## 5. Regras de negócio consolidadas

| ID | Regra |
| --- | --- |
| RN01 | P/L: 0–3 amarelo; 3–10 verde; 10–15 amarelo; >15 ou negativo vermelho |
| RN02 | Dív. Líq./EBITDA: limite por setor cadastrável; inicialmente Energia \< 5 e Outros \< 3; bancos não se aplica |
| RN03 | Alavancagem de FII = Passivo Total / Ativo Total; limite de 30% |
| RN04 | Graham = √(22,5 · VPA · LPA) |
| RN05 | Bazin = média dos dividendos dos últimos 3 anos / 0,06 |
| RN06 | Buffett = LPA · (8,5 + 2g); versão ajustada: (LPA · (8,5 + 2g) · 4,4) / juros atuais |
| RN07 | Teto FII = provento anual por cota / DY desejado (provento anual = mensal × 12) |
| RN08 | Sharpe = (Retorno − Taxa livre de risco) / Desvio padrão |
| RN09 | Dado ausente nunca gera zero: o indicador mostra "dados insuficientes" |
| RN10 | Toda métrica gera nota de 1 a 10; 10 = favorável à compra, 1 = desfavorável |
| RN11 | Nota por interpolação linear entre pontos de referência (valor → nota); fora do intervalo, trava no ponto extremo |
| RN12 | Margem de segurança = (Teto − Preço atual) / Teto; a nota de preço-teto cresce com a margem de segurança. Curva: margem ≥ 30% = 10; 0% = 5; ≤ −30% = 1; linear entre os pontos |
| RN13 | Métrica sem dado ou não aplicável não recebe nota e é excluída das médias de grupo |
| RN14 | Existe nota por métrica e por grupo; **não existe nota geral do ativo** |
| RN15 | Grupos: **Valuation** (P/L, P/VP, preços-teto), **Endividamento** (Dív. Líq./EBITDA, alavancagem), **Rentabilidade** (margem líquida, rendimento por cota), **Risco do FII** (vacância, CRI, liquidez, taxas) |
| RN16 | Nota do grupo = média simples das notas das métricas do grupo que têm nota |
| RN17 | Cor derivada da nota: 1 a 3 vermelho; 4 a 6 amarelo; 7 a 10 verde |

## 6. Requisitos funcionais (resumo)

- **RF01** Cadastro e edição de ativos (ação/FII) com classificação.
- **RF02** Lançamento manual de dados com data de referência.
- **RF03** Cálculo automático de indicadores e semáforos.
- **RF04** Cálculo dos 4 preços-teto e comparação lado a lado com o preço atual.
- **RF05** Parâmetros configuráveis (RN01 a RN07).
- **RF07** Cálculo de nota (1 a 10) por métrica e por grupo, com pontos de referência configuráveis.
- **RF06** Histórico por ativo (Fase 2), screener (Fase 2), carteira e alertas (Fase 3).

## 7. Requisitos não funcionais

- **RNF01** Camada de entrada de dados separada da camada de cálculo, para trocar manual por API sem reescrever regras.
- **RNF02** Fórmulas testadas com casos de teste conhecidos (resultados verificados à mão).
- **RNF03** Persistência dos dados entre sessões.
- **RNF04** Interface responsiva (uso no desktop e no celular).
- **RNF05** Mostrar qual fórmula e quais valores geraram cada resultado (transparência do cálculo).
- **RNF06** Formato numérico brasileiro (vírgula decimal, R$).
- **RNF07** Funções de pontuação isoladas e testáveis (mesma entrada, mesma nota), com pontos de referência armazenados como dados, não como código.
- **RNF08** Autenticação por e-mail e senha, com dados vinculados à conta (modelo preparado para mais de um usuário no futuro).

## 8. Decisões tomadas na entrevista

- Fonte de dados manual no início; API só após maturidade do sistema.
- MVP com ações e FIIs, em aplicação web.
- Bancos não têm a métrica Dív. Líq./EBITDA.
- P/L negativo ou acima de 15 = alerta máximo.
- Preços-teto lado a lado com o preço atual; Buffett exibe as duas versões.
- Notas de 1 a 10 por interpolação; sem nota geral; nota de preço-teto pela margem de segurança.
- Sharpe e correlações ficam para a Fase 4 (API).
- Quatro grupos de métricas (RN15), com nota do grupo por média simples (RN16).
- Curva de margem de segurança: 30% = 10, 0% = 5, −30% = 1 (RN12).
- Semáforo derivado da nota: 1–3 vermelho, 4–6 amarelo, 7–10 verde (RN17).
- CRI é informativo (% do patrimônio), sem nota nem limite.
- Limite de Dív. Líq./EBITDA por setor cadastrável, começando com Energia e Outros.
- Acesso com login simples (e-mail e senha), mesmo sendo uso individual.
- Análise de RI cobre tendência dos resultados trimestrais e relação com quedas de preço (Épico J, Fase 2).
- Escalas de P/L, alavancagem, vacância e P/VP definidas (seção 9); no P/L, os valores exatos 3 e 10 não exigem tratamento especial, pois a interpolação suaviza a transição.

## 9. Pontos de referência das notas (valor → nota)

Entre dois pontos a nota é interpolada linearmente; fora do intervalo, vale a nota do ponto extremo.

| Métrica | Aplica-se a | Pontos (valor → nota) |
| --- | --- | --- |
| Vacância | FII de tijolo | ≤ 5% → 10; ≥ 20% → 1 |
| P/VP | Ações e FIIs | ≤ 0,8 → 10; 1,0 → 5; ≥ 1,5 → 1 |
| Margem de segurança | Preços-teto | ≥ 30% → 10; 0% → 5; ≤ −30% → 1 |
| P/L | Ações | negativo → 1; 3 → 4; 4 → 7; 6 a 8 → 10; 10 → 7; 12 → 4; ≥ 15 → 1 (entre 0 e 3, interpola de 1 a 4; hipótese a confirmar) |
| Alavancagem (FII) | FIIs | ≤ 10% → 10; 30% (limite) → 5; ≥ 50% → 1 |
| Dív. Líq./EBITDA | Ações (exceto bancos) | a definir (limites: Energia \< 5; Outros \< 3) |
| CRI (% do patrimônio) | FIIs | informativo, sem nota |
| Margem líquida | Ações | a definir |
| Demais métricas | — | a definir |

## 10. Questões em aberto

1. **P/L entre 0 e 3:** confirma a interpolação de nota 1 (em 0) a 4 (em 3)?
2. **RI:** quais campos comparar entre trimestres (receita, lucro líquido, LPA, margem)? A tendência também gera nota?
3. **Pontos de referência restantes:** Dív. Líq./EBITDA (Energia e Outros), margem líquida, rendimento por cota, liquidez e taxas (administração e performance).