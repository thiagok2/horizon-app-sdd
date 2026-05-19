\1. Criar a entidade convênio(geap, unimed, hapvida e etc). Criar uma associação entre essa nova entidade e unidade. Uma relação de muitos para muitos. 

\2. Criar a entidade classe profissional(médico, dentista, piscicólogo e etc). A classe profissinal deve estar associada a entidade profissional;

3. Criar uma tabela usuário que unifica as informações comuns dos profissionais, pacientes e operadores do sistema. Assim, deve-se haver essas 4 entidades. A usuário, a profissional, paciente e a operador, sendo as ultimas 3 com chave estrageira para usuário.
Lembrando que dados comuns como nome, endereço, login, senha(criptografada), email, enfim, dados comuns ficam na usuário; Enquanto, a paciente tem por exemplo o convênio associado ao paciente, operador tem data de início e horário de trabalho, médico tem crm. Todos devem ter um campo distinto de texto da bio.
Todas as 3 relacioandas devem estar associados a uma unidade;


4. Criar a entidade expediente de trabalho, que relaciona uma data, um profissional, a unidade, horário de inicio e fim, flag de ordem de chegada, quantidade máxima de atendimentos, quantidade máxima de atendimentos por convenio;

5. Associar a agendamento ao expediente de trabalho, assim simplifica as chaves
presentes no agendamento.

6. Unificar Agendamento e Fluxo_Atendimento em uma única entidade Agendamento;
