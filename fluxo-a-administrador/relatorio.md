# Relatório — Laboratório de Inspeção HTTP/HTTPS — Fluxo A (Administrador)

> **Como usar este template.** Preencha cada campo `[...]` com sua resposta e arraste as capturas de tela diretamente para os locais indicados. Preserve a formatação Markdown.
>
> **Escopo:** este fluxo inclui HTTP em texto claro, HTTPS sem decriptação e HTTPS com decriptação TLS pelo Fiddler Classic.

---

## Como anexar capturas de tela

1. Faça a captura de tela e salve como PNG.
2. No editor do GitHub ou GitHub.dev, posicione o cursor no local indicado.
3. Arraste o PNG para o editor. O GitHub inserirá uma linha `![image](...)`.

---

## Identificação

| Campo | Valor |
|---|---|
| Nome | [seu nome completo] |
| RA | [seu RA] |
| Disciplina | Redes de Computadores |
| Turma | [sua turma] |
| Data | [data da realização] |
| Fluxo | **A — Aluno com privilégio de administrador** |
| SO utilizado | [Windows 10 / Windows 11] |
| Ferramenta de proxy | Fiddler Classic |
| Navegador(es) | [Chrome / Edge / Firefox / ...] |
| Decriptação HTTPS habilitada? | [sim / não] |
| Certificado Fiddler instalado durante a atividade? | [sim / não] |

---

## Atividade 1 — Primeira captura

### Captura

<!-- arraste a captura aqui: sessão de http://example.com com Request/Response Raw -->

**Request-line:**

```http
[ex: GET / HTTP/1.1]
```

**Status-line:**

```http
[ex: HTTP/1.1 200 OK]
```

**Cabeçalhos do request:**

| Cabeçalho | Função |
|---|---|
| [...] | [...] |
| [...] | [...] |
| [...] | [...] |

**Resposta:**

| Campo | Valor observado |
|---|---|
| `Content-Type` | [...] |
| `Content-Length` ou `Transfer-Encoding` | [...] |

---

## Atividade 2 — Anatomia de um GET

### Captura

<!-- arraste a captura aqui: Request Raw e Response JSON -->

**Request-line completa:**

```http
[colar aqui]
```

**Cabeçalhos-chave:**

| Cabeçalho | Valor |
|---|---|
| `Host` | [...] |
| `User-Agent` | [...] |
| `Accept` | [...] |

**Campos do JSON de resposta:**

```json
{
  "args": [colar valor],
  "headers": [colar valor resumido],
  "origin": [colar valor]
}
```

**Resposta curta:** o que o campo `origin` representa? O `User-Agent` retornado coincide com o enviado?

[resposta]

---

## Atividade 3 — POST e envio de formulário

### Captura

<!-- arraste a captura aqui: POST para /post em Request Raw -->

**Request-line do POST:**

```http
[colar aqui]
```

| Cabeçalho | Valor |
|---|---|
| `Content-Type` | [...] |
| `Content-Length` | [...] |

**Corpo do request:**

```text
[colar aqui]
```

**Campo `form` da resposta:**

```json
[colar trecho relevante]
```

**Resposta curta:** qual formato codifica o corpo? Qual aba mostra literalmente os bytes enviados: `WebForms` ou `Raw`?

[resposta]

---

## Atividade 4 — Status codes

### Captura

<!-- arraste a captura aqui: lista do Fiddler com as quatro sessões -->

| # | Método | URL | Status-line | Tamanho/body |
|---|---|---|---|---|
| 1 | GET | `https://httpbingo.org/status/200` | [...] | [...] |
| 2 | GET | `https://httpbingo.org/redirect-to?status_code=301&url=/get` | [...] | [...] |
| 3 | GET | `https://httpbingo.org/status/404` | [...] | [...] |
| 4 | GET | `https://httpbingo.org/status/500` | [...] | [...] |

**Resposta curta:** no `301`, qual cabeçalho informa o destino do redirecionamento?

[resposta]

---

## Atividade 5 — Cabeçalhos essenciais

### Captura

<!-- arraste a captura aqui: Inspectors → Headers -->

| Cabeçalho | Req/Resp | Valor capturado | Função |
|---|---|---|---|
| `Host` | [...] | [...] | [...] |
| `User-Agent` | [...] | [...] | [...] |
| `Accept` | [...] | [...] | [...] |
| `Content-Type` | [...] | [...] | [...] |
| `Content-Length` / `Transfer-Encoding` | [...] | [...] | [...] |
| `Content-Encoding` | [...] | [...] | [...] |
| `Set-Cookie` | [...] | [...] | [...] |
| `Cache-Control` | [...] | [...] | [...] |
| `Strict-Transport-Security` | [...] | [...] | [...] |

**Resposta curta:** qual é o papel de `Content-Encoding` e de `Strict-Transport-Security`?

[resposta]

---

## Atividade 6 — HTTP vs HTTPS

### Captura — HTTP puro

<!-- arraste a captura aqui: http://httpbingo.org/get -->

### Captura — HTTPS sem decriptação

<!-- arraste a captura aqui: https://httpbingo.org/get sem decriptação -->

### Captura — HTTPS com decriptação

<!-- arraste a captura aqui: https://httpbingo.org/get com decriptação -->

| Situação | O que ficou visível? | O que ficou oculto? |
|---|---|---|
| HTTP puro | [...] | [...] |
| HTTPS sem decriptação | [...] | [...] |
| HTTPS com decriptação | [...] | [...] |

**Resposta curta:** por que a decriptação HTTPS pelo Fiddler exige instalar um certificado raiz?

[resposta]

---

## Atividade 7 — Cookies e sessão

### Captura

<!-- arraste a captura aqui: sequência cookies/set e cookies -->

| # | URL | `Set-Cookie` recebido | `Cookie` enviado |
|---|---|---|---|
| 1 | `/cookies/set?...` | [...] | [...] |
| 2 | `/cookies` | [...] | [...] |
| 3 | `/cookies` após recarregar | [...] | [...] |

**Resposta curta:** `Set-Cookie` apareceu em toda requisição ou apenas quando o servidor definiu/atualizou cookies? Quais atributos foram observados?

[resposta]

---

## Atividade 8 — Manipulação com breakpoint

### Captura

<!-- arraste a captura aqui: breakpoint com User-Agent editado -->

**JSON de resposta:**

```json
{
  "user-agent": ["[valor observado]"]
}
```

**Resposta curta:** o que este teste mostra sobre o papel ativo de um proxy?

[resposta]

- [ ] Breakpoints desabilitados ao final

---

## Reflexão final (opcional)

[até 10 linhas]

---

## Encerramento — Higiene de segurança

### Captura antes da remoção

<!-- arraste aqui a captura do certmgr.msc mostrando DO_NOT_TRUST_FiddlerRoot presente -->

### Captura depois da remoção

<!-- arraste aqui a captura mostrando o certificado ausente -->

- [ ] `Decrypt HTTPS traffic` desabilitado no Fiddler
- [ ] Certificado `DO_NOT_TRUST_FiddlerRoot` removido do Windows
- [ ] Certificado `DO_NOT_TRUST_FiddlerRoot` removido do Firefox, se aplicável
- [ ] Fiddler fechado

**Por que esta etapa é importante?**

[resposta curta]

---

## Checklist de entrega

- [ ] Campos `[...]` substituídos
- [ ] Capturas inseridas
- [ ] Atividades 1 a 8 preenchidas
- [ ] Encerramento com duas capturas concluído
- [ ] PDF gerado como `SOBRENOME_NOME_RA_LAB_HTTP_FLUXOA.pdf`
- [ ] PDF submetido no Microsoft Teams
