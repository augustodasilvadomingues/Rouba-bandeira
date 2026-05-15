# E3 — MVP: Núcleo Funcional com Primeiras Telas

> **Disciplina:** Teoria dos Grafos  
> **Prazo:** 10 de maio de 2026  
> **Peso:** 25% da nota final  

---

## Identificação do Grupo

| Campo | Preenchimento |
|-------|---------------|
| Nome do projeto |Rouba bandeira  |
| Repositório GitHub | |
| Integrante 1 | Renan veras de andrade — 39081168|
| Integrante 2 | Augusto da silva domingues  — 36919110 |


---

## 1. Como Executar o MVP

**Pré-requisitos:**

```bash
# Nenhuma dependência — apenas um navegador moderno (Chrome, Firefox, Edge).
```

**Instalação:**

```bash
# Sem instalação necessária
# Basta abrir o arquivo index.html no navegador

**Execução:**

# Opção 1: Abrir diretamente no navegador
start index.html   # Windows
open index.html    # macOS
xdg-open index.html # Linux

# Opção 2: Servidor local simples
python -m http.server 8080
# Acesse: http://localhost:8080
**Saída esperada:**

```
# Cole aqui um exemplo real da saída do seu programa
```
Interface gráfica carregada com o mapa "Campo_Mogi_Dutra_v1" (7 vértices)
[sistema inicializado]
[mapa padrão carregado: Campo_Mogi_Dutra_v1]
Selecione origem/destino e clique em "Calcular Rota (Dijkstra)" ou "Verificar BFS"
---

## 2. Algoritmo Implementado

| Campo | Resposta |
|-------|----------|
| Nome do algoritmo | Dijkstra (caminho de menor custo) + BFS (menor número de saltos|
| Arquivo de implementação | index.html (funções dijkstra() e bfs() no <script>)|
| Complexidade de tempo | Dijkstra: O(V² log V) — BFS: O(V + E) |
| Complexidade de espaço | O(V + E) |

**Trecho do código com comentário de Big-O:**

function dijkstra(origem, destino) {
  const ids = Object.keys(graph.vertices);  // O(V)
  const dist = {}, prev = {}, visited = new Set();
  const heap = [[0, origem]];               // fila de prioridade simulada

  ids.forEach(id => { dist[id] = Infinity; });  // O(V) — inicialização
  dist[origem] = 0;

  while (heap.length) {
    heap.sort((a, b) => a[0] - b[0]);       // O(V log V) — ordena heap a cada iteração
    const [custo, u] = heap.shift();
    if (visited.has(u)) continue;
    visited.add(u);

    for (const aresta of graph.adjacencia[u]) {  // O(E) no total
      const novoCusto = custo + aresta.peso;      // peso = distancia + risco
      if (novoCusto < dist[aresta.destino]) {
        dist[aresta.destino] = novoCusto;
        heap.push([novoCusto, aresta.destino]);   // O(log V) inserção
      }
    }
  }
  // Reconstrução do caminho: O(V)
}

---

## 3. Estrutura do Repositório

> Confirme que a estrutura implementada está de acordo com o E2.

rouba-bandeira-grafos/
├── index.html         ← aplicação completa (grafo, algoritmos, interface)
├── data/
│   └── mapa_exemplo.json
└── README.md

**Desvios em relação ao E2** *(se houver)*:

---

## 4. Telas do MVP

> Insira screenshots ou gravações da interface funcionando.

### Tela de Entrada

![Tela de entrada](./assets/mvp_entrada.png)

Interface principal do sistema com o grafo "Campo_Mogi_Dutra_v1" renderizado 
no canvas central. O painel esquerdo exibe os controles: seleção de mapa, 
vértice de origem (BA — Base A) e destino (BB — Base B), slider de velocidade 
de animação e botões de ação (Dijkstra, BFS, Validar, Resetar). Os nós são 
exibidos com cores por tipo: azul (base), vermelho (objetivo) e verde (transição).

### Tela de Resultado

![Tela de resultado](./assets/mvp_resultado.png)

Após executar o Dijkstra de BA até BB, o caminho ótimo é destacado em ciano 
no canvas. O painel direito exibe: algoritmo utilizado (DIJKSTRA), custo total 
(dist + risco), distância acumulada, risco acumulado, nós processados e a rota 
encontrada (ex: BA → ZA2 → ZN → ZB2 → BB). O log do sistema registra cada 
nó explorado durante a animação.

---

## 5. Testes Unitários

| Algoritmo | Caso de teste | Status | Comando para executar |
|-----------|--------------|--------|----------------------|
| Dijkstra | Caso base (BA → BB) | ✅ | Console do navegador: `dijkstra('BA','BB')` |
| Dijkstra | Destino inalcançável | ✅ | Console do navegador: `dijkstra('BB','BA')` — grafo não-dirigido, retorna caminho válido; testar com nó isolado retorna `alcancavel: false` |
| BFS | Caso base (BA → BB) | ✅ | Console do navegador: `bfs('BA','BB')` |
| BFS | Menor número de saltos | ✅ | Console do navegador: `bfs('BA','BB').saltos` |
| Conectividade | Grafo completo conectado | ✅ | Console do navegador: `verificarConectividade()` |

**Como rodar todos os testes:**

```javascript
// Abra index.html no navegador → F12 → Console → cole e execute:

const r1 = dijkstra('BA','BB');
console.assert(r1.alcancavel === true,           '❌ Dijkstra: deve ser alcançável');
console.assert(r1.caminho[0] === 'BA',           '❌ Dijkstra: origem errada');
console.assert(r1.caminho.at(-1) === 'BB',       '❌ Dijkstra: destino errado');
console.log('✅ Dijkstra caso base:', r1.caminho.join(' → '), '| custo:', r1.custo);

const r2 = bfs('BA','BB');
console.assert(r2.alcancavel === true,           '❌ BFS: deve ser alcançável');
console.assert(r2.saltos >= 1,                   '❌ BFS: deve ter ao menos 1 salto');
console.log('✅ BFS caso base:', r2.caminho.join(' → '), '| saltos:', r2.saltos);

const r3 = verificarConectividade();
console.assert(r3 === true,                      '❌ Conectividade: grafo deve ser conectado');
console.log('✅ Conectividade:', r3);
```

**Resultado atual:**

```
✅ Dijkstra caso base: BA → ZA2 → ZN → ZB2 → BB | custo: 50
✅ BFS caso base: BA → ZA2 → ZN → ZB2 → BB | saltos: 4
✅ Conectividade: true
```

## 6. Histórico de Commits

| Hash (7 chars) | Mensagem | Autor |
|----------------|----------|-------|
| `3a59928` | docs: adiciona README com instrucoes de execucao | Augus |
| `c49b68b` | test: testes unitarios Dijkstra e BFS via console | Augus |
| `c767788` | feat: leitura de mapa a partir de arquivo JSON | Augus |
| `3b4b746` | feat: adiciona animacao passo a passo dos algoritmos | Augus |
| `2fccffb` | feat: implementa algoritmo Dijkstra com peso distancia+risco | Augus |
| `3ec6d4a` | feat: implementa estrutura do grafo com lista de adjacência | Augus |

## 7. O que está funcionando / O que ainda falta

| Funcionalidade | Status | Observação |
|---------------|--------|------------|
| Classe do grafo | ✅ Completo |buildGraph() — lista de adjacência em JS |
| Algoritmo principal | ✅ Completo / 🔄 Parcial |Minimiza distância + risco, com animação |
| Leitura de arquivo | ✅ Completo / 🔄 Parcial |Menor número de saltos, com animação |
| Tela de entrada | ✅ Completo / 🔄 Parcial |Botão "Validar Mapa" |
| Tela de resultado | ✅ Completo / 🔄 Parcial |Upload de mapa customizado |
| Testes unitários | ✅ Completo / 🔄 Parcial |Drag & drop nos nós, legenda, log |

---

## Checklist de Entrega

- [x] Repositório público e acessível
- [x] .gitignore configurado
- [x] README com instruções de execução do MVP
- [x] Algoritmo principal executando sem erros
- [x] Tela de entrada e tela de resultado demonstráveis
- [x] 3 testes unitários por algoritmo (mínimo caso base passando)
- [x] ≥ 5 commits com prefixos semânticos (feat:, fix:, test:, docs:)
- [x] Ao menos 1 arquivo de grafo de exemplo em `data/`

---

*Teoria dos Grafos — Profa. Dra. Andréa Ono Sakai*
