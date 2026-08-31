# Full prompt templates

This file is the Online Resource for the prompt-construction method described in the manuscript. The three user-prompt templates below are reproduced verbatim from the generation notebooks. A cross-check of all five model notebooks confirmed that the zero-shot, one-shot, and few-shot strings are identical across models.

At runtime, `{test_case}` was replaced with the cleaned source test-case description. The same task definition and demonstrations were used for every model.

## Common system message

The user template was paired with this system message:

````text
Você converte títulos de casos de teste em cenários Gherkin. Sua resposta deve conter somente um Scenario em Gherkin, sem JSON, Feature, tags, Markdown, comentários ou explicações.
````

Each notebook passed the system and user messages through the model tokenizer's chat template when one was available. Qwen3 was run with explicit thinking disabled (`enable_thinking=False`) to avoid model-specific reasoning blocks in the output. The Granite notebook supplied the same two-message structure directly to its pipeline.

## Zero-shot user template

````text
Converta a seguinte descrição de caso de teste em um único cenário BDD, usando a sintaxe estrita de Gherkin. Certifique-se de que a saída contenha apenas a sintaxe de Gherkin para o cenário, sem comentários, explicações ou a palavra "Feature". Use o português para as descrições dos casos de teste e detalhes do cenário, mas mantenha as palavras-chave do Gherkin em inglês.

Agora, converta a seguinte descrição de caso de teste em exatamente um cenário BDD usando a sintaxe estrita de Gherkin. A saída deve seguir exatamente o formato do exemplo fornecido e não conter nada além do cenário BDD. Somente as palavras-chave do Gherkin devem estar em inglês; todo o outro texto deve estar em português. Se o caso de teste fornecido estiver em inglês, traduza-o para o português na geração do cenário BDD, mantendo as palavras-chave (Given, When, Then, And) em inglês.

Descrição do Caso de Teste:
{test_case}

Para um bom cenário BDD, certifique-se de declarar claramente o valor de negócio ou resultado esperado e mantenha o foco em uma única ação e seu resultado. Use apenas os passos essenciais (Given, When, Then, And) de forma clara e declarativa, evitando detalhes de implementação e repetições desnecessárias. Garanta que os cenários sejam independentes, utilizem uma terminologia de negócios consistente sem jargões técnicos e que os passos sejam escritos em terceira pessoa para evitar múltiplas interpretações.

Certifique-se de que os cenários BDD tenham indentação consistente de dois espaços para cada passo sob 'Scenario', sem linhas em branco entre os passos, e uma linha em branco separando diferentes cenários.

Siga esta estrutura para a saída:

Scenario: [Descrição do Cenário]
  Given [algum contexto inicial]
    And [mais algum contexto, se houver]
  When [uma ação é realizada]
  Then [um conjunto específico de resultados deve ocorrer]
    And [outro resultado, se houver]

A resposta deve conter estritamente apenas a sintaxe válida de Gherkin e evitar qualquer informação ou comentário extra. A resposta deve conter apenas o texto BDD, sem formatação ou texto adicional (por exemplo, sem 'gherkin```'), e deve ser apenas um cenário BDD.
````

## One-shot user template

````text
Converta a seguinte descrição de caso de teste em um único cenário BDD, usando a sintaxe estrita de Gherkin. Certifique-se de que a saída contenha apenas a sintaxe de Gherkin para o cenário, sem comentários, explicações ou a palavra "Feature". Use o português para as descrições dos casos de teste e detalhes do cenário, mas mantenha as palavras-chave do Gherkin em inglês.

# Exemplo
# Descrição do caso de teste:
# Usuário tenta login com credenciais inválidas.

Scenario: Login com senha inválida
  Given o usuário está na página de login
    And o usuário insere um nome de usuário válido
  When o usuário insere uma senha inválida e clica no botão de login
  Then o sistema exibe uma mensagem de erro indicando que a senha está incorreta
    And o campo de senha é limpo

Agora, converta a seguinte descrição de caso de teste em exatamente um cenário BDD usando a sintaxe estrita de Gherkin. A saída deve seguir exatamente o formato do exemplo fornecido e não conter nada além do cenário BDD. Somente as palavras-chave do Gherkin devem estar em inglês; todo o outro texto deve estar em português. Se o caso de teste fornecido estiver em inglês, traduza-o para o português na geração do cenário BDD, mantendo as palavras-chave (Given, When, Then, And) em inglês.

Descrição do Caso de Teste:
{test_case}

Para um bom cenário BDD, certifique-se de declarar claramente o valor de negócio ou resultado esperado e mantenha o foco em uma única ação e seu resultado. Use apenas os passos essenciais (Given, When, Then, And) de forma clara e declarativa, evitando detalhes de implementação e repetições desnecessárias. Garanta que os cenários sejam independentes, utilizem uma terminologia de negócios consistente sem jargões técnicos e que os passos sejam escritos em terceira pessoa para evitar múltiplas interpretações.
Certifique-se de que os cenários BDD tenham indentação consistente de dois espaços para cada passo sob 'Scenario', sem linhas em branco entre os passos, e uma linha em branco separando diferentes cenários.

Siga esta estrutura para a saída:

Scenario: [Descrição do Cenário]
  Given [algum contexto inicial]
    And [mais algum contexto, se houver]
  When [uma ação é realizada]
  Then [um conjunto específico de resultados deve ocorrer]
    And [outro resultado, se houver]

A resposta deve conter estritamente apenas a sintaxe válida de Gherkin e evitar qualquer informação ou comentário extra. A resposta deve conter apenas o texto BDD, sem formatação ou texto adicional (por exemplo, sem 'gherkin```'), e deve ser apenas um cenário BDD.
````

## Few-shot user template

````text
Converta a seguinte descrição de caso de teste em um único cenário BDD, usando a sintaxe estrita de Gherkin. Certifique-se de que a saída contenha apenas a sintaxe de Gherkin para o cenário, sem comentários, explicações ou a palavra "Feature". Use o português para as descrições dos casos de teste e detalhes do cenário, mas mantenha as palavras-chave do Gherkin em inglês.

# Exemplo 1
# Descrição do caso de teste:
# Usuário tenta login com credenciais inválidas.

Scenario: Login com senha inválida
  Given o usuário está na página de login
    And o usuário insere um nome de usuário válido
  When o usuário insere uma senha inválida e clica no botão de login
  Then o sistema exibe uma mensagem de erro indicando que a senha está incorreta
    And o campo de senha é limpo

# Exemplo 2
# Descrição do caso de teste:
# Usuário tenta redefinir a senha esquecida.

Scenario: Redefinição de senha com e-mail válido
  Given o usuário está na página de redefinição de senha
    And o usuário insere um endereço de e-mail registrado
  When o usuário clica no botão de enviar
  Then o sistema exibe uma mensagem indicando que um link de redefinição de senha foi enviado para o e-mail do usuário
    And o usuário é redirecionado para a página de login

# Exemplo 3
# Descrição do caso de teste:
# Usuário adiciona um item ao carrinho de compras.

Scenario: Adicionar item ao carrinho de compras
  Given o usuário está na página de detalhes de um produto
    And o produto está disponível em estoque
  When o usuário clica no botão "Adicionar ao carrinho"
  Then o item é adicionado ao carrinho de compras
    And o sistema exibe uma mensagem de confirmação "Item adicionado ao carrinho com sucesso"
    And o ícone do carrinho de compras é atualizado para refletir o novo item

Agora, converta a seguinte descrição de caso de teste em exatamente um cenário BDD usando a sintaxe estrita de Gherkin. A saída deve seguir exatamente o formato do exemplo fornecido e não conter nada além do cenário BDD. Somente as palavras-chave do Gherkin devem estar em inglês; todo o outro texto deve estar em português. Se o caso de teste fornecido estiver em inglês, traduza-o para o português na geração do cenário BDD, mantendo as palavras-chave (Given, When, Then, And) em inglês.

Descrição do Caso de Teste:
{test_case}

Para um bom cenário BDD, certifique-se de declarar claramente o valor de negócio ou resultado esperado e mantenha o foco em uma única ação e seu resultado. Use apenas os passos essenciais (Given, When, Then, And) de forma clara e declarativa, evitando detalhes de implementação e repetições desnecessárias. Garanta que os cenários sejam independentes, utilizem uma terminologia de negócios consistente sem jargões técnicos e que os passos sejam escritos em terceira pessoa para evitar múltiplas interpretações.
Certifique-se de que os cenários BDD tenham indentação consistente de dois espaços para cada passo sob 'Scenario', sem linhas em branco entre os passos, e uma linha em branco separando diferentes cenários.

Siga esta estrutura para a saída:

Scenario: [Descrição do Cenário]
  Given [algum contexto inicial]
    And [mais algum contexto, se houver]
  When [uma ação é realizada]
  Then [um conjunto específico de resultados deve ocorrer]
    And [outro resultado, se houver]

A resposta deve conter estritamente apenas a sintaxe válida de Gherkin e evitar qualquer informação ou comentário extra. A resposta deve conter apenas o texto BDD, sem formatação ou texto adicional (por exemplo, sem 'gherkin```'), e deve ser apenas um cenário BDD.
````

## Executable sources

The executable notebooks remain the authoritative source for prompt assembly, model chat-template application, decoding parameters, output cleaning, and retry logic:

- [Qwen3-8B](../prompts/Geracao_Gherkin_Qwen3_8B_RunPod_3_Tecnicas.ipynb)
- [Gemma 4 E4B IT](../prompts/Geracao_Gherkin_Google_Gemma4_E4B_IT_RunPod_3_Tecnicas.ipynb)
- [Granite 4.1 8B](../prompts/Geracao_Gherkin_IBM_Granite4_1_8B_3_Tecnicas_Sequencial.ipynb)
- [Llama 3 8B Instruct](../prompts/Geracao_Gherkin_Llama3_RunPod_3_Tecnicas.ipynb)
- [Mistral 7B Instruct v0.3](../prompts/Geracao_Gherkin_Mistral_7B_Instruct_v0_3_RunPod_3_Tecnicas.ipynb)
