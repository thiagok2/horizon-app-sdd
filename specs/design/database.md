# Modelo de Dados Lógico (Entity Relationship)

Este documento descreve a estrutura lógica de dados para o sistema de agendamento e gestão de filas. O objetivo é garantir uma API generalista que atenda tanto ao App Mobile quanto ao futuro Painel Web.

---

## 1. Entidades de Cadastro (Core)

### **Unidade (Units)**
Representa os consultórios, clínicas ou laboratórios cadastrados.
* **ID:** UUID (PK)
* **Nome:** String
* **Endereco_Completo:** String
* **Latitude:** Decimal (10, 8)
* **Longitude:** Decimal (11, 8)
* **Telefone:** String

### **Especialidade (Specialties)**
Catálogo de serviços oferecidos (ex: Cardiologia, Coleta de Sangue).
* **ID:** UUID (PK)
* **Nome:** String

### **Profissional (Professionals)**
Médicos ou técnicos responsáveis pelos atendimentos.
* **ID:** UUID (PK)
* **Nome:** String
* **CRM_Registro:** String (Unique)
* **Bio:** Text

### **Paciente (Patients)**
Usuários que realizam agendamentos e acompanham a fila.
* **ID:** UUID (PK)
* **Nome:** String
* **Data_Nascimento:** Date
* **Sexo:** Enum (M, F, Outro)
* **Endereco:** String
* **Convenio:** String (Opcional)

---

## 2. Relacionamentos de Configuração

### **Unidade_Profissional (Unit_Professionals)**
*Muitos-para-Muitos: Médicos que atendem em determinadas unidades.*
* **ID_Unidade:** FK
* **ID_Profissional:** FK

### **Profissional_Especialidade (Professional_Specialties)**
*Muitos-para-Muitos: Especialidades de cada médico.*
* **ID_Profissional:** FK
* **ID_Especialidade:** FK

---

## 3. Entidades Operacionais (Agendamento e Fila)

### **Agendamento (Appointments)**
O registro principal da intenção de consulta ou exame.
* **ID:** UUID (PK)
* **ID_Unidade:** FK
* **ID_Paciente:** FK
* **ID_Profissional:** FK
* **ID_Especialidade:** FK
* **Data_Hora_Prevista:** DateTime (Horário agendado)
* **Is_Ordem_Chegada:** Boolean (True se for fila dinâmica, False se for horário fixo)
* **Valor:** Decimal (Para atendimentos particulares)
* **Nota_Fiscal:** String (Opcional)
* **Status:** Enum (Pendente, Confirmado, Cancelado, Realizado, Ausente)
* **Google_Event_ID:** String (Para sincronização externa)

### **Fluxo_Atendimento (Attendance_Tracking)**
Registra os marcos temporais para cálculo de métricas em tempo real.
* **ID:** UUID (PK)
* **ID_Agendamento:** FK (1:1)
* **Check_In_At:** DateTime (Registro de chegada no balcão)
* **Atendimento_Inicio_At:** DateTime (Chamada do médico)
* **Atendimento_Fim_At:** DateTime (Finalização e saída)

---

## 📈 Regras de Relacionamento e Integridade
1.  **Cálculo de Fila:** A posição do paciente na fila é calculada com base nos `Agendamentos` com `Status = Confirmado` e que já possuem `Check_In_At` preenchido, mas não possuem `Atendimento_Inicio_At`.
2.  **Sincronização:** Todo agendamento criado com `Data_Hora_Prevista` deve disparar um evento para a integração com Google Agenda.
3.  **Auditoria:** Todas as tabelas devem conter os campos `created_at` e `updated_at` (padrão Sequelize).