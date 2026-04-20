# E2 — Design Técnico, Arquitetura e Backlog

> **Disciplina:** Teoria dos Grafos  
> **Prazo:** 13 de abril de 2026  
> **Peso:** 20% da nota final  

---

## Identificação do Grupo

| Nome do projeto |Rouba Bandeira|
| Integrante 1 | Renan veras de andrade-RGM 39081168 |
| Integrante 2 | Augusto Da Silva Domingues-RGM 36919110 |

---

## 1. Algoritmos Escolhidos

### 1.1 Algoritmo Principal

🔹 Algoritmo 1: Algoritmo de Dijkstra

Categoria: Guloso, estratégia de resolução (Greedy)

Complexidade de Tempo: $O((V + E)logV)$ (usando Fila de Prioridade)

Complexidade de Espaço: $O(V + E)$

Justificativa: Diferente da Busca em Largura (BFS), o Dijkstra consegue "enxergar" os pesos das arestas. Se um caminho é mais curto fisicamente, mas tem um "peso de risco" (adversários) muito alto, o Dijkstra encontrará a rota alternativa mais segura e eficiente.

Comparação com Alternativa:Embora o Bellman-Ford também encontre o menor caminho, ele é menos eficiente para o nosso domínio. Sua complexidade de tempo é $O(V \cdot E)$, enquanto o Dijkstra com Fila de Prioridade é $O((V + E) \log V)$. 

Limitações: O algoritmo assume que os pesos das arestas (risco e distância) são fixos. Se um oponente mudar de posição rapidamente, o caminho calculado pode se tornar perigoso antes de ser concluído, exigindo uma nova execução do algoritmo

Referência: CORMEN, T. H. et al. Algoritmos: teoria e prática. 3. ed. Rio de Janeiro: Elsevier, 2012

-----------

🔹 Algoritmo 2: BFS (Para verificar a conectividade do mapa ou achar rotas rápidas sem peso).

Categoria:  Busca em Grafo (ou Busca em Largura).

Complexidade de Tempo: $O((V + E)logV)$ (usando Fila de Prioridade)

Complexidade de Espaço: $O(V + E)$

Justificativa: BFS será utilizada como uma ferramenta de pré-processamento e validação. Antes de gastar recursos computacionais calculando a rota de menor risco com o Dijkstra, a BFS verifica se o mapa carregado via JSON é válido

Comparação com Alternativa: Enquanto o Dijkstra busca o caminho de menor custo acumulado, a BFS funciona como um validador de topologia, garantindo que o destino é alcançável e fornecendo a distância base em saltos para compararmos com a eficiência da rota de risco calculada pelo algoritmo principal.

Limitações: A principal limitação do uso do Dijkstra no projeto Rouba Bandeira é a sua natureza estática. Como o jogo envolve oponentes que se movimentam pelo campo, o custo das arestas (risco de captura) pode mudar após o cálculo inicial. O algoritmo não prevê o movimento dos adversários, exigindo execuções frequentes para manter a rota atualizada diante de mudanças no ambiente.

Referência: CORMEN, T. H. et al. Algoritmos: teoria e prática. 3. ed. Rio de Janeiro: Elsevier, 2012


## 2. Arquitetura em Camadas



![Diagrama de arquitetura](./docs/arquitetura_e2.png)

### Descrição das camadas

A arquitetura em 4 camadas foi escolhida para garantir o desacoplamento do sistema. A lógica dos algoritmos de grafos (Dijkstra/BFS) fica isolada na camada de Domínio, permitindo que a forma de entrada de dados (JSON via Infraestrutura) ou a interface de usuário (Apresentação) sejam alteradas sem impactar a regra de negócio central do jogo Rouba Bandeira.

Apresentação (UI/CLI):  Na prática, quando você rodar o programa no console do VS Code, a apresentação da "Questão 2" deve mostrar algo assim:

Entrada: O usuário escolhe a opção "Validar Mapa" ou "Cálculo Simples".


Processamento: O sistema chama a BFS na camada de Domínio.

Saída: O console exibe:

"Status do Mapa: Conectado."

"Menor número de zonas a atravessar (ignorando risco): X vértices

Aplicação (Service):  Quando o usuário pede uma rota, a Aplicação primeiro solicita à Infraestrutura que carregue o JSON. Em seguida, ela pode chamar a BFS para confirmar se o mapa está conectado e, se estiver tudo certo, chama o Dijkstra para calcular a rota de menor risco

Domínio (Core): O domínio funciona como o cérebro do Rouba Bandeira, onde transformamos o mapa do jogo em uma estrutura matemática de Grafos para que o computador entenda os caminhos"

Infraestrutura (I/O): Esta camada tem a missão de isolar o restante do software das tecnologias de armazenamento. Se amanhã você decidir trocar o arquivo JSON por um Banco de Dados SQL, você alteraria apenas esta camada, mantendo o Dijkstra e a BFS intactos.


---

## 3. Estrutura de Diretórios

rouba-bandeira-grafos/
├── docs/
│   ├── README.md
│   ├── E1_template.md
│   └── E2_template.md          # Este documento que estamos preenchendo
│   └── arquitetura_e2.png      # A imagem do diagrama de camadas
├── src/
│   ├── presentation/           # Camada de Apresentação
│   │   └── main.py             # Interface de linha de comando (CLI)
│   ├── application/            # Camada de Aplicação
│   │   └── game_service.py     # Orquestrador entre lógica e dados
│   ├── domain/                 # Camada de Domínio (Core)
│   │   ├── graph.py            # Estrutura do Grafo
│   │   └── algorithms.py       # Implementação do Dijkstra e BFS
│   ├── infrastructure/         # Camada de Infraestrutura (I/O)
│   │   └── json_adapter.py     # Leitor do arquivo de mapa
├── tests/
│   ├── test_dijkstra.py
│   └── test_bfs.py
├── data/
│   └── mapa_teste.json         # Seu Dataset definido no item 4
└── requirements.txt            # Dependências (se houver)

> **Justificativa de desvios** *(se houver)*: 

## 4. Definição do Dataset

**Formato : JSON.
 
**Estratégia de Geração: A nossa estratégia é criar um script que gera mapas automáticos. Isso serve para provar que o nosso código não funciona só com o mapa que a gente desenhou na mão, mas sim com qualquer configuração de jogo que o computador inventar

Exemplo de  JSON
{
  "nome_mapa": "Campo_Mogi_Dutra_v1",
  "versao": "2026.1",
  "vertices": [
    { "id": "V0", "label": "Base Time A", "tipo": "base" },
    { "id": "V1", "label": "Zona Neutra Central", "tipo": "transicao" },
    { "id": "V2", "label": "Base Time B (Bandeira)", "tipo": "objetivo" }
  ],
  "arestas": [
    { 
      "origem": "V0", 
      "destino": "V1", 
      "distancia": 25, 
      "risco": 3,
      "descricao": "Caminho pela mata" 
    },
    { 
      "origem": "V1", 
      "destino": "V2", 
      "distancia": 15, 
      "risco": 9,
      "descricao": "Corredor aberto exposto" 
    }
  ]
}

No nosso JSON, cada caminho (aresta) tem dois valores: distancia e risco. Isso permite que o nosso algoritmo de Dijkstra decida se vale a pena pegar um caminho curto porém perigoso, ou um longo e seguro.
---

## 5. Backlog do Projeto

### 5.1 In-Scope — O que será implementado

1 Carregamento de Mapa: via JSON,Alta,Dado um arquivo JSON no formato definido, quando o sistema for iniciado, então os dados de vértices e arestas devem ser carregados corretamente na memória.

2,Validação de Conectividade: (BFS),Alta,Dado um ponto inicial e um destino, quando o algoritmo BFS for executado, então o sistema deve informar se existe um caminho físico entre eles antes de calcular a rota.

3,Cálculo de Rota Segura: (Dijkstra),Alta,Dado que o mapa possui pesos de 'risco', quando o usuário solicitar a melhor rota, então o sistema deve retornar o caminho com o menor custo total (distância + perigo).

4,Interface de Linha de Comando: (CLI),Média,Dado que o usuário está no terminal do VS Code, quando ele interagir com o menu, então o sistema deve exibir de forma clara o passo a passo da rota encontrada

5,Relatório de Performance: (Big-O),Baixa,Dado a execução de um mapa gerado aleatoriamente, quando o cálculo terminar, então o sistema deve exibir o tempo gasto e o número de nós processados.

### 5.2 Out-of-Scope — O que NÃO será feito

Interface Gráfica: (GUI),O projeto focará exclusivamente em uma interface via Terminal (CLI) para garantir que a performance do algoritmo de Dijkstra seja o foco principal, sem distrações com bibliotecas visuais pesadas.

Edição de Mapa: em Tempo Real,A alteração de vértices e arestas será feita apenas via edição direta no arquivo JSON. Implementar um editor dentro do software exigiria uma complexidade de I/O que ultrapassa o cronograma atual

Modo Multi-Agente: (IA contra IA),O sistema processará a rota para um agente por vez. A simulação de múltiplos competidores em tempo real (colisão e disputa) exigiria concorrência e sincronização que não fazem parte do escopo de Teoria dos Grafos.
---

## Checklist de Entrega

- [x] Big-O de tempo e espaço declarados para cada algoritmo

 Dijkstra Tempo $O(V + E)$ e Espaço $O(V + E)$. BFS: Tempo $O(V + E)$ e Espaço $O(V + E)$.

- [x] Ao menos 1 alternativa descartada com justificativa

Interface Gráfica: (GUI),O projeto focará exclusivamente em uma interface via Terminal (CLI) para garantir que a performance do algoritmo de Dijkstra seja o foco principal, sem distrações com bibliotecas visuais pesadas


- [x] Diagrama de arquitetura com 4 camadas identificadas

1 Artefatos: main.py, menus de console 2 Camada de Aplicação (Application)  3 Camada de Domínio (Domain/Core)  4 Camada de Infraestrutura (Infrastructure)

- [x] Referência bibliográfica para cada algoritmo (ABNT ou IEEE)

CORMEN, T. H. et al. Algoritmos: Teoria e Prática. 3. ed. 2012. | SEDGEWICK, R. Algorithms. 4. ed. 2011.

- [x] Backlog com ≥ 5 itens In-Scope e ≥ 3 Out-of-Scope

In-Scope: Leitura de Grafo via JSON, Grafo (Lista de Adjacência), Verificação de Caminho (BFS), Cálculo de Rota Otimizada (Dijkstra), Interface de Console (CLI), Simulação de Mapas de Estresse. Out-of-Scope: GUI, Tempo Real, Bloqueio de Contas.

- [x] Ao menos 3 critérios de aceite no formato "dado / quando / então"

C1: Dado o foco do projeto em eficiência algorítmica e Arquitetura em Camadas, quando o sistema for executado, então a aplicação deve funcionar integralmente via console sem depender de Interface Gráfica (GUI).

C2: Dado um arquivo JSON como fonte oficial dos dados, quando houver necessidade de alterar pesos ou conexões do grafo, então as mudanças devem ser realizadas diretamente no arquivo de persistência e recarregadas pelo sistema.

C3: Dado um dataset estático baseado em arquivos de configuração, quando o sistema inicializar e processar consultas, então os dados devem ser carregados sem necessidade de banco de dados SQL.

- [x] Exemplo de estrutura de arquivo de entrada presente
{
  "vertices": [
    { "id": "C1", "tipo": "CONTA", "label": "Jogador_01" },
    { "id": "IP1", "tipo": "ACESSO", "label": "Ponto_Conexao_A" },
    { "id": "C2", "tipo": "CONTA", "label": "Jogador_02" }
  ],
  "arestas": [
    { 
      "origem": "C1", 
      "destino": "IP1", 
      "tipo": "ACESSA", 
      "peso_distancia": 12, 
      "peso_risco": 3 
    },
    { 
      "origem": "IP1", 
      "destino": "C2", 
      "tipo": "ACESSA", 
      "peso_distancia": 8, 
      "peso_risco": 7 
    }
  ]
}
---

*Teoria dos Grafos — Profa. Dra. Andréa Ono Sakai*
