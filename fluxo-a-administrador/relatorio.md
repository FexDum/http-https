<img width="747" height="266" alt="Atv4" src="https://github.com/user-attachments/assets/e6a3100b-bb91-429b-8a56-194171a3077b" /># Relatório — Laboratório de Inspeção HTTP/HTTPS — Fluxo A (Administrador)

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
| Nome | Osvaldo Vasoncellos Neto |
| RA | 242284 |
| Disciplina | Redes de Computadores |
| Turma | A |
| Data | 15/05/2026 |
| Fluxo | **A — Aluno com privilégio de administrador** |
| SO utilizado | Windows 10 |
| Ferramenta de proxy | Fiddler Classic |
| Navegador(es) | Chrome |
| Decriptação HTTPS habilitada? | sim |
| Certificado Fiddler instalado durante a atividade? | sim |

---

## Atividade 1 — Primeira captura

### Captura

<img width="1920" height="1051" alt="example" src="https://github.com/user-attachments/assets/682e38f3-6230-465b-baaa-80e098bfb8b4" />

**Request-line:**

```http
GET http://example.com/ HTTP/1.1
```

**Status-line:**

```http
HTTP/1.1 200 OK
```

**Cabeçalhos do request:**

| Cabeçalho | Função |
|---|---|
| Host | example.com |
| Connection | keep-alive |
| Pragma | no-cache |

**Resposta:**

| Campo | Valor observado |
|---|---|
| `Content-Type` | text/html |
| `Transfer-Encoding` | chunked |

---

## Atividade 2 — Anatomia de um GET

### Captura

<img width="1920" height="1050" alt="Atv2" src="https://github.com/user-attachments/assets/be6fe686-5bcf-45b5-9503-13b7d917acde" />

**Request-line completa:**

```http
GET https://http.aulasrede.com.br/get?aluno=Osvaldo&curso=redes HTTP/1.1
```

**Cabeçalhos-chave:**

| Cabeçalho | Valor |
|---|---|
| `Host` | http.aulasrede.com.br |
| `User-Agent` | Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36 |
| `Accept` | text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7 |

**Campos do JSON de resposta:**

```json
{
  "args":
  {
    "aluno": [
      "Osvaldo"
    ],
    "curso": [
      "redes"
    ]
  },
  "headers":
  {
    "Host": [
      "func-http-aularedes-meta-w.azurewebsites.net"
    ],
    "Max-Forwards": [
      "9"
    ],
    "Pragma": [
      "no-cache"
    ]
  },
  "origin": "200.173.179.130:55100"
}
```

**Resposta curta:** o que o campo `origin` representa? O `User-Agent` retornado coincide com o enviado?

  > O campo `origin` indica a origem de onde a solicitação partiu
  > Sim o `User-Agent` são iguais em ambos os Request e no Json 

---

## Atividade 3 — POST e envio de formulário

### Captura

<img width="1917" height="1053" alt="Atv3" src="https://github.com/user-attachments/assets/eeda0754-cf43-4505-b895-9e57a0ec1885" />

**Request-line do POST:**

```http
POST https://http.aulasrede.com.br/post HTTP/1.1
```

| Cabeçalho | Valor |
|---|---|
| `Content-Type` | application/x-www-form-urlencoded |
| `Content-Length` | 87 |

**Corpo do request:**

```text
nome=Aluno&disciplina=Redes&observacao=Teste+de+formul%C3%A1rio+HTTP.&interesse=headers
```

**Campo `form` da resposta:**

```json
{
  "args": {},
  "data": "nome=Aluno&disciplina=Redes&observacao=Teste+de+formul%C3%A1rio+HTTP.&interesse=headers",
  "files": {},
  "form": {
    "nome": [
      "Aluno"
    ],
    "disciplina": [
      "Redes"
    ],
    "observacao": [
      "Teste de formulário HTTP."
    ],
    "interesse": [
      "headers"
    ]
  },
  "headers":
  {
    "User-Agent": [
      "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36"
    ],
    "Accept-Language": [
      "pt-BR,pt;q=0.9,en-US;q=0.8,en;q=0.7"
    ],
    "Cache-Control": [
      "max-age=0"
    ],
  }
  "json": null,
  "method": "POST",
  "origin": "187.92.211.170:30932",
  "url": "https://http.aulasrede.com.br/post",
  "path": "/post"
}
```

**Resposta curta:** qual formato codifica o corpo? Qual aba mostra literalmente os bytes enviados: `WebForms` ou `Raw`?

  > o formato é texto puro ou JSON, que é a versão codificada para transmitir
  > A aba que mostra os bytes é a aba `Raw`

---

## Atividade 4 — Status codes

### Captura

<img width="747" height="266" alt="Atv4" src="https://github.com/user-attachments/assets/a68ac8db-a46a-47d4-a968-75689a6ae85a" />

| # | Método | URL | Status-line | Tamanho/body |
|---|---|---|---|---|
| 1 | GET | `https://http.aulasrede.com.br/status/200` | HTTP/1.1 200 OK | 60 |
| 2 | GET | `https://http.aulasrede.com.br/redirect-to?status_code=301&url=/get` | HTTP/1.1 301 Moved Permanently | 0 |
| 3 | GET | `https://http.aulasrede.com.br/status/404` | HTTP/1.1 404 Not Found | 45 |
| 4 | GET | `https://http.aulasrede.com.br/status/500` | HTTP/1.1 500 Internal Server Error | 57 |

**Resposta curta:** no `301`, qual cabeçalho informa o destino do redirecionamento?

  > GET https://http.aulasrede.com.br/redirect-to?status_code=301&url=/get HTTP/1.1

---

## Atividade 5 — Cabeçalhos essenciais

### Captura

<img width="1920" height="1052" alt="Atv5" src="https://github.com/user-attachments/assets/5c1aea5c-e7d7-43fb-8cfa-300e90aeafe0" />

| Cabeçalho | Req/Resp | Valor capturado | Função |
|---|---|---|---|
| `Host` | http.aulasrede.com.br | [...] | [...] |
| `User-Agent` | Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/142.0.0.0 Safari/537.36 | [...] | [...] |
| `Accept` | text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7 | [...] | [...] |
| `Content-Type` | application/json; charset=utf-8 | [...] | [...] |
| `Content-Length` | 1536 | [...] | [...] |
| `Content-Encoding` | gzip | [...] | [...] |
| `Set-Cookie` | teste=1; domain=http.aulasrede.com.br; path=/; secure | [...] | [...] |
| `Cache-Control` | max-age=3600 | [...] | [...] |
| `Strict-Transport-Security` | max-age=31536000 | [...] | [...] |

**Resposta curta:** qual é o papel de `Content-Encoding` e de `Strict-Transport-Security`?

  > O Content-Encoding e o Strict-Transport-Security desempenham papéis completamente distintos no tráfego web: o primeiro foca em desempenho e transferência de dados, enquanto o segundo foca em segurança e criptografia

---

## Atividade 6 — HTTP vs HTTPS

### Captura — HTTP puro

<!-- arraste a captura aqui: http://http.aulasrede.com.br/get com redirecionamento 301 para HTTPS -->

### Captura — HTTPS sem decriptação

<!-- arraste a captura aqui: https://http.aulasrede.com.br/get sem decriptação -->

### Captura — HTTPS com decriptação

<!-- arraste a captura aqui: https://http.aulasrede.com.br/get com decriptação -->

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
| 1 | `/cookies/set?...` | [...] | disciplina=redes; professor=claudio; |
| 2 | `/cookies` | disciplina=redes; professor=claudio | [...] |
| 3 | `/cookies` após recarregar | [...] | [...] |

**Resposta curta:** `Set-Cookie` apareceu em toda requisição ou apenas quando o servidor definiu/atualizou cookies? Quais atributos foram observados?

  > O cabeçalho Set-Cookie não aparece em todas as requisições. Ele é um cabeçalho de resposta enviado pelo servidor exclusivamente quando este precisa criar, atualizar ou expirar um cookie no seu navegador
  > disciplina=redes; professor=claudio;

---

## Atividade 8 — Manipulação simples com breakpoint *(Opcional)*

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

<img width="1237" height="1050" alt="SemNot" src="https://github.com/user-attachments/assets/38c494ee-623f-4a5e-b34d-6863d0ce7421" />

- [X] `Decrypt HTTPS traffic` desabilitado no Fiddler
- [X] Certificado `DO_NOT_TRUST_FiddlerRoot` removido do Windows
- [X] Certificado `DO_NOT_TRUST_FiddlerRoot` removido do Firefox, se aplicável
- [X] Fiddler fechado

**Por que esta etapa é importante?**

  > Essa etapa é vital para restaurar a segurança e a privacidade da sua conexão.O Fiddler funciona como um proxy que intercepta o tráfego web. Para ler e exibir dados criptografados, ele força o sistema a usar um certificado digital próprio.

---

## Checklist de entrega

- [X] Campos `[...]` substituídos
- [X] Capturas inseridas
- [ ] Atividades 1 a 7 preenchidas; Atividade 8 preenchida se executada
- [ ] Encerramento com duas capturas concluído
- [X] PDF gerado como `SOBRENOME_NOME_RA_LAB_HTTP_FLUXOA.pdf`
- [X] PDF submetido no Microsoft Teams
