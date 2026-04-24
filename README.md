# 🚀 PintOS: Gestão de Threads - Alarm Clock & Advanced Scheduler

Este repositório contém a minha implementação das funcionalidades do núcleo do PintOS. O PintOS é um sistema operativo didático escrito em C, desenhado para correr numa arquitetura x86 de 32-bit. O foco principal deste projeto recai sobre a otimização da gestão e escalonamento de *threads*. Este projeto abrange a criação de um sistema de temporização eficiente (Alarm Clock) e de um escalonador avançado baseado em filas de retroalimentação multinível (Advanced Scheduler ou MLFQS).

## 🛠️ Tecnologias e Ferramentas
* **Linguagem:** C
* **Ambiente / Sistema Operativo Alvo:** PintOS (x86, 32-bit)
* **Simuladores de Hardware:** Bochs ou QEMU
* **Conceitos Aplicados:** Interrupções de Temporizador, Estados de Thread, Escalonamento de Processos, e MLFQS (Multi-Level Feedback Queue Scheduler).

## 📋 Funcionalidades Implementadas

### ⏰ 1. Alarm Clock (Temporizador Eficiente)
A implementação original da função `timer_sleep()` utilizava um mecanismo ineficiente conhecido como "espera ativa" (*busy waiting*). Neste formato original, a *thread* executava um ciclo contínuo, a verificar repetidamente o tempo atual até que o período estipulado terminasse.

* **A Minha Solução:** O código foi modificado de modo a remover completamente a espera ativa do sistema.
* Quando a função `timer_sleep()` é invocada, o sistema verifica se ainda falta tempo para o término do intervalo de espera. Se o tempo ainda não tiver esgotado, a *thread* é removida da lista de prontidão (`ready_list`) e inserida numa fila de adormecidas (*sleep queue*).
* **Mecanismo de Despertar:** Durante cada interrupção do temporizador (`timer_interrupt()`), o núcleo verifica que *threads* necessitam de ser acordadas. As *threads* cujo tempo de bloqueio terminou são retiradas da fila de adormecidas, e o seu estado é alterado novamente para "pronto", sendo por fim inseridas de volta na `ready_list`.

### 🧠 2. Advanced Scheduler (Escalonador MLFQS)
Com o objetivo de reduzir o tempo médio de resposta e garantir uma partilha justa do processador, foi implementado um Escalonador de Filas de Retroalimentação Multinível (MLFQS), semelhante ao modelo adotado no sistema operativo 4.4BSD.

* **Estrutura de Filas:** O escalonador mantém e gere 64 filas de prontidão diferentes, correspondentes a níveis de prioridade numerados de 0 até 63.
* O escalonador escolhe ininterruptamente para execução a *thread* que se encontra na fila não vazia com a prioridade mais elevada.
* **Cálculos Dinâmicos de Prioridade:** A prioridade atribuída a cada *thread* é recalculada de forma dinâmica utilizando as seguintes variáveis:
  * `niceness` (valor *nice*): Uma variável com valores de -20 a 20. Valores mais elevados indicam que a *thread* está disposta a ceder tempo de CPU a outras.
  * `recent_cpu`: Uma medida ponderada que contabiliza o tempo recente de CPU consumido por cada processo (tempos mais recentes possuem um peso maior).
  * `load_avg`: Uma estimativa do número de *threads* preparadas para executar no sistema, atualizada rigorosamente sempre que o contador de *ticks* atinge um múltiplo de um segundo.

## 🚀 Como Compilar e Executar

1. Encontrar o diretório das *threads*:
A compilação e execução (`make`) devem ser feitas dentro da pasta específica das *threads* do projeto.
**cd pintos/src/threads**

2. Executar os testes do Alarm Clock e MLFQS:
A compilação e execução (`make check`) dentro da mesma pasta.
