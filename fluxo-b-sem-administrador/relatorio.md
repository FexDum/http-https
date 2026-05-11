# Relatório — Laboratório de Inspeção HTTP/HTTPS — Fluxo B (sem privilégio administrativo)

> **Como usar este template.** Edite este arquivo **diretamente no GitHub** (ícone de lápis no canto superior direito) ou no **GitHub.dev** (basta teclar `.` em qualquer página deste repositório no navegador). Preencha cada campo `[...]` com a sua resposta. Preserve a formatação markdown (tabelas, blocos de código) para facilitar a correção.
>
> **Observação:** toda a análise prática deste relatório é feita sobre tráfego **HTTP em texto claro**, usando `httpbingo.org` na porta 80. A análise de HTTPS é teórica, baseada na fundamentação do [`readme.md`](../readme.md) do repositório.

---

## Como anexar capturas de tela neste relatório

Este relatório usa o recurso nativo do GitHub para anexar imagens.

1. Faça a captura de tela com a ferramenta do sistema (Print Screen → Paint → salvar como PNG; ou *Snip & Sketch* no Windows; ou *Captura de Tela* no macOS).
2. No editor do GitHub (ou GitHub.dev), posicione o cursor no local indicado por `<!-- arraste a captura aqui -->`.
3. **Arraste o arquivo PNG diretamente** do seu explorador de arquivos para dentro do editor. O GitHub fará o upload automaticamente e inserirá uma linha do tipo `![image](https://github.com/user-attachments/assets/...)` na posição do cursor.
4. Salve o commit. Pronto — a imagem fica embutida no markdown renderizado.

> ⚠️ **Ctrl+V não funciona** no editor do GitHub para colar imagens da área de transferência. Use sempre **arrastar-e-soltar** do arquivo salvo em disco.

> 💡 **Geração do PDF.** Quando você converter este `.md` para PDF (md2pdf, VS Code, etc.), o gerador baixará as imagens hospedadas pelo GitHub automaticamente — desde que tenha acesso à internet no momento da conversão.

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
| SO utilizado | [Windows 11 / Ubuntu 22.04 / macOS ...] |
| Ferramenta de proxy | [Fiddler Classic pré-instalado / HTTP Toolkit / mitmproxy / ...] |
| Navegador(es) | [Chrome 124 / Firefox 125 / ...] |
| HTTPS-First Mode / HTTPS-Only desabilitado? | [sim / não] |

---

## Atividade 1 — Primeira captura (`http://example.com`)

### Captura — Atividade 1

<!-- arraste a captura aqui (sessão de http://example.com, abas Raw de Request e Response) -->

**Request-line enviada:**

```
[colar aqui a linha inicial do request, ex: GET / HTTP/1.1]
```

**Status-line recebida:**

```
[colar aqui, ex: HTTP/1.1 200 OK]
```

### Pergunta 1.1

> Cite **ao menos 5 cabeçalhos** que o navegador enviou no request e explique brevemente a função de cada um.

**Resposta:**

| Cabeçalho | Função |
|---|---|
| [cabeçalho 1] | [...] |
| [cabeçalho 2] | [...] |
| [cabeçalho 3] | [...] |
| [cabeçalho 4] | [...] |
| [cabeçalho 5] | [...] |

### Pergunta 1.2

> Qual foi o `Content-Length` da resposta? Se ele não apareceu, registre `Transfer-Encoding`, versão do protocolo ou outro indício observado. O corpo retornado é HTML, texto puro, JSON ou binário? Como você descobriu?

**Resposta:** [...]

---

## Atividade 2 — Anatomia de um GET (`http://httpbingo.org/get?...`)

### Captura — Atividade 2

<!-- arraste a captura aqui (Request → Raw e Response → JSON) -->

**Request-line completa:**

```
[colar aqui]
```

**Cabeçalhos-chave capturados:**

| Cabeçalho | Valor |
|---|---|
| `Host` | [...] |
| `User-Agent` | [...] |
| `Accept` | [...] |

**Campos do JSON de resposta** (lembrando que `httpbingo.org` retorna valores como arrays):

```json
{
  "args":    [colar valor],
  "headers": [colar valor resumido],
  "origin":  [colar valor]
}
```

### Pergunta 2.1

> O valor do campo `origin` corresponde a qual elemento da rede? Por que normalmente vem como uma lista de IPs separados por vírgula?

**Resposta:** [...]

### Pergunta 2.2

> Compare o `User-Agent` enviado com o que aparece no JSON da resposta. Coincidem?

**Resposta:** [...]

### Pergunta 2.3

> Em `http://httpbingo.org/headers`, liste até três cabeçalhos que o servidor vê mas **não aparecem** no Raw do request. De onde vêm? Se não encontrar três, explique por que o resultado pode variar.

**Resposta:**

| Cabeçalho visto pelo servidor | Origem provável | Observação |
|---|---|---|
| [...] | [...] | [...] |
| [...] | [...] | [...] |
| [...] | [...] | [...] |

---

## Atividade 3 — POST e envio de formulário (`http://httpbingo.org/forms/post` → `/post`)

### Captura — Atividade 3

<!-- arraste a captura aqui (Request → Raw do POST) -->

**Request-line do POST:**

```
[colar aqui]
```

**Cabeçalhos do request:**

| Cabeçalho | Valor |
|---|---|
| `Content-Type` | [...] |
| `Content-Length` | [...] |

**Corpo completo do request:**

```
[colar aqui o body enviado]
```

**Trecho do JSON de resposta (campo `form`):**

```json
"form": {
  [colar aqui]
}
```

### Pergunta 3.1

> Qual o formato do corpo? Como esse formato codifica caracteres especiais (espaço, acentos)?

**Resposta:** [...]

### Pergunta 3.2

> Comparando **Request → WebForms** e **Request → Raw**: qual das duas corresponde literalmente aos bytes enviados no socket TCP?

**Resposta:** [...]

### Bônus 3.3 — Composer com JSON (opcional)

> Só preencha se executou o exercício bônus. Envio manual de um `POST` para `http://httpbingo.org/post` com JSON. Que campo do JSON confirma que o servidor interpretou o JSON?

#### Captura — Bônus 3.3

<!-- arraste aqui a captura do Composer ou do terminal, se executou (opcional) -->

**Response JSON (trecho relevante):**

```json
{
  [colar aqui]
}
```

**Resposta:** [...]

---

## Atividade 4 — Catálogo de status codes (`http://httpbingo.org/...`)

### Captura — Atividade 4

<!-- arraste a captura aqui (lista do Fiddler com as 4 sessões principais) -->

| # | Método | URL | Status-line | `Content-Length` / `Transfer-Encoding` | Body presente? |
|---|---|---|---|---|---|
| 1 | GET | `http://httpbingo.org/status/200` | [...] | [...] | [sim/não] |
| 2 | GET | `http://httpbingo.org/redirect-to?status_code=301&url=/get` | [...] | [...] | [sim/não] |
| 3 | GET | `http://httpbingo.org/status/404` | [...] | [...] | [sim/não] |
| 4 | GET | `http://httpbingo.org/status/500` | [...] | [...] | [sim/não] |

### Pergunta 4.1

> Em qual dos status o corpo está ausente/tamanho zero? Isso é obrigatório pela especificação ou depende do servidor?

**Resposta:** [...]

### Pergunta 4.2

> No `301`, qual cabeçalho da resposta informa para onde ir? O que aconteceria se estivesse ausente?

**Resposta:** [...]

### Pergunta 4.3

> Diferença semântica entre `200`, `304` e `404` do ponto de vista do cache do navegador.

**Resposta:** [...]

### Bônus 4.4 — `418`, `503` e `304` (opcional)

> Só preencha se executou os exercícios bônus.

| # extra | Método | URL / Composer | Status-line | Observação |
|---|---|---|---|---|
| 5 | GET | `http://httpbingo.org/status/418` | [...] | [...] |
| 6 | GET | `http://httpbingo.org/status/503` | [...] | [...] |
| 7 | GET | `/cache` com `If-Modified-Since` via Composer | [...] | [...] |

**Comentário (opcional):** [...]

---

## Atividade 5 — Identificação de cabeçalhos (`http://httpbingo.org/response-headers?...` + `/gzip`)

### Captura — Atividade 5

<!-- arraste a captura aqui (Inspectors → Headers de uma das sessões) -->

Tabela consolidando os cabeçalhos observados nas Atividades 1–5:

| Cabeçalho | Req/Resp | Valor capturado | Função em uma frase |
|---|---|---|---|
| `Host` | [...] | [...] | [...] |
| `User-Agent` | [...] | [...] | [...] |
| `Accept` | [...] | [...] | [...] |
| `Accept-Encoding` | [...] | [...] | [...] |
| `Cookie` | [...] | [...] | [...] |
| `Server` | [...] | [...] | [...] |
| `Content-Type` | [...] | [...] | [...] |
| `Content-Encoding` | [...] | [...] | [...] |
| `Set-Cookie` | [...] | [...] | [...] |
| `Cache-Control` | [...] | [...] | [...] |
| `Strict-Transport-Security` | Não esperado em HTTP — ver Pergunta 5.3 | — | — |

### Pergunta 5.1

> `Content-Encoding: gzip`/`br` apareceu? Compare `Content-Length`, quando presente, com o conteúdo visível. O que explica a diferença?

**Resposta:** [...]

### Pergunta 5.2

> Cliente envia `Accept: application/json` mas o recurso só existe em `text/html`. Qual status code esperar?

**Resposta:** [...]

### Pergunta 5.3

> `Strict-Transport-Security` apareceu nas respostas HTTP? Por que esse cabeçalho está ausente neste fluxo? (Consulte a RFC 6797.) Qual é seu papel contra downgrades para HTTP puro?

**Resposta:** [...]

---

## Atividade 6 — HTTP vs HTTPS (análise sem decriptação)

### Captura — Atividade 6: HTTP puro (`http://httpbingo.org/get`)

<!-- arraste a captura HTTP aqui -->

### Captura — Atividade 6: HTTPS sem decriptação (`https://httpbingo.org/get`)

<!-- arraste a captura HTTPS aqui (apenas CONNECT visível) -->

### Pergunta 6.1

> Que método HTTP aparece na sessão do `https://httpbingo.org/get`? O que ele faz e por que existe?

**Resposta:** [...]

### Pergunta 6.2

> Tabela comparativa dos campos visíveis ao Fiddler em cada caso:

| Campo | Visível em HTTP? | Visível em HTTPS (sem decriptação)? |
|---|---|---|
| Método | [...] | [...] |
| URL completa (path + query) | [...] | [...] |
| Cabeçalhos de request | [...] | [...] |
| Corpo de request | [...] | [...] |
| Status code | [...] | [...] |
| Cabeçalhos de response | [...] | [...] |
| Corpo de response | [...] | [...] |
| Host (via SNI, no `CONNECT`) | [...] | [...] |
| IP e porta de destino | [...] | [...] |

### Pergunta 6.3 (teórica)

> O que você **veria** no Fiddler se tivesse privilégio de administrador e pudesse habilitar *Decrypt HTTPS traffic*? Indique telas/abas e justifique por que essa inspeção exige a instalação de um certificado raiz.

**Resposta:** [...]

### Pergunta 6.4

> Por que a técnica de decriptação dos *debugging proxies* **não** funcionaria contra um usuário se um atacante a tentasse sem instalar o certificado?

**Resposta:** [...]

---

## Atividade 7 — Cookies e sessão (`http://httpbingo.org/cookies/...`)

### Captura — Atividade 7

<!-- arraste a captura aqui (sequência de sessões de cookies/set e cookies) -->

| # | URL | `Set-Cookie` recebido | `Cookie` enviado |
|---|---|---|---|
| 1 | `/cookies/set?...` | [...] | [nenhum / ...] |
| 2 | `/cookies` (1ª visita) | [...] | [...] |
| 3 | `/cookies` (reload 1) | [...] | [...] |
| 4 | `/cookies` (reload 2) | [...] | [...] |

### Pergunta 7.1

> `Set-Cookie` aparece uma vez ou em toda requisição? Justifique.

**Resposta:** [...]

### Pergunta 7.2

> Que atributos o `Set-Cookie` trouxe? Explique cada um presente. Para atributos não observados, registre `não observado`.
>
> **Nota:** o httpbingo define cookies mínimos — apenas o atributo `Path=/` estará presente. Para cada atributo ausente, registre **não observado** e explique o comportamento padrão do navegador na sua ausência (ex.: sem `Expires`/`Max-Age` → cookie de sessão; sem `Secure` → pode ser enviado por HTTP; sem `SameSite` → o navegador aplica a política padrão da versão em uso).

**Resposta:**

| Atributo | Valor | Função | Observado? |
|---|---|---|---|
| `Path` | `/` | [...] | Sim |
| `Domain` | — | [...] | não observado |
| `Expires` | — | [...] | não observado |
| `Max-Age` | — | [...] | não observado |
| `Secure` | — | [...] | não observado |
| `HttpOnly` | — | [...] | não observado |
| `SameSite` | — | [...] | não observado |

### Pergunta 7.3

> O atributo `Secure` pode aparecer num cookie recebido por HTTP puro? Qual seria o comportamento esperado? Relacione com o fato de que todo o tráfego desta atividade é visível em texto claro.

**Resposta:** [...]

### Pergunta 7.4

> Na aba **Inspectors → Cookies**, o cookie armazenado coincide com o campo `cookies` do JSON?

**Resposta:** [...]

---

## Atividade 8 — Manipulação com breakpoints

> **Atividade exclusiva do Fiddler Classic.** Se você utilizou HTTP Toolkit ou mitmproxy, responda às questões 8.1 e 8.2 de forma teórica (sem capturas de tela), indicando que a ferramenta utilizada não suporta breakpoints interativos.

### Captura — Atividade 8: edição do User-Agent

<!-- arraste aqui a captura do breakpoint com User-Agent editado -->

**JSON de resposta após edição:**

```json
{
  "user-agent": ["[valor forjado]"]
}
```

### Pergunta 8.1

> O servidor pode detectar que o `User-Agent` foi forjado? Discuta.

**Resposta:** [...]

### Pergunta 8.2

> Após editar a status-line de `200 OK` para `404 Not Found`, o que o navegador exibe? Comente o papel do proxy como MITM.

#### Captura — Atividade 8: edição da status-line

<!-- arraste aqui a captura do breakpoint com status-line editada -->

**Resposta:** [...]

### Pergunta 8.3

> Confirme que todos os breakpoints foram desabilitados.

- [ ] Breakpoints desabilitados ao final (Shift+F11)

---

## Atividade 9 — Redirecionamento HTTP → HTTPS

### Captura — Atividade 9

<!-- arraste aqui a captura da sessão original com 301 Moved Permanently -->

**Status-line da resposta a `http://httpbingo.org/redirect-to?status_code=301&url=https%3A%2F%2Fhttpbingo.org%2Fget`:**

```
[colar aqui, ex: HTTP/1.1 301 Moved Permanently]
```

**Cabeçalho `Location` da resposta:**

```
Location: [colar aqui]
```

### Pergunta 9.1

> Código de status e cabeçalho que direcionaram o navegador para `https://`.

**Resposta:** [...]

### Pergunta 9.2

> Além do redirecionamento 3xx, qual outro mecanismo/cabeçalho faz o navegador passar a forçar HTTPS em visitas futuras? Cite a RFC.

**Resposta:** [...]

### Pergunta 9.3

> Se esse cabeçalho fosse enviado por uma resposta servida via HTTP puro, o navegador deveria obedecer? Justifique com base na RFC.

**Resposta:** [...]

---

## Questões de Verificação

### 1. Por que `Host` é obrigatório em HTTP/1.1 mas era opcional em HTTP/1.0?

[resposta]

### 2. Diferença entre `401 Unauthorized` e `403 Forbidden`. Dê um exemplo prático de quando cada um deveria aparecer.

[resposta]

### 3. Um `POST` enviado duas vezes produz o mesmo efeito? E um `PUT`? Justifique em termos de idempotência.

[resposta]

### 4. Mesmo com HTTPS, um observador na rede pode descobrir qual site está sendo visitado. Por quê? (Cite SNI e DNS.)

[resposta]

### 5. Impacto prático de `Cache-Control: no-store`. Em que tipo de recurso ele seria essencial?

[resposta]

### 6. (Exclusiva do Fluxo B) Cite três cabeçalhos de segurança que não fazem sentido em respostas HTTP puro. Para cada um, descreva o que aconteceria se enviado por um servidor HTTP. (Cite RFC 6797 para HSTS.)

| Cabeçalho | Comportamento esperado sobre HTTP | Referência |
|---|---|---|
| [...] | [...] | [...] |
| [...] | [...] | [...] |
| [...] | [...] | [...] |

---

## Reflexão final (opcional, até 10 linhas)

> O que você aprendeu que não conhecia antes deste laboratório? Há algum cabeçalho, código de status ou comportamento que passou a olhar com mais atenção? Alguma dificuldade que recomendaria evitar para a próxima turma?

[reflexão]

---

## Encerramento — justificativa de segurança (Fluxo B)

**Parágrafo: por que a remoção de certificado é dispensável neste fluxo e por que seria obrigatória para o aluno administrador** (até 5 linhas, com base na seção 4.6 do [`readme.md`](../readme.md)):

[redigir aqui]

- [ ] HTTPS-First Mode / HTTPS-Only Mode reabilitado no navegador
- [ ] Fiddler / HTTP Toolkit / mitmproxy fechado (porta de proxy liberada)
- [ ] Configuração de proxy removida do navegador (se aplicável)

---

## Checklist de entrega

- [ ] Todos os campos `[...]` substituídos
- [ ] Todas as capturas inseridas via arrastar-e-soltar no editor do GitHub
- [ ] 6 questões de verificação respondidas (incluindo a exclusiva do Fluxo B)
- [ ] Atividade 9 (redirecionamento HTTP→HTTPS) documentada
- [ ] Justificativa de encerramento redigida
- [ ] Arquivo `relatorio.md` convertido em **`SOBRENOME_NOME_RA_LAB_HTTP_FLUXOB.pdf`** com imagens embutidas
- [ ] Submetido no Microsoft Teams dentro do prazo
