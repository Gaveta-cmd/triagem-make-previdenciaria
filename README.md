# Triagem Jurídica Previdenciária - Fluxo Make

Fluxo automatizado no Make (Integromat) para triagem de leads previdenciários com análise por inteligência artificial, retornando resultados estruturados e separando os casos por nível de viabilidade.

---

## Sobre o Projeto

Este fluxo recebe dados de um lead via webhook, processa as informações com IA (OpenRouter + Llama 3.1) e retorna um resultado estruturado indicando se o caso possui **viabilidade Alta** ou **viabilidade Normal**. O objetivo é agilizar a triagem inicial de potenciais clientes na área do Direito Previdenciário, reduzindo o tempo de análise e priorizando os casos com maior potencial.

---

## Tecnologias Utilizadas

| Tecnologia | Função |
|---|---|
| **Make (Integromat)** | Orquestração e automação do fluxo |
| **OpenRouter API** | Gateway para acesso ao modelo de linguagem |
| **Llama 3.1** | Modelo de IA responsável pela análise jurídica |
| **Webhook** | Recepção de leads e envio de respostas |

---

## Como Funciona

```
Lead enviado via Webhook
        ↓
HTTP Request → OpenRouter API (Llama 3.1)
        ↓
     Router
    /       \
Viabilidade  Viabilidade
   Alta        Normal
    ↓            ↓
Webhook      Webhook
Response     Response
```

1. **Recepção**: Um webhook personalizado recebe os dados do lead (nome, CPF, histórico, benefício solicitado etc.)
2. **Análise com IA**: Uma requisição HTTP POST é feita à API do OpenRouter, enviando os dados formatados para o modelo Llama 3.1, que analisa a viabilidade jurídica do caso
3. **Roteamento**: O Router avalia o resultado retornado pela IA e direciona o fluxo para a rota correspondente
4. **Resposta**: O webhook retorna o resultado estruturado para o sistema de origem, indicando a viabilidade e as informações relevantes do caso

---

## Estrutura do Fluxo

O fluxo é composto por **5 módulos** no Make:

### Módulo 1 — Webhooks: Custom Webhook
Ponto de entrada do fluxo. Recebe os dados do lead via requisição HTTP e dispara a automação.

### Módulo 2 — HTTP: POST `/api/v1/chat/completions`
Realiza a chamada à API do OpenRouter com o modelo `meta-llama/llama-3.1-8b-instruct` (ou variante), enviando os dados do lead formatados em um prompt estruturado para análise jurídica previdenciária.

### Módulo 3 — Router
Avalia o conteúdo da resposta da IA e divide o fluxo em dois caminhos:
- **Rota 1**: Viabilidade Alta
- **Rota 2**: Viabilidade Normal

### Módulo 4 — Webhooks: Webhook Response (Viabilidade Alta)
Retorna ao sistema de origem uma resposta estruturada com os dados do caso classificado como **Alta Viabilidade**, incluindo os principais argumentos jurídicos identificados pela IA.

### Módulo 5 — Webhooks: Webhook Response (Viabilidade Normal)
Retorna ao sistema de origem uma resposta estruturada com os dados do caso classificado como **Viabilidade Normal**, com as devidas observações sobre os pontos de atenção identificados.

---

## Exemplo de Payload (Entrada)

```json
{
  "nome": "João da Silva",
  "cpf": "123.456.789-00",
  "data_nascimento": "1965-03-15",
  "beneficio_solicitado": "Aposentadoria por Tempo de Contribuição",
  "tempo_contribuicao_estimado": "32 anos",
  "observacoes": "Trabalhador rural por 10 anos sem registro formal"
}
```

## Exemplo de Resposta (Saída)

```json
{
  "viabilidade": "Alta",
  "score": 8.5,
  "argumentos": [
    "Tempo de contribuição estimado superior ao mínimo exigido",
    "Possibilidade de reconhecimento de trabalho rural via prova testemunhal"
  ],
  "recomendacao": "Prosseguir com análise documental completa"
}
```

---

## Autor

Desenvolvido por **Davi Augusto**
