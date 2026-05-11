# Roteiro Prático — Fluxo B (Aluno SEM privilégio de administrador)

> **Pré-requisito deste roteiro:** você não possui privilégio de administrador na máquina usada para a atividade.
>
> **Escopo:** captura e inspeção de tráfego **HTTP em texto claro**. A inspeção de HTTPS com decriptação é substituída por comparação sem decriptação e análise teórica curta.
>
> **Fundamentação teórica:** consulte o [`readme.md`](../readme.md) na raiz do repositório para estrutura de mensagens, métodos, status codes, cabeçalhos, proxy e TLS.
>
> **Tempo total previsto em sala:** ~80 minutos, além da entrega. As perguntas foram reduzidas ao mínimo necessário para cobrir os objetivos pedagógicos.

---

## Sumário

- [1. Setup do ambiente](#1-setup-do-ambiente)
- [2. Validação rápida no início da aula](#2-validação-rápida-no-início-da-aula)
- [3. Atividades práticas](#3-atividades-práticas)
- [4. Entrega](#4-entrega)
- [5. Encerramento](#5-encerramento)

---

## 1. Setup do ambiente

### 1.1. Ferramenta de proxy

- **No laboratório:** use a instalação e as orientações fornecidas pelo professor. Normalmente o Fiddler Classic já estará disponível; apenas abra o programa pelo menu Iniciar.
- **Em máquina pessoal:** instale o Fiddler Classic a partir do site oficial da Telerik (<https://www.telerik.com/fiddler/fiddler-classic>). Se a instalação pedir administrador e você não tiver essa permissão, use uma alternativa autorizada pelo professor, como HTTP Toolkit portátil.

> ⚠️ Neste fluxo, **não instale certificado raiz** do proxy no sistema. A atividade não exige decriptação HTTPS.

### 1.2. Configuração para HTTP puro

1. No Fiddler, acesse **Tools → Options → HTTPS**.
2. Confirme que **Decrypt HTTPS traffic** está desmarcado.
3. Deixe a captura ativa (**Capturing** no canto inferior esquerdo; F12 alterna).

### 1.3. Evitar promoção automática para HTTPS

Para inspecionar HTTP puro, desabilite temporariamente o modo que força HTTPS:

- **Chrome/Edge:** `chrome://settings/security` ou `edge://settings/privacy` → desativar *Sempre usar conexões seguras*. Se necessário, verifique também as flags de HTTPS-First/HTTPS-Upgrades.
- **Firefox:** `about:preferences#privacy` → seção **Segurança HTTPS-Only** → escolher **Não ativar o modo HTTPS-Only**.

Use apenas os sites do roteiro, especialmente `http://httpbingo.org`, que aceita HTTP puro.

---

## 2. Validação rápida no início da aula

Antes de iniciar:

1. Fiddler aberto e capturando.
2. `Decrypt HTTPS traffic` desmarcado.
3. Acesse `http://httpbingo.org/get`.
4. A sessão deve aparecer como `GET`, com JSON legível em **Response → Raw** ou **Response → JSON**.

Se aparecer `CONNECT` ou a barra do navegador mudar para `https://`, revise a seção 1.3.

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

1. Acesse `http://httpbingo.org/get?aluno=SEU_NOME&curso=redes`, substituindo `SEU_NOME`.
2. Inspecione **Request → Raw** e **Response → JSON**.

**Registrar:**

- *Request-line* completa.
- Valores de `Host`, `User-Agent` e `Accept`.
- Campos `args`, `headers` e `origin` do JSON.

**Pergunta:** o que o campo `origin` representa? O `User-Agent` no JSON coincide com o enviado no request?

---

### Atividade 3 — POST e envio de formulário ⏱ ~12 min

1. Acesse `http://httpbingo.org/forms/post`.
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
| 1 | `http://httpbingo.org/status/200` | 2xx |
| 2 | `http://httpbingo.org/redirect-to?status_code=301&url=/get` | 3xx |
| 3 | `http://httpbingo.org/status/404` | 4xx |
| 4 | `http://httpbingo.org/status/500` | 5xx |

**Registrar:** método, URL, *status-line*, tamanho do corpo quando visível e presença/ausência de body.

**Pergunta:** no `301`, qual cabeçalho informa o destino do redirecionamento?

---

### Atividade 5 — Cabeçalhos essenciais ⏱ ~10 min

1. Em janela anônima, acesse:
   `http://httpbingo.org/response-headers?Cache-Control=max-age%3D3600&Set-Cookie=teste%3D1`
2. Acesse `http://httpbingo.org/gzip`.
3. Analise **Inspectors → Headers**.

**Registrar:** uma tabela consolidada com `Host`, `User-Agent`, `Accept`, `Content-Type`, `Content-Length` ou `Transfer-Encoding`, `Content-Encoding`, `Set-Cookie`, `Cache-Control` e `Strict-Transport-Security`.

**Pergunta:** qual é o papel de `Content-Encoding`? Por que `Strict-Transport-Security` não é esperado como mecanismo válido em respostas HTTP puro?

---

### Atividade 6 — HTTP vs HTTPS sem decriptação ⏱ ~12 min

1. Acesse `http://httpbingo.org/get`.
2. Acesse `https://httpbingo.org/get`.
3. Compare as duas sessões no Fiddler.

**Registrar:**

- Captura do HTTP puro, com request/response legíveis.
- Captura do HTTPS sem decriptação, normalmente visível como `CONNECT`.
- Tabela curta indicando o que é visível em cada caso: método, host, path/query, cabeçalhos, status code e corpo.

**Pergunta:** por que o HTTPS oculta a mensagem HTTP sem instalar um certificado raiz confiável na máquina?

---

### Atividade 7 — Cookies e sessão ⏱ ~10 min

1. Em janela anônima, acesse `http://httpbingo.org/cookies/set?disciplina=redes&professor=claudio`.
2. Observe o `Set-Cookie` na primeira resposta.
3. Observe o `Cookie` enviado na requisição seguinte para `/cookies`.
4. Recarregue a página uma vez.

**Registrar:** sequência das sessões, `Set-Cookie` recebido e `Cookie` enviado.

**Pergunta:** `Set-Cookie` aparece em toda requisição ou apenas quando o servidor define/atualiza cookies? Quais atributos do cookie foram observados?

---

### Atividade 8 — Manipulação simples com breakpoint ⏱ ~8 min

> Esta atividade depende do Fiddler Classic. Se você estiver usando HTTP Toolkit ou outra ferramenta sem breakpoint interativo, responda à pergunta de forma conceitual e informe a ferramenta utilizada.

1. Clique na janela do Fiddler para garantir foco.
2. Ative **Rules → Automatic Breakpoints → Before Requests**.
3. Acesse `http://httpbingo.org/user-agent`.
4. Na sessão pausada, edite o cabeçalho `User-Agent` para:
   `User-Agent: LaboratorioRedes/1.0 (Aluno NOME)`
5. Clique em **Run to Completion**.
6. Desabilite breakpoints em **Rules → Automatic Breakpoints → Disabled**.

**Registrar:** captura do request editado e campo `user-agent` do JSON de resposta, se a ferramenta permitir.

**Pergunta:** o que este teste mostra sobre o papel ativo de um proxy?

---

## 4. Entrega

### O que entregar

Um único PDF chamado **`SOBRENOME_NOME_RA_LAB_HTTP_FLUXOB.pdf`**, gerado a partir do `relatorio.md` preenchido.


### Como gerar o PDF

| Ferramenta | Quando usar | Como usar |
|---|---|---|
| **Markdown to PDF** | Recomendado, online e sem instalação. | Abrir <https://md2pdf.netlify.app>, colar o conteúdo raw do relatório e converter. |
| **Markdown PDF** no VS Code | Se o repositório estiver clonado. | Usar o comando *Markdown PDF: Export (pdf)*. |

### Critérios de avaliação

| Critério | Peso |
|---|---|
| Exatidão técnica das respostas | 40% |
| Evidências coerentes e legíveis | 30% |
| Clareza e objetividade da análise | 20% |
| Organização do relatório | 10% |

---

## 5. Encerramento

1. Reabilitar o HTTPS-First/HTTPS-Only Mode no navegador.
2. Fechar o Fiddler ou a ferramenta utilizada.
3. Remover qualquer configuração manual de proxy, se tiver sido criada.
4. No relatório, explicar em poucas linhas por que este fluxo não exige remoção de certificado raiz.
