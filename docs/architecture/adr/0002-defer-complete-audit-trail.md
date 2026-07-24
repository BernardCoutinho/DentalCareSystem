# ADR 0002 — Adiar trilha completa de auditoria

* Status: Aceita
* Data: 2026-07-23

## Contexto

O sistema realizará operações relevantes, como alteração de preços, confirmação de procedimentos e registro de pagamentos. Uma trilha completa de auditoria permitiria reconstruir todas as alterações, identificar valores anteriores e exigir justificativas para determinadas operações.

Entretanto, implementar esse recurso no MVP aumentaria significativamente a complexidade do domínio, da persistência, da sincronização e da interface.

## Decisão

A trilha completa de auditoria será adiada para uma versão posterior ao MVP.

O MVP manterá somente rastreabilidade operacional mínima por meio de campos de criação, atualização e autoria.

Para a conclusão de procedimentos, deverão ser armazenados:

* momento da realização;
* profissional executor;
* usuário que confirmou a realização;
* momento da confirmação;
* preço aplicado.

Não será criada uma interface de consulta de auditoria no MVP.

## Proteções mantidas

Mesmo sem auditoria completa:

* exclusões importantes devem ser lógicas;
* o preço negociado deve ser preservado;
* registros históricos não devem depender de dados atuais mutáveis;
* procedimentos com movimentações financeiras não poderão ser revertidos livremente;
* alterações sincronizáveis devem manter autoria e data de atualização.

## Consequências positivas

* Reduz o escopo e o tempo de desenvolvimento do MVP.
* Evita introduzir prematuramente armazenamento baseado em eventos.
* Mantém os principais campos necessários para suporte operacional.

## Consequências negativas

* Não será possível reconstruir todas as versões de um registro.
* Valores anteriores poderão não estar disponíveis.
* Investigações administrativas terão informações limitadas.
* Algumas operações sensíveis dependerão de bloqueios, sem histórico detalhado.

## Evolução futura

Uma versão posterior poderá acrescentar:

* eventos de auditoria imutáveis;
* valor anterior e valor posterior;
* usuário, dispositivo e momento da operação;
* justificativas obrigatórias;
* relatórios de auditoria;
* políticas de retenção;
* tratamento específico de eventos de auditoria na sincronização.
