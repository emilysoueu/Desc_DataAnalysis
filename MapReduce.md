
# MapReduce no Contexto de Análise de Dados

MapReduce é um modelo de programação para processamento distribuído de grandes quantidades de dados em clusters de computadores. Ele foi popularizado pelo Google e posteriormente implementado no Apache Hadoop. O modelo é baseado na divisão do problema em duas fases principais:

1. **Map (Mapeamento):** Transforma os dados de entrada em pares de chave-valor.
2. **Reduce (Redução):** Consolida os pares de chave-valor gerados na etapa de mapeamento para produzir um resultado agregado.

Esse paradigma permite processar grandes volumes de dados de forma paralela e escalável, sendo útil para tarefas como análise de logs, indexação de dados, agregação estatística e aprendizado de máquina em larga escala.

---

## 1. Arquitetura do MapReduce

O processo ocorre em três etapas:

1. **Input Splitting (Divisão dos Dados):**  
   - Os dados são divididos em pequenos blocos e distribuídos entre os nós de um cluster.
   - Cada nó processa uma parte do dado de forma independente.

2. **Fase de Map:**  
   - Cada bloco de dados é processado por um **mapper**, que transforma os dados em pares chave-valor.
   - Exemplo: Contar palavras em um texto → `("palavra", 1)`

3. **Shuffle & Sort:**  
   - O framework MapReduce reorganiza os dados, agrupando todas as chaves iguais.
   - Isso permite que a próxima fase processe valores agregados.

4. **Fase de Reduce:**  
   - O **reducer** recebe um conjunto de pares de chave-valor e combina os valores associados a cada chave.
   - Exemplo: Somar todas as contagens de uma palavra → `("palavra", total)`

5. **Output (Escrita do Resultado):**  
   - O resultado final é armazenado em um sistema de arquivos distribuído, como HDFS no Hadoop.

---

## 2. Exemplo Prático com Python

Agora, vamos implementar um exemplo de MapReduce utilizando Python. Como o MapReduce foi projetado para processamento distribuído, vamos simular o comportamento usando a biblioteca `multiprocessing`.

### Exemplo: Contagem de palavras em um conjunto de textos

Vamos seguir a estrutura clássica do MapReduce para contar palavras em um conjunto de documentos.

```python
import multiprocessing
from collections import defaultdict

def map_function(text_chunk):
    """Map: Conta palavras e retorna pares (palavra, 1)."""
    word_count = []
    words = text_chunk.split()
    for word in words:
        word = word.lower().strip(".,!?()[]{}:;")  # Normaliza palavras
        word_count.append((word, 1))
    return word_count

def shuffle_and_sort(mapped_data):
    """Shuffle & Sort: Agrupa palavras iguais."""
    grouped_data = defaultdict(list)
    for word, count in mapped_data:
        grouped_data[word].append(count)
    return grouped_data

def reduce_function(grouped_data):
    """Reduce: Soma as contagens de cada palavra."""
    reduced_data = {}
    for word, counts in grouped_data.items():
        reduced_data[word] = sum(counts)
    return reduced_data

def mapreduce(texts):
    """Executa o pipeline MapReduce em textos fornecidos."""
    pool = multiprocessing.Pool(processes=len(texts))  # Processamento paralelo
    
    # Etapa Map: Processamento paralelo dos textos
    mapped_results = pool.map(map_function, texts)
    pool.close()
    pool.join()
    
    # Flatten da lista de resultados
    mapped_data = [pair for sublist in mapped_results for pair in sublist]
    
    # Etapa Shuffle & Sort
    grouped_data = shuffle_and_sort(mapped_data)
    
    # Etapa Reduce
    reduced_data = reduce_function(grouped_data)
    
    return reduced_data

# Exemplo de entrada: Conjunto de textos
documents = [
    "O céu está azul e bonito. O sol brilha forte!",
    "Hoje o céu está nublado, mas ainda bonito.",
    "O sol está quente e brilhando no céu azul!"
]

# Executando o MapReduce
resultado = mapreduce(documents)

# Exibir resultado
for palavra, contagem in sorted(resultado.items(), key=lambda x: -x[1]):
    print(f"{palavra}: {contagem}")
```

O código acima implementa um sistema básico de MapReduce para contar palavras em um conjunto de textos. Ele:

1. **Divide os textos** e os processa em paralelo.
2. **Mapeia cada palavra** para `(palavra, 1)`.
3. **Agrupa palavras iguais** para facilitar a soma.
4. **Soma as ocorrências** de cada palavra na fase de redução.
5. **Exibe o resultado ordenado** pela frequência das palavras.

Agora, podemos adaptar essa abordagem para outros problemas, como análise de logs ou estatísticas em grandes datasets. Se quiser, podemos testar com um conjunto maior de dados! 🚀
```
