# AWS Cluster Instance Selector

Uma ferramenta em Python para otimizar a seleção de instâncias EC2 da AWS baseada em análise de custo e desempenho para clusters computacionais.

## 📋 Descrição

Este projeto implementa um seletor inteligente de instâncias AWS que analisa diferentes tipos de máquinas virtuais em múltiplas regiões, considerando:

- **Preços On-Demand e Spot**: Consulta a API de Pricing da AWS em tempo real
- **Makespan (Tempo de Execução)**: Tempo estimado para completar a carga de trabalho
- **Checkpoints**: Overhead de tempo para salvar estado em instâncias Spot
- **Número de Nós**: Suporte para clusters com múltiplos nós
- **Múltiplas Regiões**: Análise comparativa entre diferentes regiões AWS

O algoritmo seleciona as três melhores opções de instâncias com base na relação custo-benefício, considerando o tempo total de execução com possíveis interrupções.

## 🚀 Funcionalidades

- ✅ Integração com AWS Pricing API para obter preços em tempo real
- ✅ Suporte a múltiplos tipos de instâncias EC2
- ✅ Análise de instâncias On-Demand e Spot
- ✅ Cálculo de custo ajustado para múltiplos nós
- ✅ Estimativa de custo para checkpoints em instâncias Spot
- ✅ Seleção de top 3 melhores opções
- ✅ Logging detalhado dos resultados

## 📦 Estrutura do Projeto

```
aws-cluster-instance-selector/
├── creator.py                 # Script principal
├── conf.json                  # Arquivo de configuração
├── LICENSE                    # Licença do projeto
├── README                     # Este arquivo
└── src/
    ├── classes/
    │   ├── cluster_creator.py # Lógica de seleção de instâncias
    │   ├── instance.py        # Modelo de dados da instância
    │   └── pricing.py         # Integração com AWS Pricing API
    └── models/
        └── instance_market.py # Enum para tipos de mercado
```

## 🛠️ Pré-requisitos

- Python 3.7+
- Credenciais AWS configuradas (para acessar a AWS Pricing API)
- Bibliotecas: `boto3`

## 📝 Configuração

O projeto utiliza um arquivo `conf.json` para definir os parâmetros de entrada.

### Exemplo de Configuração

```json
{
  "regions": ["us-east-1", "us-east-2", "us-west-1", "us-west-2", "sa-east-1"],
  "instances": [
    {
      "name": "c6a.12xlarge",
      "makespan": 779.6
    },
    {
      "name": "hpc7a.12xlarge",
      "makespan": 480.8,
      "num_nodes": 4
    }
  ],
  "num_checkpoints": 24,
  "avg_time_checkpoint": 3.88,
  "num_nodes": 4
}
```

### Parâmetros de Configuração

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `regions` | Array | Lista de regiões AWS a analisar (ex: us-east-1, sa-east-1) |
| `instances` | Array | Lista de instâncias EC2 a avaliar |
| `instances[].name` | String | Nome da instância (ex: c6a.12xlarge) |
| `instances[].makespan` | Float | Tempo estimado de execução em segundos |
| `instances[].num_nodes` | Integer | (Opcional) Número de nós para a instância. Padrão: `num_nodes` global |
| `num_checkpoints` | Integer | Número de checkpoints para instâncias Spot |
| `avg_time_checkpoint` | Float | Tempo médio de cada checkpoint em segundos |
| `num_nodes` | Integer | (Opcional) Número padrão de nós do cluster. Padrão: 1 |

## 🚀 Como Executar

```bash
python creator.py conf.json
```

O script irá:
1. Carregar a configuração do arquivo JSON
2. Consultar os preços (On-Demand e Spot) para cada instância em cada região
3. Calcular o custo total considerando o makespan
4. Selecionar as 3 melhores opções
5. Gerar logs com os resultados

## 📊 Como Funciona o Algoritmo

### Cálculo de Custo

Para cada instância, o custo total é calculado como:

```
custo_total = (preço_horário × (makespan / 3600)) × num_nós
```

### Ajuste para Instâncias Spot

Instâncias Spot podem ser interrompidas, portanto o makespan é ajustado:

```
makespan_estimado = (num_checkpoints × avg_time_checkpoint) + makespan_original
```

O algoritmo seleciona as três instâncias com o menor custo total estimado.

## 📚 Classes Principais

### `ClusterCreator`
Gerencia a criação e seleção de instâncias otimizadas.

**Métodos principais:**
- `select_best_instances()`: Retorna as 3 melhores opções de instância
- `create_log()`: Gera logs dos resultados

### `Instance`
Representa uma instância EC2 com seus atributos.

**Atributos:**
- `name`: Nome da instância
- `makespan`: Tempo de execução em segundos
- `cost`: Preço por hora em USD
- `market`: Tipo de mercado (ONDEMAND ou SPOT)
- `region`: Região AWS
- `num_nodes`: Número de nós

### `Pricing`
Consulta a AWS Pricing API para obter preços atualizados.

**Métodos principais:**
- `get_ondemand_instance_price()`: Obtém preço On-Demand
- `get_spot_instance_price()`: Obtém preço Spot

## ⚙️ Requisitos de AWS

Para executar este projeto, você precisa:

1. **Credenciais AWS** configuradas localmente (via `~/.aws/credentials` ou variáveis de ambiente)
2. **Permissão IAM** para acessar o serviço `pricing` da AWS
3. **Política IAM recomendada:**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "pricing:GetProducts",
         "Resource": "*"
       }
     ]
   }
   ```
