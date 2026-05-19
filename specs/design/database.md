# Modelo de Dados Lógico (Entity Relationship)

Este documento descreve a estrutura lógica de dados para o sistema de agendamento e gestão de filas. O objetivo é garantir uma API generalista que atenda tanto ao App Mobile quanto ao futuro Painel Web.

---

## 1. Entidades de Cadastro (Core)

### **Unidade **
Representa os consultórios, clínicas ou laboratórios cadastrados.
* **ID:** UUID (PK)
* **Nome:** String
* **End_Logradouro:** String
* **End_Numero:** String
* **End_Complemento:** String
* **End_Bairro:** String
* **End_Cidade:** String
* **End_Estado:** String
* **End_CEP:** String
* **Latitude:** Decimal (10, 8)
* **Longitude:** Decimal (11, 8)
* **Telefone:** String

### **Especialidade **
Catálogo de serviços oferecidos (ex: Cardiologia, Coleta de Sangue).
* **ID:** UUID (PK)
* **Nome:** String

### **Usuário **
Usuários 
* **ID:** UUID (PK)
* **Nome:** String
* **Login:** String(50) NOT NULL, UNIQUE
* **Senha_Usuario:** String NOT NULL
* **Email:** NOT NULL UNIQUE
* **Data_Nascimento:** Date
* **Sexo:** Enum (M, F, Outro)
* **End_Logradouro:** String
* **End_Numero:** String
* **End_Complemento:** String
* **End_Bairro:** String
* **End_Cidade:** String
* **End_Estado:** String
* **End_CEP:** String

### **Paciente **
* **ID_Usuario:** UUID (FK)
* **ID_Convenio:** UUID (FK, Opcional) — Aponta para a tabela Convenio
* **Num_Carteirinha:** String (Opcional)
* **Bio:** String

### **Operador **
* **ID_Usuario:** UUID (FK)
* **Data_Inicio:** Date
* **Data_Fim:** Date
* **Bio:** String

### **Profissional **
Médicos ou técnicos responsáveis pelos atendimentos.
* **ID_Usuario:** (FK)
* **Registro_Profissional:** String (Unique)
* **ID_Classe_Profissional:** (FK)
* **Data_Inicio:** Date
* **Data_Fim:** Date
* **Ativo:** Boolean
* **Bio:** Text

### **Convenio**
* **ID:** UUID (PK)
* **Nome_Fantasia:** String
* **Telefone_Suporte:** String
* **Is_Ativo:** Boolean

### **Classe_profissional**
* **ID:** UUID (PK)
* **Nome:** String (ex: médico, dentista, piscicólogo)

---

## 2. Relacionamentos de Configuração

### **Unidade_Profissional**
*Muitos-para-Muitos: Médicos que atendem em determinadas unidades.*
* **ID_Unidade:** FK
* **ID_Profissional:** FK

### **Unidade_Operador**
* **ID_Unidade:** FK
* **ID_Operador:** FK

### **Unidade_Paciente**
* **ID_Unidade:** FK
* **ID_Paciente:** FK

### **Profissional_Especialidade (Professional_Specialties)**
*Muitos-para-Muitos: Especialidades de cada médico.*
* **ID_Profissional:** FK
* **ID_Especialidade:** FK

### **Convenio_Unidade**
*Muitos-para-Muitos: Convenios que unidade atende
* **ID_Convenio:** (FK)
* **ID_Unidade:** (FK)
* **Is_Ativo:** Boolean (Permite desativar um convênio em uma unidade específica sem afetar as outras)
---

## 3. Entidades Operacionais (Agendamento e Fila)

### **Agendamento (Appointments)**
O registro principal da intenção de consulta ou exame.
* **ID:** UUID (PK)
* **ID_Unidade:** FK
* **ID_Paciente:** FK
* **ID_Profissional:** FK
* **ID_Especialidade:** FK
* **ID_Expediente:** UUID (FK) — referência ao `Expediente de trabalho` quando aplicável

* **Data_Hora_Prevista:** DateTime (Horário agendado)
* **Is_Ordem_Chegada:** Boolean (True se for fila dinâmica, False se for horário fixo)
* **Valor:** Decimal (Para atendimentos particulares)
* **Nota_Fiscal:** String (Opcional)
* **Status:** Enum (Pendente, Confirmado, Cancelado, Realizado, Ausente)
* **Google_Event_ID:** String (Para sincronização externa)
* **Atendimento_Inicio_At:** DateTime  
* **Atendimento_Fim_At:** DateTime 

### **Expediente de trabalho**
* **ID:** UUID (PK)
* **Data_Expediente:** Date
* **ID_Profissional:** UUID (FK)
* **ID_Unidade:** UUID (FK)
* **Expediente_Inicio:** DateTime
* **Expediente_Fim:** DateTime
* **Is_Ordem_Chegada:** Boolean (Default: false) — flag que indica se o expediente usa fila por ordem de chegada
* **Quantidade_Max_Atendimentos:** Integer (Opcional) — limite total de atendimentos no expediente
* **Quantidade_Max_Atendimentos_Por_Convenio:** Integer (Opcional) — limite por convênio
* **created_at / updated_at:** DateTime (auditoria)
---

## 📈 Regras de Relacionamento e Integridade
1.  **Cálculo de Fila:** A posição do paciente na fila é calculada com base nos `Agendamentos` com `Status = Confirmado` e que já possuem `Check_In_At` preenchido, mas não possuem `Atendimento_Inicio_At`.
2.  **Sincronização:** Todo agendamento criado com `Data_Hora_Prevista` deve disparar um evento para a integração com Google Agenda.
3.  **Auditoria:** Todas as tabelas devem conter os campos `created_at` e `updated_at` (padrão Sequelize).