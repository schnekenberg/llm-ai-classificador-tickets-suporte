# Classificador automático de tickets de suporte

projeto 1 da atividade de IA LLM (Ciência da Computação · PUC-SP).

recebe e-mails de clientes em qualquer idioma e usa uma LLM para extrair, de forma estruturada (Pydantic), **categoria**, **gravidade**, **sentimento**, **idioma** e um **resumo em português**. com isso, cada ticket é encaminhado ao setor responsável e a fila é ordenada por prioridade.

## integrantes

- Guilherme Schnekenberg Teixeira (RA00336189)
- Luís Augusto Coelho de Souza (RA00331675)

## stack

- Python (Google Colab)
- biblioteca `openai`, apontada para a **Gemini API (free tier)** pelo endpoint compatível com OpenAI
- modelo: `gemini-3.5-flash-lite`
- pydantic, pandas, seaborn, matplotlib

## como rodar

1. gere uma chave em **aistudio.google.com → Get API key** (grátis, sem cartão)
2. abra o notebook `ativ_AI_LLM.ipynb` no Google Colab
3. no painel **Secrets**, crie `GEMINI_API_KEY` com a chave e ative o **Notebook access**
4. **Ambiente de execução → Executar tudo**

a chave fica só nos Secrets do Colab e não aparece no notebook.

## estrutura do notebook

| etapa | o que faz |
|---|---|
| setup | instala as bibliotecas e cria o cliente da API |
| teste de conexão | lista os modelos disponíveis para a chave |
| dados | 15 e-mails fictícios em 5 idiomas (pt, en, es, fr, de) sobre bugs, faturamento e login |
| chamada do modelo | uma chamada com saída estruturada (`chat.completions.parse` + esquema `TicketBatch`) |
| roteamento | mapeia categoria → setor (Engenharia, Financeiro, Segurança / Contas) e ordena por gravidade |
| análise | gráfico de tickets por categoria e gravidade, e tabela de sentimento por setor |
| avaliação | compara as respostas da LLM com rótulos feitos à mão: acerto, matrizes de confusão e discordâncias |
| conclusão | discussão dos resultados e limitações |

## resultados

classificação dos 15 e-mails em **uma única chamada** (~1.100 tokens):

| campo | acerto |
|---|---|
| categoria | 100% (15/15) |
| idioma | 100% (15/15) |
| sentimento | 87% (13/15) |
| gravidade | 80% (12/15) |

- **categoria:** acertou todas, inclusive o caso ambíguo "sistema de pagamentos fora do ar", que é sobre pagamentos mas foi classificado corretamente como bug crítico
- **gravidade:** nos 3 casos em que discordou, o modelo deu uma gravidade **um nível abaixo** da nossa. nunca errou por mais de um nível
- **sentimento:** nos 2 casos em que discordou, o modelo marcou como negativo e-mails que, pelo tom, nós consideramos neutros

## limitações

- **free tier do Gemini:** o Google pode usar o que é enviado para melhorar os produtos dele (por isso os e-mails são fictícios), e há limite de requisições por minuto e por dia
- **amostra pequena:** são só 15 e-mails, então cada erro muda o acerto em ~7 pontos percentuais
- **rótulos subjetivos:** gravidade e sentimento foram rotulados por nós, sem um segundo avaliador
- **uma única execução:** a saída de uma LLM pode variar entre execuções

## referências

- Gemini API: OpenAI compatibility, https://ai.google.dev/gemini-api/docs/openai
- notebook da aula `PUCSP_CS_AI_16_AI_LLM.ipynb` (seção 7, structured outputs → pandas)
