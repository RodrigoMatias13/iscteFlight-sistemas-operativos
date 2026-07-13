# IscteFlight — Sistema de Gestão de Voos

Este repositório contém o projeto prático desenvolvido no âmbito da unidade curricular de **Sistemas Operativos** (Ano Letivo 2023/2024) no **Iscte - Instituto Universitário de Lisboa**.

O **IscteFlight** é uma plataforma totalmente automatizada que simula o fluxo completo de gestão, reserva, check-in e escolha de lugares num voo comercial, dividida em três fases incrementais.

---

## 📁 Estrutura do Projeto e Funcionalidades

### PARTE 1 — Administração e Compra de Bilhetes (Bash)
Desenvolvimento de scripts de administração em **Shell Script (Bash)** para gerir a base de dados em texto simples (`passageiros.txt` e `voos.txt`).
*   **`regista_passageiro.sh`**: Registo e validação de passageiros (NIF, email automático, encriptação/verificação de senha) e carregamento de saldo.
*   **`compra_bilhete.sh`**: Pesquisa interativa de voos com lugares disponíveis, login de utilizador, validação de saldo, compra de bilhete com atualização do inventário e registo histórico em `relatorio_reservas.txt`.
*   **`estado_voos.sh`**: Geração automática de relatórios de voos ocupados em formato HTML (`voos_disponiveis.html`) e agendamento via **Cron Job** (`cron.def`).
*   **`stats.sh`**: Estatísticas de vendas e ranking de voos mais rentáveis.
*   **`menu.sh`**: Interface de consola interativa para centralizar a execução de todos os módulos.

### PARTE 2 — Check-in Eletrónico (C: Processos, Sinais e FIFOs)
Simulação de um servidor de sessões de check-in utilizando a arquitetura Cliente-Servidor concorrente em **C**.
*   **Comunicação via FIFO**: Utilização de um named pipe público (`server.fifo`) para recepção síncrona de pedidos do Cliente.
*   **Concorrência Multiprocesso**: O processo Servidor principal recebe os pedidos e cria processos filhos (**Servidores Dedicados**) via `fork()` para processar individualmente cada check-in na base de dados binária `bd_passageiros.dat`.
*   **Tratamento de Sinais**: Gestão robusta de sinais POSIX (`SIGINT` para shutdown ordenado, `SIGCHLD` para reciclagem de processos filho pelo pai, `SIGUSR1` e `SIGHUP` para sincronização de estados de check-in, e `SIGALRM` para tratamento de timeouts no cliente).

### PARTE 3 — Escolha de Lugar (C: IPC - Shared Memory, Semaphores e Message Queues)
Evolução do sistema cliente-servidor em **C** para suportar a atribuição concorrente e segura de lugares físicos no avião utilizando mecanismos de IPC (*Inter-Process Communication*).
*   **Memória Partilhada (SHM)**: Alocação de memória partilhada estruturada (`DadosServidor`) para manter as listas de passageiros e voos em RAM.
*   **Semáforos (SEM)**: Implementação de exclusão mútua (`SEM_PASSAGEIROS` e `SEM_VOOS`) para prevenir condições de corrida (*race conditions*) quando múltiplos utilizadores escolhem o mesmo lugar concorrentemente, além de um semáforo de sincronização estilo "barreira" (`SEM_NR_SRV_DEDICADOS`) para garantir um encerramento limpo do servidor.
*   **Filas de Mensagens (MSG)**: Canal bidirecional estruturado de troca de mensagens segmentadas por tipo (usando os PIDs dos processos) para guiar o fluxo de autenticação e seleção de lugares.

---

## 🛠️ Tecnologias e Ferramentas

*   **Linguagens:** Bash Shell Scripting, Linguagem C (C99 / POSIX)
*   **IPC & Concorrência:** `fork()`, `wait()`, Sinais UNIX, Named Pipes (FIFOs), IPC Shared Memory (`shmget`), IPC Semaphores (`semget`), IPC Message Queues (`msgget`)
*   **Ambiente de Desenvolvimento:** Linux/Unix (Servidor Tigre)
*   **Utilitários de Texto:** `grep`, `awk`, `sed`

---

## 🚀 Como Executar

### Parte 1 (Bash)
1. Garanta que os scripts têm permissão de execução:
   ```bash
   chmod +x *.sh
   ```
2. Execute o menu interativo:
   ```bash
   ./menu.sh
   ```
### Parte 2 e 3 (C)

1. Compile os módulos utilizando o compilador gcc (ou o Makefile fornecido na baseline):

   ```bash
   gcc -Wall servidor.c -o servidor
   gcc -Wall cliente.c -o cliente
   ```
   
2. Execute o servidor numa janela de terminal:

   ```bash
   ./servidor
   ```

3. Noutro terminal, execute o cliente correspondente para interagir:

   ```bash
   ./cliente
   ```

👤 Autor
Rodrigo Matias
