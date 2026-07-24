# ADR 0001 — Suportar clínica multiusuário no MVP

* Status: Aceita
* Data: 2026-07-23

## Contexto

A primeira utilização do DentalCare ocorrerá, provavelmente, por uma única dentista. Entretanto, o objetivo do produto é atender clínicas com vários dentistas e recepcionistas.

Construir o domínio inicialmente para um único profissional exigiria mudanças estruturais posteriores em usuários, pacientes, agendamentos, permissões, armazenamento remoto e sincronização.

## Decisão

O MVP será modelado como um sistema multiusuário para uma única clínica.

Serão suportados os papéis:

* administrador;
* dentista;
* recepcionista.

Os pacientes pertencerão à clínica e poderão ser atendidos por diferentes dentistas.

Todo agendamento deverá identificar o profissional responsável. Entidades compartilhadas deverão identificar a clínica à qual pertencem.

O primeiro usuário poderá acumular os papéis de administrador e dentista.

A implementação inicial do gerenciamento de usuários será simples, sem permissões personalizadas ou estrutura de múltiplas unidades.

## Consequências positivas

* Evita migração estrutural para adicionar novos profissionais.
* Permite agendas separadas por dentista.
* Estabelece isolamento dos dados por clínica.
* Permite aplicar restrições de acesso desde o início.
* Prepara o modelo para sincronização entre diferentes usuários e dispositivos.

## Consequências negativas

* Aumenta o número de entidades e relacionamentos do MVP.
* Exige autenticação, autorização e vínculo entre usuário e clínica.
* Exige tratamento de concorrência em agendamentos.
* Torna a sincronização mais complexa do que em um sistema de usuário único.

## Fora do escopo

Esta decisão não inclui no MVP:

* múltiplas clínicas por usuário;
* múltiplas unidades por clínica;
* papéis criados pelo administrador;
* permissões personalizadas por usuário;
* convites avançados;
* transferência de usuários entre clínicas.
