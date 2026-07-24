# Papéis e permissões

## Objetivo

Este documento define os papéis de usuário e as permissões iniciais do DentalCare.

As permissões descritas representam o comportamento esperado no MVP. Permissões personalizadas por usuário não fazem parte do escopo inicial.

## Papéis

O sistema possui inicialmente três papéis:

* `Administrator`: responsável pela administração da clínica e dos usuários.
* `Dentist`: profissional responsável pelos atendimentos e registros clínicos.
* `Receptionist`: responsável pelas atividades operacionais, administrativas e financeiras da recepção.

Um mesmo usuário pode acumular os papéis de administrador e dentista.

## Matriz de permissões

| Operação                                         |          Administrador |           Dentista |               Recepcionista |
| ------------------------------------------------ | ---------------------: | -----------------: | --------------------------: |
| Gerenciar dados da clínica                       |                    Sim |                Não |                         Não |
| Gerenciar usuários                               |                    Sim |                Não |                         Não |
| Cadastrar e editar pacientes                     |                    Sim |                Sim |                         Sim |
| Consultar dados cadastrais de pacientes          |                    Sim |                Sim |                         Sim |
| Criar agendamentos                               |                    Sim |                Sim |                         Sim |
| Remarcar ou cancelar agendamentos                |                    Sim |                Sim |                         Sim |
| Consultar agenda de profissionais                |                    Sim |                Sim |                         Sim |
| Criar planos de tratamento                       | Conforme papel clínico |                Sim |                         Não |
| Visualizar tratamentos e procedimentos           |                    Sim |                Sim |                         Sim |
| Visualizar preços e condições de pagamento       |                    Sim | Conforme permissão |                         Sim |
| Agendar procedimentos planejados                 |                    Sim |                Sim |                         Sim |
| Marcar procedimento como realizado               |                    Sim |                Sim |                         Sim |
| Desfazer conclusão de procedimento               |                    Sim |                Sim | Conforme regras financeiras |
| Registrar pagamento                              |                    Sim | Conforme permissão |                         Sim |
| Alterar preço ou conceder desconto               |                    Sim | Conforme permissão |          Conforme permissão |
| Registrar diagnóstico                            | Conforme papel clínico |                Sim |                         Não |
| Preencher anamnese                               | Conforme papel clínico |                Sim |                         Não |
| Registrar evolução clínica                       | Conforme papel clínico |                Sim |                         Não |
| Editar odontograma clínico                       | Conforme papel clínico |                Sim |                         Não |
| Visualizar prescrições, exames e anexos clínicos | Conforme papel clínico |                Sim |                         Não |

## Acesso da recepção

A recepcionista pode acessar as informações necessárias para executar o trabalho administrativo da clínica, incluindo:

* dados cadastrais e contatos do paciente;
* agenda e profissional responsável;
* tratamentos planejados ou em andamento;
* procedimentos planejados, agendados e realizados;
* preços, descontos autorizados, parcelas, pagamentos e saldo pendente.

A recepcionista pode marcar um procedimento como realizado quando solicitado pelo dentista. Essa ação representa uma confirmação operacional e não autoriza o preenchimento ou a alteração de informações clínicas.

A recepcionista não pode acessar ou alterar:

* anamnese;
* diagnóstico;
* evolução clínica;
* observações clínicas;
* odontograma clínico detalhado;
* prescrições;
* exames;
* anexos clínicos;
* materiais utilizados durante o atendimento.

## Conclusão de procedimento

Ao marcar um procedimento como realizado, o sistema deve registrar no mínimo:

* data e hora da realização;
* dentista responsável pela execução;
* usuário que confirmou a realização;
* data e hora da confirmação;
* preço aplicado ao procedimento;
* agendamento relacionado, quando houver.

Caso existam pagamentos vinculados, o procedimento não poderá simplesmente retornar ao estado anterior. A reversão deverá respeitar as regras financeiras que ainda serão detalhadas.

## Evolução futura

Não fazem parte do MVP:

* permissões personalizadas por usuário;
* criação dinâmica de papéis;
* trilha completa de auditoria;
* relatórios de auditoria;
* exigência de justificativa para toda alteração;
* aprovação em dois níveis para operações sensíveis.
