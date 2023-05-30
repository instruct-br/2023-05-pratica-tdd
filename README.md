
# Prática de TDD

TDD (Test-driven development) é uma técnica de desenvolvimento de software em que o desenvolvimento inicia pela escrita dos testes, é seguido da implementação mais básica para os testes passarem e finalmente múltiplas rodadas de refatoração até que o código fique bom o suficiente.

O conceito é simples na teoria, mas para aprender é necessário praticar. Este repositório contem um enunciado para guiar no desenvolvimento de um programa usando TDD.

## Problema

_Em Abril de 2023 o STF publica decisão sobre 'revisão da vida toda' do INSS. A decisão estabelece que devem prevalecer todas as contribuições no cálculo do benefício, se o resultado for mais favorável ao segurado, e não apenas as feitas a partir de julho de 1994, como estabeleceu a reforma de 1999._

_Quem recebia salários mais altos antes de 1994, pode aumentar o benefício com a revisão. Já para quem recebia salários mais altos depois de 1994, vale a pena considerar somente as contribuições feitas após novas mudanças nas regras, em 1999._

_De acordo com o STF, também só tem direito quem recebeu o primeiro pagamento da aposentadoria nos últimos dez anos, e não pode ter tido o benefício concedido depois de 12 de novembro de 2019._

https://g1.globo.com/jornal-nacional/noticia/2023/04/13/stf-publica-decisao-sobre-revisao-da-vida-toda-do-inss.ghtml

## Solução

Desenvolver uma calculadora simplificada para ajudar alguém a determinar se é vantajoso ou entrar com o pedido de revisão. Para isso é preciso implementar a forma que era determinado o valor da aposentadoria antes da Reforma da Previdência de 2019.

Para chegar nos valores finais, é necessário implementar algumas regras:

### Cálculo básico

Definição inicial do valor, depende dos valores das contribuições feitas

- Contribuições são mensais
- Para quem se aposentou até 12/11/2019: considerar a média das 80% maiores contribuições entre 07/1994 e 12/11/2019
- Para quem se aposentou a partir de 13/11/2019: considerar a média de todas as contribuições

### Fator previdenciário

Porcentagem aplicada ao valor encontrado no cálculo básico, que reduz o valor da aposentadoria de maneira inversamente proporcional ao tempo de contribuição.

- Para quem se aposentou até 12/11/2019: 70% + 1% para cada ano de contribuição
- Para quem se aposentou a partir de 13/11/2019: 60% + 2% para cada ano de contribuição após o tempo mínimo

O tempo mínimo para homens é 20 anos e 15 para mulheres, ou seja, para conseguir uma aposentadoria integral, após a reforma de 2019, um homem deve contribuir por 40 anos e uma mulher por 35.

### Teto da contribuição

Uma contribuição não pode ser maior do que o teto do INSS no mês em que a contribuição foi feita. Consulte a tabela abaixo para considerar o teto e corrigir as contribuições recebidas como entrada no seu software:

https://previdenciarista.com/blog/tabela-historica-de-tetos-previdenciarios-da-previdencia-social-inss-a-partir-de-1994/

---

| ⚠️ | Destaque novamente para o termo "calculadora simplificada", com ênfase no _simplificada_. O software desenvolvido como resultado deste exercício não deve ser usado para determinar se é ou não vantajoso entrar com um pedido de revisão. Alguns valores foram fixados, não há correções monetárias e diversas regras não foram sequer mencionadas. Um pedido de revisão incorreto pode **diminuir** o valor do benefício recebido. O exercício, apesar de se basear em acontecimentos da realidade, tem como objetivo apenas praticar o desenvolvimento de um programa com um conjunto mínimo de regras de negócio.  |
| --- | --- |

---

## Implementação

Você pode implementar o software proposto como um CLI, uma API ou simplesmente um módulo Python que expõe alguma(s) funções para interagir com o seu programa.

### Primeiros passos

Pense na interface do seu programa, quais são as entradas que o usuário deve fornecer? Qual é o formato da saída? Uma string? Uma lista? Um dicionário? Qual o conteúdo dessa estrutura?

Comece considerando apenas o cálculo básico.

Elabore um caso, o mais simples possível, para testar se o seu programa funciona. A média de uma lista de números com o mesmo valor é o valor. Então, apesar de não fazer muito sentido prático, crie um caso de teste em que uma pessoa fez N contribuições com exatamente o mesmo valor.

Escreva o teste: ele passa a entrada para exercitar esse caso (pessoa que fez N contribuições iguais) e checa se o resultado foi esse mesmo valor.

Implemente o código: escreva as assinaturas necessárias para executar o teste que foi escrito e retorne o mesmo valor do teste **hardcoded**, ignorando os valores passados como entrada e ainda sem nenhuma lógica implementada.

Rode os testes e confirme que eles passam, ou seja, que você está no verde. Mude o valor hardcoded na implementação e rode novamente os testes, confirme se eles falharam, ou seja, que você está no vermelho. Se eles não falharam revise a configuração do seu projeto. Se eles falharam, volte o valor correto para a implementação.

### Shameless Green

> Shameless Green is defined as the solution that quickly reaches green while prioritizing understandability over changeability. It uses tests to drive comprehension, and patiently accumulates concrete examples while awaiting insight into underlying abstractions. It doesn’t dispute that DRY is good, rather, it believes that it is cheaper to manage temporary duplication than to recover from incorrect abstractions.

https://sandimetz.com/99bottles-sample-js#_shameless_green

Escreva agora uma solução concreta para o teste passar, mas faça a implementação mais simples possível. Não tente remover repetições do código ainda, não se apoie em abstrações.

Quando você chegar no verde novamente, faça um commit.

Agora pense em mais um ou dois casos de teste, dessa vez explore mais as regras do problema para garantir que você vai testar os **comportamentos** importantes.

Escreva um desses casos de teste, depois a implementação. Quando você chegar no verde, faça um commit.

### Próxima regra

Quando você estiver confiante que a sua solução atende o Cálculo Basíco, pense agora em casos de teste para a segunda regra: o Fator Previdenciário.

Novamente, comece implementando um teste e depois a solução para esse teste.

Conforme você adiciona novas regras e novos testes, para validar o comportamento da sua solução, algum dos testes antigos podem falhar: ou porquê o caso não é compatível com a nova regra inserida, ou porquê o novo teste revelou um defeito que havia na sua solução. Ajuste sua base de código de acordo com o necessário.

Crie um commit quando você voltar para o verde.

Continue implementando pensando no _Shameless Green_.

### Todas as regras

Repita o processo até você esgotar todas as regras e ter casos de teste o suficiente para ter confiança de que a sua solução atende o problema. Agora é hora de refatorar sua solução.

Observe a sua implementação e tente encontrar _code smells_:
https://refactoring.guru/refactoring/smells

Aplique uma das técnicas de refatoração recomendada para lidar com o _code smell_ identificado. Quando você terminar e voltar para o verde, faça um commit.

Dê passos pequenos.

### Red, Green, Refactor

Repita o processo algumas vezes até que fique difícil identificar algum _code smell_ na sua solução. Conforme você adquirir mais confiança, pode espaçar mais o tempo entre os commits, mas execute os testes com frequência para garantir que alguma alteração não introduziu um problema.

Durante esse processo você vai encontrar oportunidades para melhorar o design do seu código, teste essas hipóteses. Se alguma delas der errado, basta descartar as alterações e voltar para o último commit, onde o seu código está funcional.

### Rebase

Se você seguiu corretamente todos os passos até aqui, você precisou pensar em detalhes no design da sua solução antes de implementar e provavelmente chegou em uma solução boa o suficiente para o problema apresentado. Além disso, o seu código provavelmente tem mais do que 90% de cobertura de testes. Verifique.

No entanto, o histórico de commits do seu projeto provavelmente está cheio. Antes de empurrar essas alterações em algum lugar, você pode fazer um rebase para recontar a história dessa branch, de maneira mais breve.

