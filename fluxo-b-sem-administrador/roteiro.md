# Roteiro Prático — Fluxo B (Aluno SEM privilégio de administrador)

> **Pré-requisito deste roteiro:** você **não** possui privilégio de administrador na máquina do laboratório. Toda a análise prática será feita sobre tráfego **HTTP em texto claro** — a inspeção de HTTPS é substituída por análise teórica baseada na fundamentação do [`readme.md`](../readme.md).
>
> **Escopo:** captura e inspeção de tráfego HTTP usando `httpbingo.org` na porta 80 (HTTP puro suportado nativamente). Sem instalação de certificados. Sem alteração de configurações protegidas pelo UAC.
>
> **Fundamentação teórica:** a teoria (estrutura de mensagens, métodos, status codes, cabeçalhos, papel do proxy e decriptação TLS) está no arquivo [`readme.md`](../readme.md) na raiz do repositório. Este roteiro é auto-contido nos aspectos práticos.
>
> **Tempo total previsto em sala:** ~115 minutos. Cada atividade traz uma estimativa individual (⏱).

---

## Sumário

- [1. Setup do ambiente](#1-setup-do-ambiente)
- [2. Validação rápida no início da aula](#2-validação-rápida-no-início-da-aula)
- [3. Atividades Práticas](#3-atividades-práticas)
- [4. Entrega](#4-entrega)
- [5. Encerramento](#5-encerramento)

---

## 1. Setup do ambiente

### 1.1. Acesso ao Fiddler Classic

Nas máquinas do laboratório institucional o Fiddler Classic já está **pré-instalado pelo professor** (instalação única feita com privilégio de administrador). O aluno sem privilégio apenas **abre** o programa pelo menu Iniciar — não precisa instalar nada.

Em sua máquina pessoal (sem privilégio de administrador), as opções são:

**Opção A — Fiddler já instalado pelo administrador da sua máquina:** abra normalmente, prossiga para 1.2.

**Opção B — Fiddler portátil em modo per-user:** baixar `FiddlerSetup.exe` em <https://www.telerik.com/fiddler/fiddler-classic> (exige cadastro de email) e instalar na pasta do usuário (`%LOCALAPPDATA%\Programs\Fiddler`). Em alguns ambientes essa opção evita o prompt de UAC; se o instalador insistir em pedir senha de administrador, use a Opção C.

**Opção C — HTTP Toolkit portátil (fallback se o Fiddler exigir admin para instalar):** baixar a versão portátil em <https://httptoolkit.tech/>, executar sem instalar. A opção *"Intercept a fresh Chrome"* abre um Chrome já pré-configurado para usar o proxy. **Atenção:** algumas atividades exclusivas do Fiddler (breakpoints — Atividade 8) ficam indisponíveis com HTTP Toolkit; responda-as teoricamente nesse caso.

> ⚠️ **Em nenhuma das opções, instale o certificado raiz do proxy no sistema.** Este fluxo **não** inspeciona HTTPS. Se o software oferecer a opção, recuse — ou aceite apenas a captura de HTTP em texto claro.

> 💡 **Sobre o produto.** A Telerik mantém o Fiddler Classic em modo de manutenção (sem novas funcionalidades) e empurra o Fiddler Everywhere (pago) para uso profissional. Para o nosso propósito didático, o Classic é perfeitamente adequado.

### 1.2. Configuração do Fiddler para o Fluxo B

Após abrir o Fiddler, acesse **Tools → Options → HTTPS** e **confirme que *Decrypt HTTPS traffic* está DESMARCADO**. Deixe apenas a captura de HTTP ativa.

**Interface principal:**

- **Painel da esquerda:** lista de sessões (cada linha = um par request/response).
- **Painel da direita:** abas `Inspectors`, `AutoResponder`, `Composer`, `Filters`.
- Dentro de **Inspectors**, abas horizontais separam **Request** (cima) e **Response** (baixo) com visualizações `Raw`, `Headers`, `WebForms`, `JSON`, `Cookies`, `TextView`.

### 1.3. Desabilitar promoção forçada para HTTPS no navegador

Navegadores modernos promovem silenciosamente `http://` para `https://` via HSTS e "HTTPS-First Mode". Para inspecionar HTTP puro, desabilite esse comportamento **só para a sessão do laboratório**:

- **Chrome/Edge:** abra `chrome://settings/security` e desative temporariamente a opção *"Sempre usar conexões seguras"*. Em algumas versões, também pode ser necessário abrir `chrome://flags` (ou `edge://flags`), procurar **"HTTPS-First Mode"**, **"HTTPS-Upgrades"** ou **"Always use secure connections"**, definir como **Disabled** e reiniciar o navegador. Se as flags não aparecerem ou estiverem bloqueadas por política de grupo do laboratório, use o **Firefox** como alternativa.
- **Firefox:** `about:preferences#privacy` → seção **Segurança HTTPS-Only** → escolher **"Não ativar o modo HTTPS-Only"**.

> 💡 Mesmo com essas configurações desabilitadas, sites com HSTS **pré-carregado no navegador** (p. ex. `google.com`, `facebook.com`, grandes bancos) continuarão redirecionando para HTTPS. Use apenas os sites listados na seção 1.4, que **não** estão na lista HSTS preload.

### 1.4. Sites de teste que aceitam HTTP puro

O `httpbingo.org` foi escolhido como serviço principal — ele aceita HTTP **e** HTTPS no mesmo domínio (porta 80 declarada explicitamente em sua configuração de deploy), o que simplifica todo o fluxo.

| # | URL | Uso no roteiro |
|---|---|---|
| 1 | `http://example.com` | Página HTML simples — primeira captura |
| 2 | `http://neverssl.com` | Página HTML mínima como segunda opção |
| 3 | `http://httpbingo.org/get?...` | Eco de request em JSON (HTTP puro) — base da maior parte do roteiro |
| 4 | `http://httpbingo.org/forms/post` e `/post` | Formulários e POST |
| 5 | `http://httpbingo.org/status/{200,301,404,500}` e `/redirect-to?...` | Geração controlada de códigos de status |
| 6 | `http://httpbingo.org/cache` | Resposta com `Last-Modified` (exercício bônus de 304) |
| 7 | `http://httpbingo.org/response-headers?...` | Cabeçalhos de resposta customizáveis |
| 8 | `http://httpbingo.org/gzip` | Resposta compactada em HTTP puro |
| 9 | `http://httpbingo.org/cookies/set?...` e `/cookies` | Cookies e sessão |
| 10 | `http://httpbingo.org/user-agent` | Inspeção de `User-Agent` |

> ⚠️ **Limite de conexões simultâneas.** O serviço público `httpbingo.org` aceita até 50 conexões simultâneas. Se a turma for grande, evite disparar capturas exatamente no mesmo instante. Se o serviço ficar instável, o professor pode disponibilizar um espelho local em `http://httpbin.local.sala`.

---

## 2. Validação rápida no início da aula

Antes de começar as atividades, confirme em 2 minutos:

1. Fiddler aberto e capturando (canto inferior esquerdo: **"Capturing"**; F12 alterna).
2. *Decrypt HTTPS traffic* **desmarcado** em *Tools → Options → HTTPS*.
3. Acesse `http://httpbingo.org/get`. A sessão deve aparecer com:
   - **Método `GET`** (e não `CONNECT`).
   - Aba **Response → Raw** mostrando JSON legível em texto claro.
4. Se você vir um `CONNECT`, ou se a URL na barra de endereço virar `https://`, revise a seção 1.3 (HTTPS-First Mode).

---

## 3. Atividades Práticas

> **Como registrar.** Para cada atividade, preencha a seção correspondente no arquivo [`relatorio.md`](./relatorio.md) desta mesma pasta com:
>
> - **Captura de tela arrastada diretamente para o editor web do GitHub** (não é necessário criar pasta `evidencias/`; veja a seção "Como anexar capturas" no topo do `relatorio.md`).
> - Trecho da mensagem *raw* (request ou response) conforme solicitado.
> - Respostas às questões embutidas.

### Glossário rápido

- **Request-line:** primeira linha do pedido enviado pelo cliente, no formato `MÉTODO request-target VERSÃO` (ex.: `GET /index.html HTTP/1.1`).
- **Status-line:** primeira linha da resposta do servidor, no formato `VERSÃO CÓDIGO RAZÃO` (ex.: `HTTP/1.1 200 OK`).
- **Raw:** visualização que mostra a mensagem HTTP exatamente como trafegou nos bytes do socket TCP.
- **Body:** o corpo da mensagem, separado dos cabeçalhos por uma linha em branco.

> 💡 **Sobre `httpbingo.org`.** É uma reimplementação do clássico `httpbin.org` escrita em Go, mantida ativamente, que aceita tráfego em HTTP puro (porta 80) **sem** redirecionar para HTTPS — exatamente o que precisamos. O comportamento é equivalente ao httpbin, exceto que `args` e `headers` no JSON de resposta vêm como arrays (`"id": ["42"]`), refletindo o fato de que em HTTP a mesma chave pode aparecer múltiplas vezes.

---

### Atividade 1 — Primeira captura ⏱ ~8 min

**Objetivo:** validar o ambiente e familiarizar-se com a interface.

1. Com o Fiddler capturando, abrir o navegador e acessar `http://example.com`.

> 💡 **Se o navegador redirecionar para `https://`** mesmo após a seção 1.3: no Chrome/Edge, acesse `chrome://net-internals/#hsts`, campo **"Delete domain security policies"**, digite `example.com` e clique **Delete**. Se for o `httpbingo.org` que redireciona, faça o mesmo para `httpbingo.org`. No Firefox, limpe os dados de sessão em `about:preferences#privacy → Limpar dados`.

2. Identificar a sessão correspondente na lista do Fiddler (coluna `Host` = `example.com`).

3. Selecionar a sessão e, no painel `Inspectors`, abrir a aba **Raw** (tanto em Request quanto em Response).

**Registrar no relatório:**

- Captura de tela da sessão selecionada.
- A linha inicial (*request-line*) do pedido enviado.
- A linha inicial (*status-line*) da resposta recebida.

**Pergunta 1.1:** Cite **ao menos 5 cabeçalhos** que o navegador enviou no request e explique brevemente a função de cada um.

**Pergunta 1.2:** Qual foi o `Content-Length` da resposta? Se esse cabeçalho não apareceu, registre `Transfer-Encoding`, a versão do protocolo ou outro indício observado. O corpo retornado é HTML, texto puro, JSON ou binário? Como você descobriu?

---

### Atividade 2 — Anatomia de um GET ⏱ ~12 min

**Objetivo:** dissecar uma requisição GET com *query string* e correlacioná-la à resposta.

1. Acessar no navegador: `http://httpbingo.org/get?aluno=SEU_NOME&curso=redes` — **substituindo `SEU_NOME` pelo seu próprio nome** (sem espaços; use underline se necessário, ex: `joao_silva`).
2. Localizar a sessão no Fiddler.
3. Inspecionar em **Request → Raw** e **Response → JSON**.

**Registrar no relatório:**

- *Request-line* completa, destacando método, *request-target* e versão.
- Valor exato dos cabeçalhos `Host`, `User-Agent` e `Accept`.
- Conteúdo dos campos `args`, `headers` e `origin` no JSON de resposta. **Lembre-se:** os valores virão como arrays (ex.: `"aluno": ["joao_silva"]`).

**Pergunta 2.1:** O valor do campo `origin` corresponde a qual elemento da rede? Por que normalmente vem como uma lista de IPs separados por vírgula em vez do IP local da sua máquina?

**Pergunta 2.2:** Compare o cabeçalho `User-Agent` enviado pelo navegador com o que aparece no JSON da resposta. Eles coincidem? Justifique.

**Pergunta 2.3:** Modifique a URL para `http://httpbingo.org/headers`. Liste até três cabeçalhos que o servidor vê mas que **não aparecem explicitamente** na aba Raw do request, e explique de onde eles vêm (dica: o Fiddler pode inserir alguns, o reverso proxy do Fly.io que hospeda o httpbingo pode inserir outros). Se não encontrar três, registre os que encontrar e explique por que o resultado pode variar.

---

### Atividade 3 — POST e envio de formulário ⏱ ~15 min

**Objetivo:** observar o envio de dados no corpo da mensagem e o tipo MIME associado.

1. Acessar `http://httpbingo.org/forms/post`.
2. Preencher os campos do formulário (pizza, tamanho, toppings, etc.).
3. Clicar em **Submit order**.
4. Localizar a nova sessão no Fiddler (método **POST** para `/post`).

**Registrar no relatório:**

- *Request-line* do POST.
- Valor de `Content-Type` e `Content-Length` no request.
- Corpo completo do request (aba **Request → Raw**, abaixo da linha em branco).
- Trecho do JSON de resposta mostrando o campo `form`.

**Pergunta 3.1:** Qual o formato do corpo enviado? (`application/x-www-form-urlencoded`, `multipart/form-data` ou outro?) Como esse formato codifica caracteres especiais como espaço e acentos?

**Pergunta 3.2:** Na aba **Request → WebForms** do Fiddler, o conteúdo aparece tabulado. Compare com a aba **Raw**: qual das duas visões corresponde literalmente aos bytes enviados no socket TCP?

#### Bônus 3.3 (opcional, ~8 min) — Composer com JSON

Envie manualmente um `POST` para `http://httpbingo.org/post` com `Content-Type: application/json` e o corpo `{"protocolo":"HTTP","versao":"1.1"}`. Registre a *response* completa.

**Pergunta 3.3:** Que campo do JSON de resposta confirma que o servidor recebeu e interpretou corretamente o JSON?

> **Como enviar o POST manual:**
>
> - **Fiddler Classic:** aba **Composer** → preencha a URL, selecione método `POST`, adicione o cabeçalho `Content-Type: application/json` na área de cabeçalhos e o corpo na área de texto abaixo. Clique em **Execute**.
> - **Sem Fiddler (HTTP Toolkit / mitmproxy):** abra um terminal PowerShell e execute:
>
>    ```powershell
>    Invoke-WebRequest -Uri "http://httpbingo.org/post" -Method POST -ContentType "application/json" -Body '{"protocolo":"HTTP","versao":"1.1"}' | Select-Object -ExpandProperty Content
>    ```

---

### Atividade 4 — Catálogo de status codes ⏱ ~15 min

**Objetivo:** provocar e identificar códigos de status representativos.

Para cada URL abaixo, acesse no navegador e localize a sessão no Fiddler:

| # | URL | Classe esperada |
|---|---|---|
| 1 | `http://httpbingo.org/status/200` | 2xx |
| 2 | `http://httpbingo.org/redirect-to?status_code=301&url=/get` | 3xx |
| 3 | `http://httpbingo.org/status/404` | 4xx |
| 4 | `http://httpbingo.org/status/500` | 5xx |

> No caso do `301`, o navegador provavelmente seguirá o redirecionamento e criará uma segunda sessão para o destino (`/get`). **Registre a sessão original** que retornou `301 Moved Permanently`.

**Registrar no relatório:**

- Tabela com as 4 sessões: método, URL, *status-line* completa, `Content-Length` ou `Transfer-Encoding` quando presentes, presença ou ausência de body.

**Pergunta 4.1:** Em qual dos status acima o corpo da resposta está **ausente** ou tem tamanho zero? Isso é obrigatório pela especificação ou depende do servidor?

**Pergunta 4.2:** No caso do `301`, qual cabeçalho da resposta informa ao navegador para onde ir? O que aconteceria se esse cabeçalho não estivesse presente?

**Pergunta 4.3:** Explique a diferença semântica entre `200`, `304` e `404` do ponto de vista do cache do navegador.

#### Bônus 4.4 (opcional, ~10 min) — `418`, `503` e `304 Not Modified`

Para quem terminar mais cedo:

1. Acesse `http://httpbingo.org/status/418` e `http://httpbingo.org/status/503`. Registre as duas status-lines.
2. Para provocar um `304 Not Modified` de forma controlada:
   - Acesse `http://httpbingo.org/cache` e anote o cabeçalho `Last-Modified` da resposta.
   - No **Composer** do Fiddler, envie um `GET` para `http://httpbingo.org/cache` incluindo o cabeçalho `If-Modified-Since` com o valor **exato** de `Last-Modified` (formato `Mon, 01 Jan 2024 12:00:00 GMT`).
   - Confirme que a resposta retornou `304 Not Modified` com body vazio.

---

### Atividade 5 — Identificação de cabeçalhos ⏱ ~12 min

**Objetivo:** reconhecer o propósito funcional de cada cabeçalho em tráfego real.

> 💡 Esta atividade **consolida** o que você já capturou nas Atividades 1–4. Faça apenas dois acessos adicionais (passos 1 e 2) e preencha a tabela com base em todas as suas capturas até aqui.

1. Em uma janela anônima, acesse [esta URL para provocar cabeçalhos controlados](http://httpbingo.org/response-headers?Cache-Control=max-age%3D3600&Set-Cookie=teste%3D1).

> 💡 A URL acima está percent-encoded. Decodificada, equivale a pedir os parâmetros `Cache-Control=max-age=3600` e `Set-Cookie=teste=1`. **Clique no link** em vez de digitar.

2. Acessar `http://httpbingo.org/gzip` para observar uma resposta compactada em HTTP puro.

3. Selecionar as sessões e, na aba **Inspectors → Headers**, analisar request e response.

**Preencher a tabela abaixo no relatório** consolidando dados das sessões desta atividade **e** das atividades anteriores:

| Cabeçalho | Presente em (Req/Resp)? | Valor capturado | Função em uma frase |
|---|---|---|---|
| `Host` | | | |
| `User-Agent` | | | |
| `Accept` | | | |
| `Accept-Encoding` | | | |
| `Cookie` | | | |
| `Server` | | | |
| `Content-Type` | | | |
| `Content-Encoding` | | | |
| `Set-Cookie` | | | |
| `Cache-Control` | | | |
| `Strict-Transport-Security` | Não esperado em HTTP — ver Pergunta 5.3 | — | — |

**Pergunta 5.1:** O servidor retornou `Content-Encoding: gzip` (ou `br`)? Se sim, compare o valor de `Content-Length`, quando presente, com o tamanho do conteúdo visível na aba **Response → TextView**. O que explica a diferença?

**Pergunta 5.2:** O que acontece com um request onde o cliente envia `Accept: application/json` mas o recurso só existe em `text/html`? (Pesquisar o código de status correto.)

**Pergunta 5.3:** O cabeçalho `Strict-Transport-Security` apareceu nas respostas HTTP observadas? **Por que esse cabeçalho está ausente neste fluxo?** (Dica: consulte a RFC 6797 sobre em qual protocolo o HSTS é válido.) Explique o papel do HSTS contra downgrades para HTTP puro, ainda que você não o tenha observado em tráfego real.

---

### Atividade 6 — HTTP vs HTTPS (análise sem decriptação) ⏱ ~18 min

**Objetivo:** observar a diferença entre tráfego claro e cifrado, mesmo sem poder decifrar o HTTPS.

1. Acessar `http://httpbingo.org/get` (HTTP puro — porta 80).
2. Acessar `https://httpbingo.org/get` no mesmo navegador (HTTPS — porta 443, cifrado).
3. Comparar as duas sessões no Fiddler.

> 💡 **Vantagem do mesmo host.** Como `httpbingo.org` aceita as duas portas, você compara duas conexões para o **mesmo servidor** — a única diferença é a presença ou ausência de TLS.

**Registrar no relatório:**

- Captura de tela de cada sessão com a aba **Raw** aberta.
- Para o HTTP, transcrever parte do request e da response (legíveis).
- Para o HTTPS, descrever **o que aparece** na sessão (método, host, bytes cifrados em `TextView`, aba `Inspectors → Statistics` mostrando tamanho e duração).

**Pergunta 6.1:** No caso do `https://httpbingo.org/get`, que método HTTP aparece na sessão do Fiddler? Explique o que esse método faz e por que ele existe. (Dica: `CONNECT` é definido na RFC 9110 §9.3.6.)

**Pergunta 6.2:** Faça uma **tabela comparativa** dos campos visíveis ao Fiddler em cada caso:

| Campo | Visível em HTTP? | Visível em HTTPS (sem decriptação)? |
|---|---|---|
| Método | | |
| URL completa (path + query) | | |
| Cabeçalhos de request | | |
| Corpo de request | | |
| Status code | | |
| Cabeçalhos de response | | |
| Corpo de response | | |
| Host (via SNI, no `CONNECT`) | | |
| IP e porta de destino | | |

**Pergunta 6.3 (teórica):** Descreva em um parágrafo, com base na seção 4.6 do [`readme.md`](../readme.md), o que você **veria** no Fiddler se tivesse privilégio de administrador e pudesse habilitar *Decrypt HTTPS traffic*. Indique as telas e abas (Inspectors → Raw, Response → JSON/TextView, etc.) onde cada informação apareceria, e **por que** essa inspeção exige a instalação de um certificado raiz no sistema.

**Pergunta 6.4:** Do ponto de vista de segurança, explique por que a técnica de decriptação usada pelos *debugging proxies* **não** funcionaria contra um usuário se um atacante a tentasse sem instalar o certificado na máquina-alvo. Relacione com o conceito de "cooperação do usuário" da seção 4.6 do [`readme.md`](../readme.md).

---

### Atividade 7 — Cookies e sessão ⏱ ~12 min

**Objetivo:** rastrear o ciclo de vida de um cookie ao longo de múltiplas requisições.

1. Em uma janela anônima, acessar `http://httpbingo.org/cookies/set?disciplina=redes&professor=claudio`.
2. Observar o **`Set-Cookie`** na resposta. Anotar os valores.
3. O servidor retorna um `302` que o navegador segue automaticamente para `http://httpbingo.org/cookies`. Isso gera **duas sessões** no Fiddler: a primeira com `Set-Cookie`, a segunda com `Cookie`.
4. Observar o cabeçalho **`Cookie`** enviado na segunda requisição.
5. Recarregar mais duas vezes a página `http://httpbingo.org/cookies`.

**Registrar no relatório:**

- Sequência (numerada) das sessões capturadas.
- Valores dos cabeçalhos `Set-Cookie` e `Cookie` em cada uma.

**Pergunta 7.1:** O cabeçalho `Set-Cookie` só aparece uma vez ou em toda requisição? Justifique.

**Pergunta 7.2:** Que atributos o `Set-Cookie` trouxe (`Path`, `Domain`, `Expires`, `Max-Age`, `Secure`, `HttpOnly`, `SameSite`)? Para cada um presente, explique brevemente sua função. Se algum atributo da lista não apareceu, registre como **não observado**.

> **Nota:** o httpbingo define cookies mínimos — apenas o atributo `Path=/` estará presente nas respostas deste exercício. Registre todos os demais atributos listados como **não observado** e, para cada um, explique o comportamento padrão adotado pelo navegador na ausência desse atributo (ex.: sem `Expires`/`Max-Age`, o cookie é de sessão; sem `Secure`, pode ser enviado por HTTP; sem `SameSite`, o navegador aplica a política padrão da versão em uso).

**Pergunta 7.3:** O atributo `Secure` **pode** aparecer em um cookie recebido por HTTP puro como neste exercício? Qual o comportamento esperado do navegador se um cookie `Secure` **fosse** enviado nesta conexão? Relacione com o fato de que todo o tráfego desta atividade é **visível em texto claro** ao Fiddler (e, portanto, a qualquer observador na rede).

**Pergunta 7.4:** Na aba **Inspectors → Cookies** do Fiddler, compare o cookie armazenado com o que o servidor devolve no campo `cookies` do JSON da resposta. Eles coincidem?

---

### Atividade 8 — Manipulação com *breakpoints* ⏱ ~18 min

**Objetivo:** compreender o proxy como agente ativo, capaz de modificar tráfego em trânsito.

> **Atividade exclusiva do Fiddler Classic.** As etapas de breakpoint abaixo dependem do menu **Rules → Automatic Breakpoints**, disponível apenas no Fiddler Classic. Usuários com HTTP Toolkit podem pular para a Atividade 9 e responder às questões 8.1 e 8.2 de forma teórica.

1. **Antes de acionar o atalho:** clique uma vez na lista de sessões do Fiddler para garantir que a **janela do Fiddler** tenha o foco. Sem foco no Fiddler, F11 abre tela cheia do navegador em vez do breakpoint.

2. No Fiddler, habilitar **breakpoint de request**: menu **Rules → Automatic Breakpoints → Before Requests** (ou F11 com foco no Fiddler).

3. No navegador, acessar `http://httpbingo.org/user-agent`.

4. A sessão pausará no Fiddler com um ícone vermelho.

5. Na aba **Inspectors → Request → Raw**, editar o cabeçalho `User-Agent` para um valor inventado, por exemplo:
   `User-Agent: LaboratorioRedes/1.0 (Aluno NOME)`

6. Clicar em **Run to Completion** (botão verde no topo do inspector).

7. Na resposta, observar o JSON retornado.

**Registrar no relatório:**

- Captura de tela da edição do request.
- Trecho do JSON de resposta mostrando o campo `user-agent`.

**Pergunta 8.1:** O servidor tem como detectar que o `User-Agent` foi forjado? Discuta.

**Pergunta 8.2:** Repita o exercício, mas desta vez habilite **breakpoint de response** (**Rules → Automatic Breakpoints → After Responses**). Acesse `http://httpbingo.org/status/200` e, quando o Fiddler pausar, edite a *status-line* para `HTTP/1.1 404 Not Found`. Libere. O que o navegador exibe? Comente sobre o papel do proxy como *man-in-the-middle*.

**Pergunta 8.3:** Desabilite todos os breakpoints (**Rules → Automatic Breakpoints → Disabled**, atalho **Shift+F11**) ao terminar.

---

### Atividade 9 — Redirecionamento HTTP → HTTPS (exclusiva do Fluxo B) ⏱ ~10 min

**Objetivo:** observar, no próprio Fiddler, um redirecionamento controlado de HTTP para HTTPS e relacioná-lo aos mecanismos de segurança discutidos na teoria.

1. Com os breakpoints desabilitados, acessar no navegador: `http://httpbingo.org/redirect-to?status_code=301&url=https%3A%2F%2Fhttpbingo.org%2Fget`.

> 💡 Se o navegador não chegar a fazer a requisição em HTTP puro (pulando direto para HTTPS), execute `chrome://net-internals/#hsts` → **Delete domain security policies** para `httpbingo.org` e tente novamente.

2. Localizar no Fiddler a sessão original para `httpbingo.org` que retornou `301 Moved Permanently`.
3. Observar que o navegador pode criar uma segunda sessão para o destino `https://httpbingo.org/get`; para esta atividade, registre a sessão original do redirecionamento.

**Registrar no relatório:**

- Captura de tela da sessão.
- *Status-line* completa da resposta.
- Cabeçalho `Location` da resposta (obrigatório em qualquer resposta `3xx`).

**Pergunta 9.1:** Qual foi o **código de status** retornado e qual **cabeçalho** direcionou o navegador para `https://`?

**Pergunta 9.2:** Com base na fundamentação teórica do [`readme.md`](../readme.md) (seções 4.5 e Anexo A), além do redirecionamento 3xx, **qual outro mecanismo** faz o navegador passar a forçar HTTPS em visitas futuras, mesmo que o usuário digite `http://`? Responda citando o cabeçalho de response envolvido e a RFC que o define.

**Pergunta 9.3:** Se o servidor enviasse o cabeçalho citado em 9.2 por uma resposta servida via **HTTP puro** (porta 80), o navegador deveria obedecer? Justifique com base na RFC.

---

## 4. Entrega

### O que entregar

Um único arquivo PDF chamado **`SOBRENOME_NOME_RA_LAB_HTTP_FLUXOB.pdf`** (exemplo: `silva_joao_2023123_LAB_HTTP_FLUXOB.pdf`, sem espaços, sem acentos, tudo minúsculo), gerado a partir do `relatorio.md` preenchido.

> 💡 **Sem zip, sem pasta `evidencias/`.** As capturas de tela são arrastadas diretamente para o editor do GitHub e ficam embutidas no `relatorio.md`. Quando o PDF é gerado, o conversor baixa as imagens automaticamente e as embute no arquivo final.

### Como gerar o PDF a partir do `relatorio.md`

| Ferramenta | Quando usar | Como usar |
|---|---|---|
| **Markdown to PDF** (md2pdf) — site público gratuito (**recomendado**) | Funciona em qualquer navegador, sem cadastro, sem instalação. Faz download das imagens hospedadas no GitHub automaticamente. | Abrir <https://md2pdf.netlify.app>, copiar o conteúdo do `relatorio.md` direto do GitHub (botão **Raw → Copy raw file**) e colar. Clicar em **Convert**. |
| **Markdown PDF** — extensão VS Code | Se você já clonou o repositório localmente. | Instalar a extensão *"Markdown PDF"* (autor: yzane). Com o `relatorio.md` aberto, `Ctrl+Shift+P` → *"Markdown PDF: Export (pdf)"*. |
| **Dillinger** | Alternativa online. | <https://dillinger.io> → *Import From → Markdown File* → *Export As → PDF*. |

> 💡 **Garantia de internet.** O gerador de PDF precisa ter acesso à internet no momento da conversão para baixar as imagens hospedadas pelo GitHub. Não gere o PDF offline.

### Como entregar

Submeter o arquivo `SOBRENOME_NOME_RA_LAB_HTTP_FLUXOB.pdf` no **Microsoft Teams**, atividade correspondente, até a data definida em aula.

### Critérios de avaliação

| Critério | Peso |
|---|---|
| Exatidão técnica das respostas | 40% |
| Evidências coerentes (capturas legíveis e pertinentes) | 25% |
| Profundidade da análise das questões de verificação | 25% |
| Organização e clareza do relatório | 10% |

---

## 5. Encerramento

1. **Reabilitar** o HTTPS-First Mode / HTTPS-Only Mode no navegador (passos inversos de 1.3), para que seu dia-a-dia volte a priorizar conexões seguras.
2. **Fechar o Fiddler** (ou HTTP Toolkit / mitmproxy) para liberar a porta de proxy e remover qualquer configuração de proxy do navegador.
3. **Anexar ao relatório** um parágrafo explicando **por que**, neste fluxo, a etapa de remoção de certificado é **dispensável** — e **por que**, no fluxo do aluno administrador, ela seria **obrigatória**. Use a seção 4.6 do [`readme.md`](../readme.md) como referência.
