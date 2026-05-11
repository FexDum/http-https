# Relatório — Laboratório de Inspeção HTTP/HTTPS — Fluxo A (Administrador)

> **Como usar este template.** Edite este arquivo **diretamente no GitHub** (ícone de lápis no canto superior direito) ou no **GitHub.dev** (basta teclar `.` em qualquer página deste repositório no navegador). Preencha cada campo `[...]` com a sua resposta. Preserve a formatação markdown (tabelas, blocos de código) para facilitar a correção.
>
> **Observação:** este fluxo inclui inspeção prática de **HTTP em texto claro** e de **HTTPS com decriptação TLS**, usando exclusivamente o Fiddler Classic.

---

## Como anexar capturas de tela neste relatório

Este relatório usa o recurso nativo do GitHub para anexar imagens — **não há pasta `evidencias/` a ser criada**.

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
| Fluxo | **A — Aluno com privilégio de administrador** |
| SO utilizado | [Windows 10 / Windows 11] |
| Ferramenta de proxy | Fiddler Classic |
| Navegador(es) | [Chrome 124 / Firefox 125 / ...] |
| Decriptação HTTPS habilitada? | [sim / não] |
| Certificado Fiddler instalado? | [sim / não] |

---

## Atividade 1 — Primeira captura

### Captura — Atividade 1

<!-- arraste a captura aqui (sessão selecionada em http://example.com, abas Raw de Request e Response) -->

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

## Atividade 2 — Anatomia de um GET

### Captura — Atividade 2

<!-- arraste a captura aqui (Request → Raw e Response → JSON da URL httpbingo.org/get?aluno=...&curso=redes) -->

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

> Compare o `User-Agent` enviado com o que aparece no JSON da resposta. Coincidem? Se não, qual intermediário pode ter modificado?

**Resposta:** [...]

### Pergunta 2.3

> Em `https://httpbingo.org/headers`, liste até três cabeçalhos que o servidor vê mas **não aparecem** no Raw do request. De onde vêm? Se não encontrar três, explique por que o resultado pode variar.

**Resposta:**

| Cabeçalho visto pelo servidor | Origem provável | Observação |
|---|---|---|
| [...] | [...] | [...] |
| [...] | [...] | [...] |
| [...] | [...] | [...] |

---

## Atividade 3 — POST e envio de formulário

### Captura — Atividade 3

<!-- arraste a captura aqui (Request → Raw do POST para httpbingo.org/post) -->

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

> Só preencha se executou o exercício bônus. Envio manual via Composer de um `POST` para `https://httpbingo.org/post` com JSON. Que campo do JSON confirma que o servidor interpretou o JSON?

#### Captura — Bônus 3.3

<!-- arraste aqui a captura do Composer, se executou (opcional) -->

**Response JSON (trecho relevante):**

```json
{
  [colar aqui]
}
```

**Resposta:** [...]

---

## Atividade 4 — Catálogo de status codes

### Captura — Atividade 4

<!-- arraste a captura aqui (lista do Fiddler com as 4 sessões principais) -->

| # | Método | URL | Status-line | `Content-Length` / `Transfer-Encoding` | Body presente? |
|---|---|---|---|---|---|
| 1 | GET | `https://httpbingo.org/status/200` | [...] | [...] | [sim/não] |
| 2 | GET | `https://httpbingo.org/redirect-to?status_code=301&url=/get` | [...] | [...] | [sim/não] |
| 3 | GET | `https://httpbingo.org/status/404` | [...] | [...] | [sim/não] |
| 4 | GET | `https://httpbingo.org/status/500` | [...] | [...] | [sim/não] |

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
| 5 | GET | `https://httpbingo.org/status/418` | [...] | [...] |
| 6 | GET | `https://httpbingo.org/status/503` | [...] | [...] |
| 7 | GET | `/cache` com `If-Modified-Since` via Composer | [...] | [...] |

**Comentário (opcional):** [...]

---

## Atividade 5 — Identificação de cabeçalhos

### Captura — Atividade 5

<!-- arraste a captura aqui (Inspectors → Headers de uma das sessões da atividade) -->

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
| `Strict-Transport-Security` | [...] | [...] | [...] |

### Pergunta 5.1

> `Content-Encoding: gzip`/`br` apareceu? Compare `Content-Length`, quando presente, com o conteúdo visível. O que explica a diferença?

**Resposta:** [...]

### Pergunta 5.2

> `Strict-Transport-Security` apareceu? Qual seu papel contra downgrades para HTTP puro?

**Resposta:** [...]

---

## Atividade 6 — HTTP vs HTTPS

### Captura — Atividade 6: HTTP puro (`http://httpbingo.org/get`)

<!-- arraste a captura HTTP aqui -->

### Captura — Atividade 6: HTTPS sem decriptação (`https://httpbingo.org/get`)

<!-- arraste a captura HTTPS sem decriptação aqui -->

### Captura — Atividade 6: HTTPS com decriptação (`https://httpbingo.org/get`)

<!-- arraste a captura HTTPS com decriptação aqui -->

### Pergunta 6.1

> No `https://httpbingo.org/get` sem decriptação, que método aparece? O que ele faz e por que existe?

**Resposta:** [...]

### Pergunta 6.2

> Com decriptação desabilitada, o que ainda é visível no HTTPS e o que está oculto?

**Resposta (visível):** [...]
**Resposta (oculto):** [...]

### Pergunta 6.3

> O que muda quando a decriptação é ativada? Que dados passam a ser inspecionáveis?

**Resposta:** [...]

### Pergunta 6.4

> Por que a técnica do Fiddler **não** funcionaria contra você se um atacante a tentasse sem instalar o certificado?

**Resposta:** [...]

---

## Atividade 7 — Cookies e sessão

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
> **Nota:** o httpbingo define cookies mínimos — normalmente apenas o atributo `Path=/` estará presente. Para cada atributo ausente, registre **não observado** e explique o comportamento padrão do navegador na sua ausência (ex.: sem `Expires`/`Max-Age` → cookie de sessão; sem `Secure` → pode ser enviado por HTTP; sem `SameSite` → o navegador aplica a política padrão da versão em uso).

**Resposta:**

| Atributo | Valor | Função | Observado? |
|---|---|---|---|
| `Path` | [...] | [...] | [...] |
| `Domain` | — | [...] | não observado |
| `Expires` | — | [...] | não observado |
| `Max-Age` | — | [...] | não observado |
| `Secure` | — | [...] | não observado |
| `HttpOnly` | — | [...] | não observado |
| `SameSite` | — | [...] | não observado |

### Pergunta 7.3

> O cookie observado trouxe `Secure`? Se não trouxe, em que cenário poderia vazar?

**Resposta:** [...]

### Pergunta 7.4

> Na aba **Inspectors → Cookies**, o cookie armazenado coincide com o campo `cookies` do JSON?

**Resposta:** [...]

---

## Atividade 8 — Manipulação com breakpoints

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

---

## Reflexão final (opcional, até 10 linhas)

> O que você aprendeu que não conhecia antes deste laboratório? Há algum cabeçalho, código de status ou comportamento que passou a olhar com mais atenção? Alguma dificuldade que recomendaria evitar para a próxima turma?

[reflexão]

---

## Encerramento — Higiene de segurança (obrigatório no Fluxo A, prazo de 24h após a aula)

### Captura — Antes da remoção

<!-- arraste aqui a captura do certmgr.msc mostrando o DO_NOT_TRUST_FiddlerRoot ainda presente -->

### Captura — Depois da remoção

<!-- arraste aqui a captura do certmgr.msc mostrando o certificado ausente -->

- [ ] *Decrypt HTTPS traffic* desabilitado no Fiddler
- [ ] Certificado `DO_NOT_TRUST_FiddlerRoot` removido do Windows (`certmgr.msc`)
- [ ] Certificado `DO_NOT_TRUST_FiddlerRoot` removido do Firefox (se aplicável)
- [ ] Fiddler fechado (porta 8888 liberada)

**Comentário do aluno sobre a importância dessa etapa** (até 5 linhas, com base na seção 4.6 do `readme.md`):

[explicar por que manter o certificado instalado é um risco]

---

## Checklist de entrega

- [ ] Todos os campos `[...]` substituídos
- [ ] Todas as capturas inseridas via arrastar-e-soltar no editor do GitHub
- [ ] 5 questões de verificação respondidas
- [ ] Encerramento concluído com as duas capturas do `certmgr.msc` (antes/depois)
- [ ] Arquivo `relatorio.md` convertido em **`SOBRENOME_NOME_RA_LAB_HTTP_FLUXOA.pdf`** com imagens embutidas
- [ ] Submetido no Microsoft Teams dentro do prazo
