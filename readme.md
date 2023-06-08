# Path Planning and Path Covering using Q Learning Algorithm

#### Aluno: [Daniel Werneck Rodrigues](https://github.com/DanielWerneckRodrigues)
#### Orientador: [Evelyn Batista](https://github.com/FelipeBorgesC) #ALTERAR

---

Trabalho apresentado ao curso [BI MASTER](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de curso e obtenção de crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

- [Projeto.pdf](https://github.com/DanielWerneckRodrigues/UAV---Path-Covering/tree/main)

---

### Resumo

Calamidades sempre ocorreram no mundo e, muitas vezes, a improbabilidade de ocorrerem, as torna de difícil previsão e precaução.  Sendo assim, busca-se o melhor comportamento e planejamento diante de um desastre, de forma a maximizar a quantidade de sobreviventes e minimizar o tempo de exposição ao perigo ao qual as vítimas estão sendo submetidas. O objetivo deste trabalho é otimizar o planejamento do resgate de vítimas, concluindo a busca com a menor quantidade de passos, dado um cenário em que conhecemos as prioridades de cada subespaço em nosso espaço de busca. Para resolução deste problema, será utilizada o método Q Learning na área de Reinforcement Learning para a otimização da rota a ser percorrida pelo agente.

### Abstract
--


### 1. Introdução

Calamidades sempre ocorreram no mundo e, muitas vezes, a improbabilidade de ocorrerem, as torna de difícil previsão e precaução.  Sendo assim, busca-se o melhor comportamento e planejamento diante de um desastre, de forma a maximizar a quantidade de sobreviventes e minimizar o tempo de exposição ao perigo ao qual as vítimas estão sendo submetidas. O objetivo deste trabalho é otimizar o planejamento do resgate de vítimas, concluindo a busca com a menor quantidade de passos, dado um cenário em que conhecemos as prioridades de cada subespaço em nosso espaço de busca. Para resolução deste problema, será utilizada o método Q Learning na área de Reinforcement Learning para a otimização da rota a ser percorrida pelo agente.

Em Reinforcement Learning, o aprendizado ocorre através da interação de um agente com o ambiente, recebendo recompensas ou punições. Após n jogos jogados, temos um agente especializado no problema em questão que aprende através da maximização total das recompensas recebidas ao longo de cada jogo. Quase todos os problemas de Reinforcement Learning usa o framework matemático de Markov Decision Process (MDP), em que estados futuros dependem apenas do estado presente.

A programação dinâmica é outro conceito importante para problemas de Reinforcement Learning, que se consiste em uma técnica para resolução de problemas complexos através da separação desse problema em subproblemas e do aproveitamento de soluções previamente estabelecidas para poupar esforço computacional, o que minimiza o tempo de processamento.

Ainda, para o trabalho aqui considerado será utilizada a abordagem de Diferença Temporal, que não requer conhecimento prévio a respeito da dinâmica do modelo. O algoritmo de aprendizado TD foi introduzido por Sutton em 1988. O algoritmo traz os benefícios do método de Monte Carlo e da programação dinâmica (DP). Como o método de Monte Carlo, não requer dinâmica de modelo e, como DP, não requer esperar até o final do episódio para fazer uma estimativa da função de valor. Em vez disso, é realizada uma aproximação da estimativa atual com base na estimativa aprendida anteriormente.

Será utilizado o algorítimo de Q Learning, que se trata de algoritmo simples e muito popular em Temporal Difference Learning. Esse algoritmo tem como objetivo a atualização do Q Value(Q(s,a)), a cada passo do agente no ambiente, e ao final, encontrar os pares de estado ação que levarão a maximização da recompensa final.

Além do principal objetivo e da aplicabilidade direta na área de calamidades, o objetivo é que a solução proposta possa ser aplicada a qualquer outro tipo de problema que envolva o deslocamento de um agente em um espaço condicionado por pontos previamente estabelecidos como prioritários, com menor quantidade de passos dados.

### 2. Problema e Premissas

Cada subespaço do nosso espaço de busca será subdividido em quadrantes, onde cada quadrante terá uma prioridade pré-definida. Essa prioridade pode ser estabelecida de acordo com: maior chance de sobrevivência das vítimas contidas no quadrante, maior quantidade de vítimas no quadrante, ou qualquer outra forma de priorização que se julgue adequada para o problema em questão. Para este trabalho será considerado uma matriz de prioridades onde temos um epicentro da catástrofe com maior priorização e, quanto mais afastado deste epicentro, menores serão as prioridades.
Além disso, também serão pré-definidos obstáculos no espaço de busca que estarão contidos nas matrizes de prioridades e marcadas com valor -100.

O problema foi resolvido em etapas, da mais simples para a mais complexa. Sendo assim serão considerados os cenários 

            i.	‘matriz muito simples’ (matriz 4x3);
            ii.	‘matriz simples’ (5x5);
            iii.    ‘matriz média’ (8x8);
            iv.	‘matriz complexa’ (9x13).


### 3. Modelagem

Para resolução do problema foi desenvolvido um ambiente com a definição de duas classes. A primeira de “estado”, que define os atributos de estado percorrido pelo agente, e “agente”, com os atributos e os métodos pertinentes, como o de execução de cada jogo e a atualização da tabela Q por cada passo (round). O objetivo de cada jogo é, com menor quantidade de passos, percorrer todos os quadrantes com valores maiores que zero, privilegiando os subespaços com maiores valores de prioridade. 

A variável de deflação temporal (d_f), desenvolvida especificamente neste trabalho e que não faz parte da modelagem clássica, é responsável por reduzir as recompensas futuras de acordo com a quantidade de passos dados. 

Como para cada cenário avaliado temos diferente quantidade de passos médios para conclusão do problema, se faz necessário identificar o número ideal da variável deflatora (d_f) para cada cenário. Inicialmente foi identificando, para cada cenário, o d_f ideal dentro do seguinte espaço de busca. 
Cada configuração a seguir teve o modelo rodado 10 vezes para obtenção de resultados:

    i.	‘matriz muito simples’ (matriz 4x3) -> [0.8, 0.9, 0.95, 0.99, 0.999, 0.9999];
    ii.	‘matriz simples’ (5x5) -> [0.9, 0.95, 0.99, 0.999, 0.9999];
    iii.    ‘matriz média’ (8x8) -> [0.95, 0.99, 0.999, 0.9999];
    iv.	‘matriz complexa’ (9x13) -> [0.95, 0.99, 0.999, 0.9999];

Posteriormente foi realizada uma busca de melhores hiperparâmetros para cada cenário, em que tivemos o modelo rodado 10 vezes para cada configuração, sendo avaliados:
i.	 ‘matriz muito simples’ (matriz 4x3):
    o	Learning rate = [0.01, 0.001, 0.0001]
    o	Gamma = [0.95, 0.99]
    o	d_f = 0.95
    o	max exp_rate (epsilon) = 1.0
    o	min exp_rate decay (epsilon) = 0.01
    o	exp_rate decay (epsilon) = [0.001, 0.005]
    o	quantidade maxima de passos = 5.000
    o	quantidade de episódios = 8000

ii.	‘matriz simples’ (5x5):
    o	Learning rate = [0.01, 0.001, 0.0001]
    o	Gamma = [0.95, 0.99]
    o	d_f = [0.8, 0.9, 0.95, 0.999]
    o	max exp_rate (epsilon) = 1.0
    o	min exp_rate decay (epsilon) = 0.01
    o	exp_rate decay (epsilon) = [0.001, 0.005]
    o	quantidade maxima de passos = 10.000
    o	quantidade de episódios = 8000

iii.	‘matriz média’ (8x8):
    o	Learning rate = [0.01, 0.001, 0.0001]
    o	Gamma = [0.95, 0.99]
    o	d_f = [0.8, 0.9, 0.95, 0.999]
    o	max exp_rate (epsilon) = 1.0
    o	min exp_rate decay (epsilon) = 0.01
    o	exp_rate decay (epsilon) = [0.001, 0.005]
    o	quantidade maxima de passos = 10.000
    o	quantidade de episódios = 8000

iv.	‘matriz simples’ (5x5):
    o	Learning rate = [0.01, 0.001, 0.0001]
    o	Gamma = [0.95, 0.99]
    o	d_f = [0.8, 0.9, 0.95, 0.999]
    o	max exp_rate (epsilon) = 1.0
    o	min exp_rate decay (epsilon) = 0.01
    o	exp_rate decay (epsilon) = [0.001, 0.005]
    o	quantidade maxima de passos = 10.000
    o	quantidade de episódios = 8000

v.	‘matriz complexa’ (9x13)
    o	Learning rate = [0.01, 0.001, 0.0001]
    o	Gamma = [0.95, 0.99]
    o	d_f = [0.8, 0.9, 0.95, 0.999]
    o	max exp_rate (epsilon) = 1.0
    o	min exp_rate decay (epsilon) = 0.01
    o	exp_rate decay (epsilon) = [0.001, 0.005]
    o	quantidade maxima de passos = 10.000
    o	quantidade de episódios = 8000

### 4. Resultados

(Um parágrafo)

### 4. Conclusões

(2 parágrafos)

---

Matrícula: 192.110.215

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Business Intelligence Master*
