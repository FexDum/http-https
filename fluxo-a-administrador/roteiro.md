# Roteiro Prático — Fluxo A (Aluno COM privilégio de administrador)

> **Pré-requisito deste roteiro:** você possui privilégio de administrador na máquina usada para a atividade, ou conhece a senha de administrador para o prompt do UAC.
>
> **Escopo:** captura e inspeção de tráfego **HTTP e HTTPS**, incluindo decriptação TLS por meio do certificado raiz do Fiddler Classic.
>
> **Fundamentação teórica:** consulte o [`readme.md`](../readme.md) na raiz do repositório para estrutura de mensagens, métodos, status codes, cabeçalhos, proxy e TLS.
>
> **Tempo total previsto em sala:** ~85 minutos, além da entrega. As perguntas foram reduzidas ao mínimo necessário para cobrir os objetivos pedagógicos.

---

## Sumário

- [1. Setup do ambiente](#1-setup-do-ambiente)
- [2. Validação rápida no início da aula](#2-validação-rápida-no-início-da-aula)
- [3. Atividades práticas](#3-atividades-práticas)
- [4. Entrega](#4-entrega)
- [5. Encerramento pós-aula](#5-encerramento-pós-aula)

---

## 1. Setup do ambiente

### 1.1. Fiddler Classic

- **No laboratório:** use a instalação e as orientações fornecidas pelo professor. Não reinstale nem altere componentes do sistema sem instrução.
- **Em máquina pessoal:** instale o Fiddler Classic a partir do site oficial da Telerik (<https://www.telerik.com/fiddler/fiddler-classic>) ou, no Windows, pelo PowerShell:

```powershell
winget install Telerik.Fiddler.Classic
```

> 💡 O Fiddler Classic está em modo de manutenção pela Telerik, mas continua adequado para este laboratório.

### 1.2. Ativar decriptação HTTPS

1. Abrir o Fiddler Classic **como administrador**.
2. Acessar **Tools → Options → HTTPS**.
3. Marcar **Capture HTTPS CONNECTs** e **Decrypt HTTPS traffic**.
4. Aceitar a instalação do certificado raiz `DO_NOT_TRUST_FiddlerRoot`.
5. Se usar Firefox, importar o certificado em `about:preferences#privacy → Ver Certificados → Autoridades → Importar`.

### 1.3. Teste do ambiente

Com o Fiddler capturando, acesse `https://httpbingo.org/get`. A sessão deve aparecer decifrada, com JSON legível em **Inspectors → Response → JSON**.

> ⚠️ O certificado raiz do Fiddler permite decriptar conexões HTTPS desta máquina enquanto estiver instalado. A remoção no encerramento é obrigatória.

---

## 2. Validação rápida no início da aula

Antes de iniciar:

1. Fiddler aberto e capturando (**Capturing** no canto inferior esquerdo; F12 alterna).
2. `Decrypt HTTPS traffic` habilitado.
3. `https://httpbingo.org/status/200` aparece como sessão HTTPS decifrada.

Interface principal:

- **Painel esquerdo:** lista de sessões.
- **Painel direito:** abas `Inspectors`, `Composer`, `AutoResponder`, `Filters`.
- Em `Inspectors`, use principalmente `Raw`, `Headers`, `JSON`, `WebForms`, `Cookies` e `TextView`.

> 💡 Limpe a lista entre atividades com **Edit → Remove → All Sessions** (`Ctrl+X`).

---

## 3. Atividades práticas

Preencha o [`relatorio.md`](./relatorio.md) desta pasta. Para cada atividade, registre a captura solicitada, os trechos de mensagem HTTP e respostas curtas.

### Atividade 1 — Primeira captura ⏱ ~8 min

1. Acesse `http://example.com`.
2. Selecione a sessão de `example.com`.
3. Abra **Request → Raw** e **Response → Raw**.

**Registrar:**

- Captura da sessão.
- *Request-line*.
- *Status-line*.
- Três cabeçalhos enviados pelo navegador e a função de cada um.
- `Content-Type` e `Content-Length` ou `Transfer-Encoding` da resposta.

---

### Atividade 2 — Anatomia de um GET ⏱ ~10 min

1. Acesse `https://httpbingo.org/get?aluno=SEU_NOME&curso=redes`, substituindo `SEU_NOME`.
2. Inspecione **Request → Raw** e **Response → JSON**.

**Registrar:**

- *Request-line* completa.
- Valores de `Host`, `User-Agent` e `Accept`.
- Campos `args`, `headers` e `origin` do JSON.

**Pergunta:** o que o campo `origin` representa? O `User-Agent` no JSON coincide com o enviado no request?

---

### Atividade 3 — POST e envio de formulário ⏱ ~12 min

1. Acesse `https://httpbingo.org/forms/post`.
2. Preencha o formulário e envie.
3. Localize a sessão `POST` para `/post`.

**Registrar:**

- *Request-line*.
- `Content-Type` e `Content-Length`.
- Corpo do request em **Request → Raw**.
- Campo `form` na resposta JSON.

**Pergunta:** qual formato codifica o corpo enviado? A aba **WebForms** ou a aba **Raw** mostra literalmente os bytes enviados?

---

### Atividade 4 — Status codes ⏱ ~10 min

Acesse e registre as sessões:

| # | URL | Classe esperada |
|---|---|---|
| 1 | `https://httpbingo.org/status/200` | 2xx |
| 2 | `https://httpbingo.org/redirect-to?status_code=301&url=/get` | 3xx |
| 3 | `https://httpbingo.org/status/404` | 4xx |
| 4 | `https://httpbingo.org/status/500` | 5xx |

**Registrar:** método, URL, *status-line*, tamanho do corpo quando visível e presença/ausência de body.

**Pergunta:** no `301`, qual cabeçalho informa o destino do redirecionamento?

---

### Atividade 5 — Cabeçalhos essenciais ⏱ ~10 min

1. Em janela anônima, acesse:
   `https://httpbingo.org/response-headers?Cache-Control=max-age%3D3600&Set-Cookie=teste%3D1&Strict-Transport-Security=max-age%3D31536000`
2. Acesse `https://httpbingo.org/gzip`.
3. Analise **Inspectors → Headers**.

**Registrar:** uma tabela consolidada com `Host`, `User-Agent`, `Accept`, `Content-Type`, `Content-Length` ou `Transfer-Encoding`, `Content-Encoding`, `Set-Cookie`, `Cache-Control` e `Strict-Transport-Security`.

**Pergunta:** qual é o papel de `Content-Encoding` e de `Strict-Transport-Security`?

---

### Atividade 6 — HTTP vs HTTPS ⏱ ~15 min

1. Desabilite temporariamente **Decrypt HTTPS traffic**.
2. Acesse `http://httpbingo.org/get`.
3. Acesse `https://httpbingo.org/get`.
4. Compare as duas sessões.
5. Reabilite **Decrypt HTTPS traffic** e acesse novamente `https://httpbingo.org/get`.

**Registrar:**

- Captura do HTTP puro.
- Captura do HTTPS sem decriptação.
- Captura do HTTPS com decriptação.

**Pergunta:** o que fica visível em HTTP, em HTTPS sem decriptação e em HTTPS com decriptação? Por que a decriptação exige instalar o certificado raiz?

---

### Atividade 7 — Cookies e sessão ⏱ ~10 min

1. Em janela anônima, acesse `https://httpbingo.org/cookies/set?disciplina=redes&professor=claudio`.
2. Observe o `Set-Cookie` na primeira resposta.
3. Observe o `Cookie` enviado na requisição seguinte para `/cookies`.
4. Recarregue a página uma vez.

**Registrar:** sequência das sessões, `Set-Cookie` recebido e `Cookie` enviado.

**Pergunta:** `Set-Cookie` aparece em toda requisição ou apenas quando o servidor define/atualiza cookies? Quais atributos do cookie foram observados?

---

### Atividade 8 — Manipulação simples com breakpoint ⏱ ~10 min

1. Clique na janela do Fiddler para garantir foco.
2. Ative **Rules → Automatic Breakpoints → Before Requests**.
3. Acesse `https://httpbingo.org/user-agent`.
4. Na sessão pausada, edite o cabeçalho `User-Agent` para:
   `User-Agent: LaboratorioRedes/1.0 (Aluno NOME)`
5. Clique em **Run to Completion**.
6. Desabilite breakpoints em **Rules → Automatic Breakpoints → Disabled**.

**Registrar:** captura do request editado e campo `user-agent` do JSON de resposta.

**Pergunta:** o que este teste mostra sobre o papel ativo de um proxy?

---

## 4. Entrega

### O que entregar

Um único PDF chamado **`SOBRENOME_NOME_RA_LAB_HTTP_FLUXOA.pdf`**, gerado a partir do `relatorio.md` preenchido.

> 💡 As capturas devem ser arrastadas diretamente para o editor do GitHub/GitHub.dev. Não crie pasta `evidencias/`.

### Como gerar o PDF

| Ferramenta | Quando usar | Como usar |
|---|---|---|
| **Markdown to PDF** | Recomendado, online e sem instalação. | Abrir <https://md2pdf.netlify.app>, colar o conteúdo raw do relatório e converter. |
| **Markdown PDF** no VS Code | Se o repositório estiver clonado. | Usar o comando *Markdown PDF: Export (pdf)*. |
| **Dillinger** | Alternativa online. | Importar o Markdown e exportar como PDF. |

### Critérios de avaliação

| Critério | Peso |
|---|---|
| Exatidão técnica das respostas | 40% |
| Evidências coerentes e legíveis | 30% |
| Clareza e objetividade da análise | 20% |
| Organização do relatório | 10% |

---

## 5. Encerramento pós-aula

> ⚠️ **Obrigatório no Fluxo A, em até 24 horas após a aula.**

1. Desabilitar **Decrypt HTTPS traffic**.
2. Remover o certificado `DO_NOT_TRUST_FiddlerRoot`:
   - Windows: `certmgr.msc` → *Autoridades de Certificação Raiz Confiáveis → Certificados*.
   - Firefox, se usado: `about:preferences#privacy → Ver Certificados → Autoridades`.
3. Fechar o Fiddler.
4. Anexar ao relatório duas capturas: uma antes e outra depois da remoção do certificado.

Essas capturas comprovam que o certificado existia e foi removido.
