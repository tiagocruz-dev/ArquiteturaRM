# Arquitetura Corporativa – Linha RM

## 1. Introdução

O ecossistema da **Linha RM** é baseado em uma arquitetura **multicamadas (N-Tier)**, permitindo alta escalabilidade, melhor distribuição de carga e isolamento de processamento.

Historicamente, o RM era implantado em ambientes de **1 ou 2 camadas**, onde um único servidor concentrava:

- Banco de dados
- Servidor de aplicação
- Clientes

Com a evolução da plataforma, o RM passou a suportar **arquiteturas distribuídas**, separando:

- Banco de dados
- Servidores de aplicação
- Servidores de processamento (Jobs)
- Clientes Win
- Clientes Web

Hoje a arquitetura recomendada é **N Camadas**, permitindo escalabilidade horizontal e alta disponibilidade.

---

# 2. Visão Arquitetural

## Arquitetura N Camadas
         +----------------------+
         |     Client Win       |
         |      RM.exe          |
         +----------+-----------+
                    |
                    |
         +----------v-----------+
         |    Smart Client      |
         |      WCF TCP/HTTP    |
         +----------+-----------+
                    |
                    |
            +-------v-------+
            |      TGM      |
            | Load Balancer |
            +-------+-------+
                    |
     +--------------+--------------+
     |                             |
+--------v---------+ +--------v---------+
| Application Host | | Application Host |
| RM.Host.exe | | RM.Host.exe |
+--------+---------+ +--------+---------+
| |
+--------------+--------------+
|
+-------v-------+
| Job Servers |
| Background |
+-------+-------+
|
+-------v-------+
| Banco de Dados|
| SQL/Oracle |
+---------------+

---

# 3. Componentes da Arquitetura

## 3.1 Banco de Dados

O banco de dados é responsável pelo armazenamento e gerenciamento das informações do sistema.

### Bancos Suportados

| Banco | Versões |
|------|---------|
| SQL Server | 2016 até 2022 |
| Oracle | 18c e 19c |

### Responsabilidades

- Armazenamento de dados
- Integridade transacional
- Execução de consultas
- Controle de concorrência

---

## 3.2 Servidor de Aplicação (Application Server)

O **Servidor de Aplicação (RM.Host.exe)** é responsável pelo processamento das regras de negócio e atendimento das requisições dos clientes.

### Responsabilidades

- Processamento de telas
- Regras de negócio
- Comunicação com banco
- Execução de serviços

### Execução Paralela

O Host permite configurar múltiplos serviços simultâneos.

Benefícios:

- Processamento paralelo
- Redução de tempo de resposta
- Melhor uso de CPU

### Dimensionamento

A quantidade ideal depende de:

- Número de usuários simultâneos
- Capacidade de CPU
- Memória disponível
- Tipo de operações realizadas

---

## 3.3 Balanceamento de Carga

### Balanceamento Nativo RM

O RM permite configurar múltiplos servidores de aplicação.

Benefícios:

- Distribuição de carga
- Melhor performance
- Redundância

---

## 3.4 TOTVS Gateway Manager (TGM)

O **TOTVS Gateway Manager (TGM)** atua como um **ponto único de acesso** para os servidores de aplicação.

### Funções

- Balanceamento de carga
- Monitoramento de hosts
- Redirecionamento de requisições
- Alta disponibilidade

### Fluxo

### Benefícios

- Alta disponibilidade
- Failover automático
- Escalabilidade horizontal

---

# 4. Job Server

## 4.1 Visão Geral

O **Job Server** é responsável pela execução de processos em background.

Esses processos incluem:

- Relatórios
- Integrações
- Rotinas batch
- Processos agendados

O Job Server executa como um serviço do Host, porém é habilitado apenas nos servidores dedicados a Jobs.

---

## 4.2 Funcionamento

Os processos submetidos são registrados em tabelas do banco.

### Tabelas

| Tabela | Função |
|-------|--------|
| GJOBX | Registro dos processos |
| GJOBXEXECUCAO | Controle da fila |

Fluxo:

---

## 4.3 Vantagens

- Isolamento de carga pesada
- Melhor performance dos Hosts
- Melhor aproveitamento de hardware
- Facilidade de troubleshooting

---

# 5. Afinidade de Processos

O Cockpit permite definir afinidade de execução de Jobs.

Quando configurado:

- O Job Server executa apenas processos compatíveis.

### Exemplo

Servidor Job 1:

- Executa relatórios
- Executa processos gerais

Servidor Job 2:

- Executa processos gerais
- Não executa relatórios

---

# 6. Fracionamento de Processos

O RM permite dividir processos em lotes menores.

Benefícios:

- Execução paralela
- Melhor uso de CPU
- Redução de tempo total

---

# 7. Camada de Clientes

## 7.1 Client Win

Componentes:

- RM.exe
- Aplicações legadas

Responsável pela interface gráfica.

Nesta camada existem apenas:

- Formulários
- Interface do usuário

---

## 7.2 Smart Client

O Smart Client utiliza o mesmo executável RM.exe.

### Comunicação

Protocolos:

- WCF TCP
- WCF HTTP

### Configuração

Durante a instalação:

Informar:

- Servidor de aplicação
- Porta (padrão 8050)

---

# 8. TOTVS Update Server

## 8.1 Visão Geral

O TOTVS Update Server permite atualização automática do ambiente.

Pode ser instalado:

- Em servidor dedicado
- Em servidor de aplicação

---

## 8.2 Funcionamento

Etapas:

1 Atualização manual do servidor de atualização  
2 Reinício do Host  
3 Disponibilização dos arquivos  
4 Atualização automática dos clientes

---

## 8.3 Utilização

Utilizado para:

- Atualização de Releases
- Atualização de Patches

Exemplos:

Release:
12.1.12 -> 12.1.13

Patch:
12.1.12.111 -> 12.1.12.112

---

# 9. Client Web

A instalação padrão inclui:

- Corpore.NET (portal legado)
- FrameHTML (novos portais)

Responsável pelo acesso Web ao RM.

---

# 10. Topologias Recomendadas

## 10.1 Pequeno Porte

Até 50 usuários
1 Servidor Banco
1 Servidor Host + Job
Clients

---

## 10.2 Médio Porte

50 a 200 usuários
1 Banco
2 Hosts
1 Job Server
Clients

---

## 10.3 Grande Porte

Acima de 200 usuários

---

## 10.3 Grande Porte

Acima de 200 usuários
Cluster Banco
2+ Hosts
2+ Job Servers
TGM
Clients

---

# 11. Boas Práticas

## Application Server

Recomendações:

- CPU alta frequência
- SSD/NVMe
- 32GB+ RAM

Evitar:

- Jobs pesados no Host
- Banco no mesmo servidor

---

## Job Server

Recomendações:

- Alto número de cores
- 32GB+ RAM

Ideal para:

- Relatórios grandes
- Integrações
- Processos batch

---

## Banco de Dados

Recomendações:

- Storage NVMe ou SSD Enterprise
- Alta memória
- Backup automatizado

---

# 12. Portabilidade entre Versões

A cada Release é disponibilizado um documento de portabilidade contendo:

- Sistemas operacionais suportados
- Bancos compatíveis
- Requisitos mínimos
- Plataformas suportadas

---

# 13. Benefícios da Arquitetura N Camadas

## Escalabilidade

Permite crescimento horizontal.

## Performance

Distribuição de carga entre servidores.

## Alta Disponibilidade

Redundância de Hosts.

## Isolamento

Separação de:

- Banco
- Aplicação
- Processamento

## Manutenibilidade

Facilidade de expansão e manutenção.
