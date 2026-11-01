# Mentorship Program

ServiceNow scoped application to manage mentorship sessions between mentors and mentees, built with App Engine Studio and Flow Designer.

Aplicativo escopado do ServiceNow para acompanhar sessões de mentoria entre mentores e mentorados. Desenvolvido como atividade do Bootcamp Shark Academy (ServiceNow).

## Visão geral

O app permite cadastrar mentores, registrar sessões de mentoria e automatizar o ciclo de aprovação da sessão por meio de um fluxo no Flow Designer.

## Tabelas

### Mentor
Criada do zero (não estende outra tabela), com auto-number.

| Campo | Tipo | Observação |
|---|---|---|
| Name | Reference (User) | |
| Biography | String | Tamanho máximo 1000 |
| Expertise area | Choice | Financial, Health, IT, Marketing, Other |
| Available | True/False | |
| Concluded Sessions | Integer | Valor padrão 0 |

### Mentorship Session
Estende a tabela **Task** (herda campos como Number, State, Short description, Work notes), com auto-number. Campos exclusivos:

| Campo | Tipo | Observação |
|---|---|---|
| Mentor's Name | Reference (Mentor) | |
| Session Date | Date/Time | |
| Session Feedback | HTML | |

### Campo State (Dictionary Override)
Opções: **Draft**, **Awaiting approval**, **Accepted**, **Rejected/Canceled** e **Done**. As opções padrão herdadas da Task foram desativadas e o valor padrão é **Draft**, configurado por Dictionary Override.

## Navegação

Application menu **Mentorship Program**, com os grupos **Mentorship Session** e **Mentor**, cada um com os módulos **Create New** e **List**.

## Flow: Mentorship Session Flow

Executa como **System User** e é disparado quando uma sessão é criada.

1. Atualiza o State para **Awaiting approval**.
2. Envia uma solicitação de aprovação ao mentor informado em *Mentor's Name*.
3. **Se aprovado:**
   1. State passa para **Accepted**.
   2. Aguarda o preenchimento de **Session Feedback** (Wait For Condition).
   3. State passa para **Done**.
   4. Soma 1 em **Concluded Sessions** do mentor responsável, usando um script.
4. **Se rejeitado:** State passa para **Rejected/Canceled** e o fluxo termina.

## Recursos utilizados

- App Engine Studio e Studio
- Tabela estendida (Task) e tabela criada do zero
- Auto-number
- Dictionary Override
- Application menu e modules
- Flow Designer (Ask For Approval, If/Else, Wait For Condition, Update Record, script com `fd_data`)

## Como importar o app

O arquivo XML deste repositório é um update set exportado da instância.

1. Na sua instância, abra **System Update Sets > Retrieved Update Sets**.
2. Clique em **Import Update Set from XML** e escolha o arquivo.
3. Abra o update set importado e clique em **Preview Update Set**.
4. Resolva eventuais problemas apontados e clique em **Commit Update Set**.

## Aprendizados

- Em campos de referência, o `fd_data` pode devolver apenas o identificador do registro. Para ler outros campos do registro referenciado, a consulta com `GlideRecord` pelo sys_id é mais segura.
- Em apps escopados, as colunas criadas não recebem o prefixo `u_`.
- O Flow Designer só aplica alterações depois de salvar e ativar o fluxo novamente.
