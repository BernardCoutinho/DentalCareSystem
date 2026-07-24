# ADR 0003 — Adiar funcionalidades fiscais e integrações de pagamento

- Status: Aceita
- Data: 2026-07-24

## Contexto

O DentalCare precisa controlar os pagamentos relacionados aos tratamentos odontológicos. Entretanto, emissão fiscal, validação de documentos e integração direta com Pix, cartões ou maquininhas aumentariam significativamente a complexidade do MVP.

Essas integrações dependem de provedores externos, disponibilidade de conexão, idempotência, conciliação, estornos e regras fiscais que variam conforme a forma de atuação da clínica.

## Decisão

O MVP disponibilizará somente o controle financeiro manual dos tratamentos.

Serão suportados:

- pagamentos integrais, parciais e parcelados;
- valor e data de vencimento;
- data efetiva do recebimento;
- forma de pagamento registrada apenas para fins informativos;
- situações `Pending`, `Received`, `Cancelled` e `Refunded`;
- identificação do pagador quando diferente do paciente;
- vínculo do pagamento com o plano de tratamento;
- consulta ao saldo pendente e às parcelas vencidas.

`Payment` será uma entidade própria do domínio.

Pagamentos não dependerão de transações externas nem de documentos fiscais para serem registrados no MVP.

## Fora do escopo do MVP

- emissão ou validação de notas fiscais e recibos;
- Receita Saúde;
- integração com Pix;
- integração com cartões de crédito ou débito;
- integração com maquininhas ou TEF;
- links de pagamento;
- conciliação bancária;
- cálculo ou transmissão de impostos;
- relatórios e exportações fiscais.

## Evolução futura

Uma versão posterior poderá adicionar, como conceitos independentes:

- `PaymentTransaction`, para representar cobranças e transações processadas por provedores externos;
- `FiscalDocument`, para representar notas fiscais, recibos e outros documentos fiscais.

Esses conceitos poderão ser vinculados a `Payment` sem alterar o núcleo de tratamentos e pagamentos manuais.

## Consequências positivas

- Mantém o fluxo financeiro adequado à rotina de um consultório.
- Reduz dependências externas e complexidade operacional no MVP.
- Permite funcionamento offline do controle financeiro manual.
- Evita misturar pagamentos, transações bancárias e documentos fiscais.
- Mantém aberta a evolução para integrações futuras.

## Consequências negativas

- Os pagamentos deverão ser confirmados manualmente.
- O sistema não validará se o valor foi realmente recebido pelo meio informado.
- Documentos fiscais continuarão sendo emitidos e controlados fora do DentalCare.
- Não haverá conciliação automática no MVP.