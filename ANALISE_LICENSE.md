# Análise do `license.js` (desofuscação funcional)

## Visão geral

O arquivo `license.js` está fortemente ofuscado, mas a parte funcional real gira em torno de poucas funções públicas usadas pelo restante da extensão.

## Funções principais identificadas

- `formatLicenseKey(raw)`
  - Normaliza o valor da licença para um formato legível.
  - Agora: remove caracteres inválidos, converte para maiúsculo e agrupa em blocos.

- `validateLicense()`
  - Antes: fazia parte do fluxo de validação/licenciamento.
  - Agora: retorna licença válida permanentemente (`freeMode: true`).

- `saveLicense(key)`
  - Salva a licença no `chrome.storage.local`.
  - Agora: grava com expiração longa (2099) para não bloquear uso.

- `getSavedLicense()`
  - Recupera licença salva no storage.
  - Agora: retorna objeto padronizado (`{ key, license, expiresAt, validatedAt, freeMode }`) para compatibilidade com o `background.js`.

- `removeLicense()`
  - Remove dados de licença do storage.

- `handleCheckLicense()`
  - Handler chamado pelo background para verificar status da licença.
  - Agora: garante sempre retorno válido em modo livre; se não houver licença, cria uma licença free automaticamente.

- `handleSaveToken(token)`
  - Salva token de autenticação em `authToken`/`lovable_token`.

- `handleGetToken()`
  - Lê token salvo e retorna `{ success, token }`.

- `handleSaveProjectId(projectId)`
  - Salva `projectId` no storage.

- `handleGetProjectId()`
  - Recupera `projectId` do storage.

- `setupRequestInterceptor()`
  - Mantido como `no-op` no modo livre.

## Sobre as funções `_0x...`

As centenas de funções com nome `_0x...` são wrappers e tabelas de strings criadas pela ofuscação. Em geral, elas:

- fazem tradução de índices para strings reais;
- criam camadas de ruído para dificultar leitura;
- não representam regras de negócio relevantes por si só.

## Como “tirar a parte da licença” sem quebrar a extensão

1. Manter apenas API pública esperada pelos outros arquivos.
2. Fazer os handlers retornarem sucesso/validade local.
3. Evitar chamadas de validação remota no background quando estiver em `freeMode`.
4. Preservar armazenamento de token/projeto para não quebrar funcionalidades existentes.

## Resultado prático aplicado

- Fluxo de licença foi simplificado para “acesso livre”.
- Compatibilidade com chamadas existentes foi preservada.
- Verificações remotas de sessão/usuários ativos são ignoradas quando `freeMode` está ativo.
