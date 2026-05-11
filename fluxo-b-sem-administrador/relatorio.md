# Relatório — Laboratório de Inspeção HTTP/HTTPS — Fluxo B (sem administrador)

> **Como usar este template.** Preencha cada campo `[...]` com sua resposta e arraste as capturas de tela diretamente para os locais indicados. Preserve a formatação Markdown.
>
> **Escopo:** este fluxo usa HTTP em texto claro para a prática principal e compara HTTPS sem decriptação.

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
| Fluxo | **B — Aluno sem privilégio de administrador** |
| SO utilizado | [Windows / macOS / Linux] |
| Ferramenta de proxy | [Fiddler Classic / HTTP Toolkit / outra] |
| Navegador(es) | [Chrome / Edge / Firefox / ...] |
| HTTPS-First/HTTPS-Only desabilitado temporariamente? | [sim / não] |

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
| 1 | GET | `http://httpbingo.org/status/200` | [...] | [...] |
| 2 | GET | `http://httpbingo.org/redirect-to?status_code=301&url=/get` | [...] | [...] |
| 3 | GET | `http://httpbingo.org/status/404` | [...] | [...] |
| 4 | GET | `http://httpbingo.org/status/500` | [...] | [...] |

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
| `Strict-Transport-Security` | [esperado: não observado em HTTP puro] | [...] | [...] |

**Resposta curta:** qual é o papel de `Content-Encoding`? Por que `Strict-Transport-Security` não é esperado como mecanismo válido em HTTP puro?

[resposta]

---

## Atividade 6 — HTTP vs HTTPS sem decriptação

### Captura — HTTP puro

<!-- arraste a captura aqui: http://httpbingo.org/get -->

### Captura — HTTPS sem decriptação

<!-- arraste a captura aqui: https://httpbingo.org/get -->

| Campo | Visível em HTTP puro? | Visível em HTTPS sem decriptação? |
|---|---|---|
| Método real da requisição | [...] | [...] |
| Host | [...] | [...] |
| Path e query string | [...] | [...] |
| Cabeçalhos HTTP | [...] | [...] |
| Status code da resposta | [...] | [...] |
| Corpo da resposta | [...] | [...] |

**Resposta curta:** por que o HTTPS oculta a mensagem HTTP sem instalar um certificado raiz confiável na máquina?

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

## Atividade 8 — Manipulação simples com breakpoint *(Opcional)*

> Se sua ferramenta não suporta breakpoint interativo, informe isso e responda conceitualmente.

### Captura

<!-- arraste a captura aqui, se executou com Fiddler Classic -->

**JSON de resposta, se executado:**

```json
{
  "user-agent": ["[valor observado]"]
}
```

**Resposta curta:** o que este teste mostra sobre o papel ativo de um proxy?

[resposta]

- [ ] Breakpoints desabilitados ao final, se aplicável

---

## Reflexão final (opcional)

[até 10 linhas]

---

## Encerramento — Justificativa de segurança

**Por que este fluxo não exige remoção de certificado raiz? Por que isso seria obrigatório no Fluxo A?**

[resposta curta]

- [ ] HTTPS-First/HTTPS-Only reabilitado no navegador
- [ ] Ferramenta de proxy fechada
- [ ] Configuração manual de proxy removida, se aplicável

---

## Checklist de entrega

- [ ] Campos `[...]` substituídos
- [ ] Capturas inseridas
- [ ] Atividades 1 a 7 preenchidas; Atividade 8 preenchida se executada
- [ ] Encerramento preenchido
- [ ] PDF gerado como `SOBRENOME_NOME_RA_LAB_HTTP_FLUXOB.pdf`
- [ ] PDF submetido no Microsoft Teams
