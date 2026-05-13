# Project Horizon: Sistema de Agendamento e Gestão de Filas

## 🎯 Visão Geral
Plataforma de agendamento e gestão de fluxo de pacientes, focada em transparência e redução da percepção de espera. O sistema une a conveniência do agendamento prévio com a agilidade do atendimento por ordem de chegada.

## 🚀 Objetivos do Projeto
1.  **Eficiência Operacional:** Digitalizar o check-in e check-out, eliminando fichas de papel e processos manuais de contagem de fila.
2.  **Experiência do Paciente:** Reduzir a ansiedade da espera permitindo que o paciente monitore sua posição na fila pelo celular, podendo chegar ao local apenas próximo do atendimento.
3.  **Dados Estratégicos:** Gerar métricas de tempo médio de consulta, taxa de absenteísmo (faltas) e picos de demanda para os consultórios.
4.  **Interoperabilidade:** Garantir que o agendamento reflita na agenda pessoal do usuário (Google Calendar) e que a API suporte múltiplos clientes (Mobile agora, Web depois).

## 👥 Personas e Contextos
- **Paciente:** Usuário do App Mobile. Busca facilidade para agendar e previsibilidade no dia da consulta.
- **Consultório (Recepcionista/Médico):** Usuários do Painel de Gestão. Precisam de uma visão clara de quem já chegou, quem está em atendimento e quem é o próximo.

## 🛠 Stack Tecnológica (Alinhada)
- **Frontend Mobile:** React Native (Expo).
- **Backend:** Node.js (Express).
- **Database:** PostgreSQL (Sequelize ORM).
- **Geolocalização:** Armazenamento via coordenadas decimais (Lat/Log).

## 📂 Estrutura de Especificações (SDD)
- `/specs/requirements/`: Documentos de Regras de Negócio e Casos de Uso.
- `/specs/design/`: Diagramas de Arquitetura, **Modelo de Dados (ER)** e Contratos de API (Swagger/OpenAPI).
- `/specs/tasks/`: Lista de tarefas técnicas para implementação (Backlog).

## 🚩 Premissas de Desenvolvimento
1.  **Arquitetura Generalista:** A API deve ser agnóstica em relação ao cliente.
2.  **Consistência de Status:** Um agendamento possui um ciclo de vida rígido (Agendado -> Em Espera -> Em Atendimento -> Finalizado/Cancelado).
3.  **Localização:** Unidades de saúde são georeferenciadas para futuras buscas por proximidade.