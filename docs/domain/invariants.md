# Invariantes de domínio

## Escopo organizacional

1. Todo dado compartilhado deve pertencer a uma clínica por meio de `ClinicId`.
2. No MVP, cada implantação e cada usuário ativo pertencem a uma única clínica.
3. Pacientes pertencem à clínica, e não exclusivamente a um dentista.
4. O MVP não oferece suporte a múltiplas unidades da mesma clínica.

## Usuários e profissionais

1. Todo usuário deve possuir ao menos um papel ativo.
2. Os papéis iniciais são `Administrator`, `Dentist` e `Receptionist`.
3. Um usuário pode ser simultaneamente administrador e dentista.
4. Um profissional utilizado em registros históricos não deve ser excluído fisicamente.
5. A desativação de um usuário deve impedir novos acessos sem remover os registros anteriormente criados por ele.

## Pacientes

1. Todo paciente deve pertencer a uma clínica.
2. Um paciente pode ser atendido por diferentes dentistas da mesma clínica.
3. Dados clínicos sensíveis somente podem ser acessados por usuários autorizados.
4. A desativação de um paciente não deve apagar seu histórico clínico ou financeiro.

## Agendamentos

1. Todo agendamento deve pertencer a uma clínica.
2. Todo agendamento deve possuir um profissional responsável por meio de `ProfessionalId`.
3. A data final deve ser posterior à data inicial.
4. Agendamentos cancelados não devem ser considerados na verificação de conflito de horário.
5. O sistema deve impedir dois agendamentos ativos sobrepostos para o mesmo profissional.
6. A validação local não elimina a necessidade de resolver conflitos encontrados durante a sincronização.
7. Cancelamentos devem ser lógicos, preservando os dados necessários para sincronização e histórico.

## Tratamentos e procedimentos

1. Todo plano de tratamento deve estar vinculado a um paciente e a uma clínica.
2. Um procedimento pode estar planejado, agendado, realizado ou cancelado.
3. Um procedimento realizado deve identificar o profissional executor.
4. A confirmação de realização pode ser feita por dentista, administrador ou recepcionista autorizada.
5. Confirmar um procedimento como realizado não concede permissão para registrar informações clínicas.
6. O preço aplicado deve ser preservado no item do tratamento e não pode depender exclusivamente do preço atual de uma tabela.
7. Alterações futuras na tabela de preços não devem modificar tratamentos ou procedimentos previamente negociados.
8. Um procedimento com movimentação financeira vinculada não pode ter sua realização desfeita sem regularização financeira.
9. Todo procedimento disponível para contratação deve possuir um preço padrão definido na tabela de preços da clínica.
10. Ao incluir um procedimento em um plano de tratamento, o preço padrão vigente deve ser copiado para `StandardPrice`.
11. O preço final negociado com o paciente deve ser armazenado separadamente em `AgreedPrice`.
12. Inicialmente, `AgreedPrice` deve ser igual a `StandardPrice`.
13. Alterações posteriores na tabela de preços não podem modificar `StandardPrice` nem `AgreedPrice` dos itens já incluídos em planos de tratamento.
14. `AgreedPrice` não pode ser negativo.
15. Valores monetários devem ser representados com tipo decimal e precisão definida na persistência.
16. O desconto ou acréscimo deve ser calculado pela diferença entre `StandardPrice` e `AgreedPrice`, sem necessidade de persistir valores redundantes.
17. Quando `AgreedPrice` for menor que `StandardPrice`, a diferença representa um desconto.
18. Quando `AgreedPrice` for maior que `StandardPrice`, a diferença representa um acréscimo.
19. Administradores e dentistas podem alterar o preço negociado.
20. Recepcionistas somente podem alterar o preço negociado quando possuírem a permissão explícita `TreatmentPlan.ChangePrice`.
21. Ao alterar o preço negociado, o sistema deve registrar `PriceModifiedByUserId` e `PriceModifiedAtUtc`.

### Cálculo de desconto e acréscimo

O desconto e o acréscimo são valores derivados e não devem ser persistidos:

- `DiscountAmount = max(StandardPrice - AgreedPrice, 0)`;
- `SurchargeAmount = max(AgreedPrice - StandardPrice, 0)`;
- `DiscountPercentage = StandardPrice > 0
  ? DiscountAmount / StandardPrice * 100
  : 0`.

Os cálculos devem respeitar a política de arredondamento monetário definida pela aplicação.

## Dados clínicos

1. Diagnóstico, anamnese, evolução, odontograma, prescrições, exames e anexos são dados clínicos sensíveis.
2. Recepcionistas não podem criar, editar ou visualizar dados clínicos sensíveis.
3. Informações administrativas e financeiras devem ser modeladas separadamente dos registros clínicos.
4. Somente profissionais autorizados podem confirmar informações clínicas.

## Autoria mínima

As entidades sincronizáveis devem possuir, quando aplicável:

* identificador global;
* `ClinicId`;
* `CreatedAtUtc`;
* `CreatedByUserId`;
* `UpdatedAtUtc`;
* `UpdatedByUserId`;
* indicador de exclusão lógica;
* versão ou marcador necessário para sincronização.

Procedimentos realizados também devem possuir:

* `CompletedAtUtc`;
* `CompletedByUserId`;
* `PerformedByProfessionalId`.

Esses campos oferecem rastreabilidade operacional mínima, mas não constituem uma trilha completa de auditoria.

## Operação offline e sincronização

1. O SQLite é a fonte local utilizada pelas operações da aplicação.
2. Uma operação local concluída não deve depender de conexão imediata com o Firestore.
3. Alterações pendentes devem ser sincronizadas posteriormente.
4. Todas as datas usadas na sincronização devem ser armazenadas em UTC.
5. Identificadores devem ser gerados localmente e ser globalmente únicos.
6. Exclusões sincronizáveis devem utilizar tombstones ou exclusão lógica.
7. Falhas de rede não devem descartar alterações já persistidas localmente.
8. Conflitos não resolvidos automaticamente devem ser apresentados de maneira compreensível ao usuário.
9. Regras críticas, como conflito de agenda, devem ser reavaliadas após receber alterações remotas.
