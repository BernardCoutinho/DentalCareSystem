# PRD — DentalCare MVP

- Status: Em elaboração
- Versão: 1.0
- Data: 2026-07-24

## 1. Visão do produto

O DentalCare é um aplicativo de gestão para dentistas autônomos e pequenos consultórios odontológicos. O produto centraliza agenda, pacientes, informações clínicas essenciais, planos de tratamento e controle financeiro manual em uma experiência rápida e disponível mesmo sem conexão com a internet.

O MVP será construído com .NET MAUI, Blazor Hybrid e MudBlazor. As operações da interface utilizarão o SQLite local, enquanto o Firebase Firestore permitirá backup remoto e sincronização entre dispositivos em background.

## 2. Problema

Pequenos consultórios frequentemente distribuem sua operação entre agenda externa, fichas, anotações clínicas e controles financeiros separados. Essa fragmentação dificulta localizar informações durante o atendimento, acompanhar tratamentos e pagamentos e manter os dados consistentes entre profissionais.

Soluções que dependem permanentemente da internet também podem interromper atividades essenciais quando a conexão falha.

## 3. Objetivo do MVP

Permitir que uma clínica pequena execute o fluxo diário essencial em um único aplicativo:

1. cadastrar e localizar pacientes;
2. organizar a agenda dos profissionais;
3. consultar e registrar informações clínicas essenciais;
4. criar e acompanhar planos de tratamento;
5. confirmar procedimentos realizados;
6. registrar pagamentos manuais e acompanhar saldos;
7. continuar operando offline e sincronizar posteriormente.

## 4. Público-alvo

O MVP atende:

- dentistas autônomos;
- consultórios pequenos, inicialmente com uma única clínica e sem múltiplas unidades;
- equipes compostas por administrador, um ou mais dentistas e uma ou mais recepcionistas.

Uma mesma pessoa pode acumular os papéis de administrador e dentista.

## 5. Princípios do produto

- **Offline-first:** ações essenciais devem concluir no banco local sem depender de conexão imediata.
- **Baixo atrito:** tarefas frequentes devem exigir poucos passos.
- **Separação clínica e administrativa:** dados clínicos sensíveis não devem ser expostos à recepção.
- **Histórico preservado:** alterações atuais não devem modificar silenciosamente preços, responsáveis ou registros históricos.
- **Automação não bloqueante:** falhas no Firestore ou no Google Calendar não devem impedir o trabalho local.
- **Escopo adequado ao consultório:** o MVP controla a operação; não pretende ser sistema bancário, fiscal ou contábil.

## 6. Papéis

### 6.1 Administrador

Configura a clínica e os usuários, consulta a operação e executa ações administrativas. Quando também possuir papel clínico, acessa e registra informações clínicas conforme as permissões correspondentes.

### 6.2 Dentista

Consulta sua agenda, acessa o histórico autorizado do paciente, preenche anamnese e registros clínicos, cria e acompanha tratamentos, confirma procedimentos e consulta informações financeiras permitidas.

### 6.3 Recepcionista

Cadastra pacientes, organiza agendas, consulta tratamentos em nível administrativo, registra pagamentos e pode apoiar a confirmação operacional de procedimentos, sem acessar ou alterar dados clínicos sensíveis.

A matriz detalhada está definida em `docs/domain/permissions.md`.

## 7. Fluxo principal

```text
Agenda do dia
→ abrir o paciente
→ iniciar o atendimento
→ consultar ou atualizar informações clínicas
→ confirmar o procedimento realizado
→ registrar ou atualizar o plano de tratamento
→ registrar pagamento, quando houver
→ agendar retorno
→ finalizar o atendimento
```

A recepcionista pode executar as etapas administrativas. Diagnóstico, anamnese, evolução e demais informações clínicas permanecem restritos aos profissionais autorizados.

## 8. Capacidades do MVP

### 8.1 Autenticação e clínica

O sistema deve permitir:

- autenticação dos usuários da clínica;
- continuidade de acesso autorizado no dispositivo quando estiver offline, observadas as regras de segurança da sessão local;
- cadastro e edição dos dados básicos da clínica;
- cadastro, ativação e desativação de usuários;
- atribuição dos papéis `Administrator`, `Dentist` e `Receptionist`;
- associação de cada usuário ativo a uma única clínica no MVP.

Não haverá criação dinâmica de papéis nem permissões personalizadas por usuário no MVP.

### 8.2 Pacientes

O sistema deve permitir:

- cadastrar e editar dados cadastrais e de contato;
- localizar pacientes por nome, CPF ou telefone;
- desativar um paciente sem apagar seu histórico;
- permitir que diferentes dentistas da clínica atendam o mesmo paciente;
- separar informações cadastrais das informações clínicas sensíveis.

### 8.3 Dados clínicos essenciais

O sistema deve permitir que usuários autorizados:

- preencham e consultem anamnese;
- registrem diagnóstico e evolução clínica;
- consultem o histórico clínico do paciente;
- associem os registros ao paciente e ao profissional responsável;
- preservem autoria e datas operacionais mínimas.

A recepcionista não pode visualizar nem alterar esses dados.

O odontograma gráfico interativo, assinatura digital, exames, radiografias e anexos clínicos avançados não integram o escopo inicial.

### 8.4 Agenda

O sistema deve permitir:

- visualizar agendas por dia, semana e mês;
- criar, confirmar, remarcar e cancelar agendamentos;
- registrar paciente, profissional responsável, início, término, observações e procedimento previsto quando aplicável;
- acompanhar os estados `Scheduled`, `Confirmed`, `InProgress`, `Completed`, `Cancelled` e `NoShow`;
- impedir localmente a sobreposição de agendamentos ativos do mesmo profissional;
- identificar conflitos recebidos durante a sincronização e apresentá-los para resolução;
- operar a agenda interna sem conexão com serviços externos.

Todo agendamento deve estar associado a um `ProfessionalId`.

### 8.5 Google Calendar

O Google Calendar funciona como espelho auxiliar da agenda interna:

- a agenda do DentalCare é a referência operacional;
- não é necessário acessar o Google Calendar para criar ou alterar um agendamento;
- falhas, indisponibilidade ou limites da API não podem desfazer uma alteração local concluída;
- operações pendentes devem ser reenviadas posteriormente sem criar eventos duplicados;
- divergências não resolvidas automaticamente devem ser informadas ao usuário.

### 8.6 Planos de tratamento e procedimentos

O sistema deve permitir:

- criar planos de tratamento para pacientes;
- adicionar procedimentos com preço padrão e preço negociado preservados no item;
- acompanhar procedimentos planejados, agendados, realizados e cancelados;
- associar procedimentos planejados a agendamentos quando necessário;
- registrar o dentista executor e o usuário que confirmou a realização;
- impedir reversões livres quando houver movimentação financeira relacionada;
- calcular desconto ou acréscimo a partir dos preços padrão e negociado;
- aplicar as permissões definidas para alterações de preço.

Alterações futuras na tabela de preços não podem modificar itens já negociados.

### 8.7 Financeiro manual

O sistema deve permitir:

- registrar pagamentos integrais, parciais e parcelados;
- representar cada parcela como pagamento independente;
- informar valor, vencimento, data efetiva do recebimento e forma de pagamento;
- identificar o pagador quando for diferente do paciente;
- acompanhar as situações `Pending`, `Received`, `Cancelled` e `Refunded`;
- destacar pagamentos pendentes vencidos sem persistir um estado adicional de atraso;
- calcular total negociado, total recebido e saldo pendente do plano de tratamento;
- registrar manualmente quem confirmou o recebimento e quando;
- preservar cancelamentos e estornos para histórico e sincronização;
- exigir confirmação explícita antes de aceitar recebimento superior ao saldo, conforme regra futura de crédito ou adiantamento.

Somente pagamentos recebidos reduzem o saldo do tratamento.

## 9. Operação offline e sincronização

As operações essenciais de pacientes, agenda, dados clínicos, tratamentos e financeiro devem ser persistidas primeiro no SQLite.

Quando houver conectividade, o mecanismo de sincronização deverá enviar e receber alterações do Firestore em background. O produto deve:

- gerar identificadores globais localmente;
- armazenar datas de sincronização em UTC;
- utilizar exclusão lógica ou tombstones para dados sincronizáveis;
- não perder alterações locais após falha de rede ou encerramento da aplicação;
- evitar duplicação ao repetir operações;
- reavaliar regras críticas após receber alterações remotas;
- não resolver silenciosamente conflitos financeiros com risco de alterar saldos;
- apresentar conflitos que exijam decisão humana em linguagem compreensível.

## 10. Requisitos não funcionais

### 10.1 Segurança e privacidade

- Aplicar autorização por papel nos casos de uso, não somente na interface.
- Restringir dados clínicos sensíveis aos usuários autorizados.
- Evitar dados pessoais desnecessários em logs e mensagens de erro.
- Proteger credenciais, tokens e configurações sensíveis.
- Considerar a LGPD nas decisões de acesso, retenção e exclusão lógica.

### 10.2 Confiabilidade

- Uma operação confirmada ao usuário deve estar persistida localmente.
- Retentativas de sincronização devem ser idempotentes.
- Falhas em integrações externas devem ser observáveis e recuperáveis.
- Valores financeiros devem utilizar `decimal` com precisão definida.

### 10.3 Usabilidade

- As telas devem funcionar em desktop e dispositivos móveis compatíveis com .NET MAUI.
- Agenda, busca de paciente e registro de pagamento devem exigir poucos passos.
- Estados offline, sincronizando, com erro e com conflito devem ser visíveis.
- A interface deve usar componentes consistentes do MudBlazor e validações próximas aos campos afetados.

### 10.4 Manutenibilidade

- Domínio e casos de uso não devem depender diretamente de SQLite, Firestore, Google Calendar ou componentes visuais.
- Integrações externas devem ser acessadas por contratos da camada de aplicação.
- Regras permanentes devem permanecer em `docs/domain/invariants.md`.

## 11. Fora do escopo do MVP

- múltiplas clínicas ou unidades por implantação;
- papéis dinâmicos e permissões personalizadas por usuário;
- trilha completa e interface de auditoria;
- odontograma gráfico interativo;
- assinatura digital do paciente;
- automações por WhatsApp;
- armazenamento e visualização avançada de radiografias e exames;
- emissão ou validação de documentos fiscais;
- Receita Saúde e NFS-e;
- integração com Pix, cartões, links de pagamento, maquininhas ou TEF;
- conciliação bancária;
- cálculo ou transmissão de impostos;
- relatórios fiscais, contábeis ou de BI avançado.

## 12. Critérios de sucesso

O MVP será considerado funcionalmente validado quando uma clínica piloto conseguir:

1. cadastrar sua equipe e operar com as permissões esperadas;
2. cadastrar um paciente e localizar seu registro rapidamente;
3. criar e alterar agendamentos sem sobreposição para o mesmo profissional;
4. concluir o fluxo essencial de atendimento e registrar informações clínicas autorizadas;
5. criar um plano, preservar o preço negociado e confirmar procedimentos realizados;
6. registrar pagamentos parciais ou parcelados e obter saldo correto;
7. concluir operações essenciais sem internet e sincronizá-las posteriormente sem duplicação;
8. identificar claramente falhas ou conflitos que exijam intervenção;
9. usar o Google Calendar sem torná-lo dependência da agenda interna.

Metas quantitativas de usabilidade, desempenho e piloto ainda deverão ser definidas antes da homologação.

## 13. Dependências e riscos

- mecanismo seguro de autenticação offline;
- sincronização multi-dispositivo no Firestore e limites de quota;
- estratégia de resolução de conflitos por tipo de entidade;
- disponibilidade, autorização e quota da API do Google Calendar;
- proteção e retenção de dados pessoais e clínicos;
- consistência financeira diante de concorrência entre dispositivos;
- escolha e limitações do componente visual de agenda.

Os riscos e decisões pendentes devem ser acompanhados em `open-questions.md`.

## 14. Documentos relacionados

- `docs/product/mvp-scope.md` — lista normativa do que entra e não entra no MVP;
- `docs/product/roadmap.md` — evolução planejada após o MVP;
- `docs/product/open-questions.md` — decisões ainda não fechadas;
- `docs/domain/permissions.md` — matriz detalhada de autorização;
- `docs/domain/invariants.md` — regras permanentes do domínio;
- `docs/architecture/adr/` — decisões arquiteturais e limites assumidos.
