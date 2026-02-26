# Arquitetura em Camadas – Linha RM

## Visão Geral

O ecossistema da **Linha RM** é arquitetado utilizando o modelo de **aplicações em camadas**, popularmente conhecido como **arquitetura 3 camadas** ou **N camadas**.

Inicialmente, era comum que os clientes utilizassem o sistema em **1 ou 2 camadas**, onde um único servidor era responsável por:

- Banco de dados
- Servidor de aplicação
- Clientes RM

Com a evolução tecnológica, a arquitetura foi descentralizada, permitindo a separação entre:

- Banco de Dados
- Servidor de Aplicação (`RM.Host.exe`)
- Clientes (`RM.exe`)

Esse modelo passou a ser conhecido como **arquitetura 3 camadas**.

Atualmente, o sistema suporta **arquitetura em N camadas**, permitindo maior escalabilidade, isolamento de processamento e melhor aproveitamento de recursos.

Além disso, novos recursos foram implementados para permitir maior independência no processamento das requisições dos diferentes tipos de clientes (**Windows e Web**).

---

# Componentes da Arquitetura

## Banco de Dados

O RM suporta atualmente os seguintes bancos de dados:

- **SQL Server**
  - Versões suportadas: 2016 até 2022

- **Oracle**
  - Versões suportadas: 18c e 19c

O banco de dados é responsável pelo armazenamento e gerenciamento de todas as informações do sistema.

---

## Servidor de Aplicação (Application Server)

O **Servidor de Aplicação** é responsável pelo processamento das requisições realizadas pelos clientes.

Funções principais:

- Processar operações solicitadas pelas telas
- Executar regras de negócio
- Comunicar-se com o banco de dados
- Retornar resultados aos clientes

O servidor de aplicação pode ser distribuído em múltiplos hosts e integrado a um balanceador de carga como o **TGM**.

### Serviços do Host

É possível definir a quantidade de serviços que serão executados simultaneamente pelo servidor de aplicação.

As requisições de tela são distribuídas entre esses serviços, permitindo:

- Melhor performance
- Processamento paralelo
- Maior escalabilidade

A quantidade ideal de hosts depende do hardware disponível.

---

## Job Server

O **Job Server** é responsável pela execução de **processos agendados (Jobs)** de forma independente.

Características:

- Comunicação direta com o banco de dados
- Execução em background
- Serviço executado pelo Host
- Habilitado apenas em servidores dedicados a Jobs na arquitetura N camadas

Os processos submetidos pelos aplicativos entram em uma fila de execução.

### Tabelas Utilizadas

- **GJOBX**  
  Tabela onde são registrados os processos.

- **GJOBXEXECUCAO**  
  Tabela responsável pelo controle da fila de execução.

### Vantagens

- Melhor aproveitamento de hardware
- Isolamento de processos pesados
- Facilidade na identificação de problemas
- Melhor previsibilidade de execução

---

## Afinidade de Processos

É possível configurar afinidade de processos através do **Cockpit RM**.

A afinidade permite que um Job Server seja responsável pela execução exclusiva de determinados processos.

Se configurada, o Job Server executará apenas os processos compatíveis com sua afinidade.

### Exemplo

- Servidor de Job 1:
  - Executa o processo **Executar Relatório**
  - Executa também os demais processos

- Servidor de Job 2:
  - Executa todos os processos
  - Exceto **Executar Relatório**

---

## Fracionamento de Processos

O RM permite o **fracionamento de Jobs** em lotes menores.

Benefícios:

- Processamento paralelo
- Melhor aproveitamento de CPU
- Redução do tempo total de execução

---

# Camada de Clientes

## Client Win

O **Client Win** inclui:

- RM.exe
- Aplicações legadas

Responsável pela interface do usuário e abertura dos formulários.

Nesta camada ficam apenas os formulários e interfaces gráficas.

---

## Smart Client

Documentação oficial:

https://tdn.totvs.com/display/LRM/Smart+Client+RM

O **Smart Client RM** utiliza o mesmo executável **RM.exe**, utilizado em ambientes locais ou em três camadas.

Comunicação com o servidor:

- Protocolo WCF
- TCP ou HTTP

Durante a instalação da Biblioteca RM em cada cliente deve-se selecionar:

Será necessário informar:

- Servidor de aplicação
- Porta de comunicação (padrão 8050)

Documentação:

https://tdn.totvs.com.br/display/public/LRM/Configurando+o+Smart+Client+RM+e+TOTVS+Update

---

# TOTVS Update Server (Opcional)

O **TOTVS Update Server** é responsável pela atualização automática do ambiente.

Pode ser instalado:

- No próprio servidor de aplicação
- Ou em um servidor dedicado

O servidor de atualização deve possuir uma **instalação Server do RM**.

## Função

- Atualização automática de Clients (Smart Client)
- Atualização automática do Host
- Replicação de versões

Exemplos:

- Atualização de Release  
  12.1.12 → 12.1.13

- Atualização de Patch  
  12.1.12.111 → 12.1.12.112

> O TOTVS Update atualiza o ambiente enquanto o **WsUpdate** é utilizado para instalação via HTTP.

## Funcionamento

1. O servidor de atualização é atualizado manualmente via instalador RM
2. Os serviços Host são reiniciados
3. Os arquivos ficam disponíveis para distribuição automática
4. Os clientes recebem atualização automaticamente

## Requisito

Para configurar o **Smart Client**, o TOTVS Update deve estar configurado, pois ambos compartilham o mesmo servidor de atualização.

---

## Balanceamento Nativo

O RM permite configurar múltiplos servidores de aplicação para um mesmo cliente.

Benefícios:

- Distribuição de carga
- Melhor desempenho
- Alta disponibilidade

---

# TOTVS Gateway Manager (TGM)

O **TOTVS Gateway Manager (TGM)** é um centralizador de requisições que atua como **ponto único de acesso**.

Funções:

- Análise de requisições
- Filtragem de tráfego
- Redirecionamento para hosts ativos
- Balanceamento de carga

---

# Client Web

A instalação padrão inclui:

- Estrutura do **Corpore.NET** (Portal legado)
- Estrutura **FrameHTML** (Novos portais)

Utilizado pelos clientes Web do RM.

---

# Portabilidade entre Versões

Documentação:

https://tdn.totvs.com/x/6DzuPQ

A cada Release é disponibilizado um documento de portabilidade contendo:

- Sistemas operacionais suportados
- Bancos de dados compatíveis
- Requisitos do ambiente
- Plataformas suportadas

---

# Arquitetura N Camadas – Resumo

Uma arquitetura típica pode conter:

- 1 ou mais servidores de banco de dados
- 1 ou mais servidores de aplicação
- 1 ou mais servidores de Job
- Balanceadores de carga
- Clientes Windows
- Clientes Web

Benefícios:

- Escalabilidade
- Alta disponibilidade
- Melhor performance
- Isolamento de carga
- Flexibilidade de implantação
