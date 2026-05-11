# Roteiro Prático — Fluxo A (Aluno COM privilégio de administrador)

> **Pré-requisito deste roteiro:** você possui privilégio de administrador na máquina do laboratório (ou conhece a senha de administrador para o prompt do UAC).
>
> **Escopo:** executa a atividade **na íntegra**, incluindo captura e inspeção de tráfego **HTTP e HTTPS** (com decriptação TLS via certificado raiz do Fiddler).
>
> **Fundamentação teórica:** a teoria (estrutura de mensagens, métodos, status codes, cabeçalhos, papel do proxy e decriptação TLS) está no arquivo [`readme.md`](../readme.md) na raiz do repositório. Este roteiro é auto-contido nos aspectos práticos.
>
> **Tempo total previsto em sala:** ~110 minutos. Cada atividade traz uma estimativa individual (⏱). Se você terminar antes, faça os exercícios marcados como **bônus**.

---

## Sumário

- [1. Pré-aula: setup do ambiente](#1-Setup-do-ambiente)
- [2. Validação rápida no início da aula](#2-validação-rápida-no-início-da-aula)
- [3. Atividades Práticas](#3-atividades-práticas)
- [4. Entrega](#4-entrega)
- [5. Encerramento (pós-aula, 24h)](#5-encerramento-pós-aula-24h)

---

## 1. Setup do ambiente

### 1.1. Instalação do Fiddler Classic

Nas máquinas do laboratório institucional o Fiddler Classic já está pré-instalado pelo professor. Para a sua máquina pessoal (recomendado para o Fluxo A, pois exige administrador):

**Opção A (recomendada, sem cadastro):** abrir o PowerShell e executar:

```powershell
winget install Telerik.Fiddler.Classic
```

**Opção B (instalador web Telerik, exige email):** baixar em <https://www.telerik.com/fiddler/fiddler-classic>, executar `FiddlerSetup.exe` como administrador.

> 💡 **Sobre o produto.** A Telerik mantém o Fiddler Classic em modo de manutenção (sem novas funcionalidades) e empurra o Fiddler Everywhere (pago) para uso profissional. Para o nosso propósito didático, o Classic é perfeitamente adequado.

### 1.2. Ativação da decriptação HTTPS e instalação do certificado raiz

1. Abrir o Fiddler Classic **como administrador** (clique direito → *Executar como administrador*).
2. Menu **Tools → Options → HTTPS**.
3. Marcar:
   - ☑ **Capture HTTPS CONNECTs**
   - ☑ **Decrypt HTTPS traffic**
4. Aceitar a instalação do certificado raiz (`DO_NOT_TRUST_FiddlerRoot`) quando solicitado. Confirmar na caixa de diálogo do Windows (clicar **Sim** no prompt de confiança).
5. **Firefox** (se usar): o Firefox tem armazenamento de certificados próprio. Exportar o certificado em *Actions → Export Root Certificate to Desktop* e importá-lo em `about:preferences#privacy → Ver Certificados → Importar → FiddlerRoot.cer`, marcando "Confiar nesta CA para identificar sites".

### 1.3. Teste do ambiente

Com o Fiddler capturando, acessar `https://httpbingo.org/get` no navegador. No Fiddler, a sessão deve aparecer com:

- Ícone de **cadeado aberto** (indicando HTTPS decifrado).
- Em **Inspectors → Response → JSON**, um JSON legível contendo campos `args`, `headers`, `origin`, `url`.

Se o Fiddler ainda mostrar apenas `CONNECT` com conteúdo cifrado, repita 1.2 e confirme a instalação do certificado.

> ⚠️ **Aviso ético.** O certificado raiz do Fiddler permite a decriptografia de **qualquer** conexão HTTPS feita por esta máquina enquanto estiver instalado. **Não esqueça de removê-lo** ao final do laboratório (seção 5).

---

## 2. Validação rápida no início da aula

Antes de começar as atividades, confirme em 2 minutos:

1. Fiddler aberto e capturando (canto inferior esquerdo: **"Capturing"**; F12 alterna).
2. Acesse `https://httpbingo.org/status/200`. Sessão deve aparecer decifrada (cadeado aberto).
3. Se `httpbingo.org` estiver instável (timeout, ou demora >5s), avise o professor — pode haver espelho local em `http://httpbin.local.sala`.

**Interface principal do Fiddler:**

- **Painel da esquerda:** lista de sessões (cada linha = um par request/response).
- **Painel da direita:** abas `Inspectors`, `AutoResponder`, `Composer`, `Filters`.
- Dentro de **Inspectors**, abas horizontais separam **Request** (cima) e **Response** (baixo) com visualizações `Raw`, `Headers`, `WebForms`, `JSON`, `Cookies`, `TextView`.

> 💡 **Idioma da interface.** O Fiddler Classic existe somente em inglês. Todos os menus, abas e botões mencionados neste roteiro estão em inglês exatamente como aparecem na tela.

---

## 3. Atividades Práticas

> **Como registrar.** Para cada atividade, preencha a seção correspondente no arquivo [`relatorio.md`](./relatorio.md) desta mesma pasta com:
>
> - **Captura de tela arrastada diretamente para o editor web do GitHub** (não é necessário criar pasta `evidencias/`; veja a seção "Como anexar capturas" no topo do `relatorio.md`).
> - Trecho da mensagem *raw* (request ou response) conforme solicitado.
> - Respostas às questões embutidas.
>
> 💡 **Limpe a lista de sessões do Fiddler entre uma atividade e outra** com **Edit → Remove → All Sessions** (atalho `Ctrl+X`). Isso evita confusão na hora de localizar a sessão alvo.

### Glossário rápido

- **Request-line:** primeira linha do pedido enviado pelo cliente, no formato `MÉTODO request-target VERSÃO` (ex.: `GET /index.html HTTP/1.1`).
- **Status-line:** primeira linha da resposta do servidor, no formato `VERSÃO CÓDIGO RAZÃO` (ex.: `HTTP/1.1 200 OK`).
- **Raw:** visualização que mostra a mensagem HTTP exatamente como trafegou nos bytes do socket TCP — é a fonte de verdade quando outras visualizações (Headers, JSON, WebForms) parecem divergir.
- **Body:** o corpo da mensagem, separado dos cabeçalhos por uma linha em branco.

> 💡 **Sobre `httpbingo.org`.** É uma reimplementação do clássico `httpbin.org` escrita em Go, mantida ativamente. Para os endpoints que usamos, o comportamento é idêntico — exceto que os campos `args` e `headers` no JSON de resposta retornam os valores como **arrays** (`"id": ["42"]` em vez de `"id": "42"`), refletindo o fato de que em HTTP a mesma chave pode aparecer múltiplas vezes.

---

### Atividade 1 — Primeira captura ⏱ ~8 min

**Objetivo:** validar o ambiente e familiarizar-se com a interface.

1. Com o Fiddler capturando, abrir o navegador e acessar `http://example.com`.
2. Identificar a sessão correspondente na lista do Fiddler (coluna `Host` = `example.com`).
3. Selecionar a sessão e, no painel `Inspectors`, abrir a aba **Raw** (tanto em Request quanto em Response).

**Registrar no relatório:**

- Captura de tela da sessão selecionada.
- A linha inicial (*request-line*) do pedido enviado.
- A linha inicial (*status-line*) da resposta recebida.

**Pergunta 1.1:** Cite **ao menos 5 cabeçalhos** que o navegador enviou no request e explique brevemente a função de cada um.

> 💡 O número total varia entre navegadores e versões (Chrome envia o cluster `Sec-CH-*`, Firefox não). Não se preocupe com a contagem exata — foque na função pedagógica.

**Pergunta 1.2:** Qual foi o `Content-Length` da resposta? Se esse cabeçalho não apareceu, registre `Transfer-Encoding`, a versão do protocolo ou outro indício observado. O corpo retornado é HTML, texto puro, JSON ou binário? Como você descobriu?

---

### Atividade 2 — Anatomia de um GET ⏱ ~12 min

**Objetivo:** dissecar uma requisição GET com *query string* e correlacioná-la à resposta.

1. Acessar no navegador: `https://httpbingo.org/get?aluno=SEU_NOME&curso=redes` — **substituindo `SEU_NOME` pelo seu próprio nome** (sem espaços; use underline se necessário, ex: `joao_silva`). Isso identifica sua captura no relatório.
2. Localizar a sessão no Fiddler.
3. Inspecionar em **Request → Raw** e **Response → JSON**.

**Registrar no relatório:**

- *Request-line* completa, destacando método, *request-target* e versão.
- Valor exato dos cabeçalhos `Host`, `User-Agent` e `Accept`.
- Conteúdo dos campos `args`, `headers` e `origin` no JSON de resposta. **Lembre-se:** os valores virão como arrays (ex.: `"aluno": ["joao_silva"]`).

**Pergunta 2.1:** O valor do campo `origin` no JSON retornado corresponde a qual elemento da rede? Por que normalmente vem como uma lista de IPs separados por vírgula em vez do IP local da sua máquina?

**Pergunta 2.2:** Compare o cabeçalho `User-Agent` enviado pelo navegador com o que aparece no JSON da resposta. Eles coincidem? Se **não** coincidirem, qual elemento intermediário (proxy escolar, antivírus, extensão de navegador, o próprio Fiddler) poderia tê-lo modificado?

**Pergunta 2.3:** Modifique a URL para `https://httpbingo.org/headers`. Liste até três cabeçalhos que o servidor vê mas que **não aparecem explicitamente** na aba Raw do request, e explique de onde eles vêm (dica: Fiddler pode inserir alguns, o reverso proxy do Fly.io que hospeda o httpbingo pode inserir outros). Se não encontrar três, registre os que encontrar e explique por que o resultado pode variar.

---

### Atividade 3 — POST e envio de formulário ⏱ ~15 min

**Objetivo:** observar o envio de dados no corpo da mensagem e o tipo MIME associado.

1. Acessar `https://httpbingo.org/forms/post`.
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

> 💡 Faça este exercício se já tiver terminado as atividades 1–4 com folga. Caso contrário, deixe para o fim e pule para a Atividade 4.

Usando a aba **Composer** do Fiddler, envie manualmente um `POST` para `https://httpbingo.org/post` com `Content-Type: application/json` e o corpo `{"protocolo":"HTTP","versao":"1.1"}`. Registre a *response* completa.

**Pergunta 3.3:** Que campo do JSON de resposta confirma que o servidor recebeu e interpretou corretamente o JSON?

> 💡 **Dica.** Verifique especificamente o campo `json` da resposta. Se ele estiver `null` e o conteúdo aparecer apenas em `data` como string, isso indica que o `Content-Type` foi enviado errado (ou ficou em branco) — o servidor recebeu os bytes mas não os interpretou como JSON.

> **Como enviar o POST manual no Composer do Fiddler Classic:**
>
> 1. Abrir a aba **Composer** (no painel direito).
> 2. No menu suspenso de método, selecionar **POST**.
> 3. No campo de URL, digitar `https://httpbingo.org/post`.
> 4. Na **caixa de texto de cabeçalhos** (acima da caixa do body), em uma linha própria, digitar exatamente: `Content-Type: application/json` (já existirão um `User-Agent`/`Host` — não os apague).
> 5. Na **caixa do Request Body** (abaixo dos cabeçalhos), colar: `{"protocolo":"HTTP","versao":"1.1"}` — sem aspas extras, sem quebras de linha.
> 6. Clicar em **Execute** (botão à direita).

---

### Atividade 4 — Catálogo de status codes ⏱ ~15 min

**Objetivo:** provocar e identificar códigos de status representativos.

Para cada URL abaixo, acesse no navegador e localize a sessão no Fiddler:

| # | URL | Classe esperada |
|---|---|---|
| 1 | `https://httpbingo.org/status/200` | 2xx |
| 2 | `https://httpbingo.org/redirect-to?status_code=301&url=/get` | 3xx |
| 3 | `https://httpbingo.org/status/404` | 4xx |
| 4 | `https://httpbingo.org/status/500` | 5xx |

> No caso do `301`, o navegador provavelmente seguirá o redirecionamento e criará uma segunda sessão para o destino (`/get`). **Registre a sessão original** que retornou `301 Moved Permanently`.

**Registrar no relatório:**

- Tabela com as 4 sessões: método, URL, *status-line* completa, `Content-Length` ou `Transfer-Encoding` quando presentes, presença ou ausência de body.

**Pergunta 4.1:** Em qual dos status acima o corpo da resposta está **ausente** ou tem tamanho zero? Isso é obrigatório pela especificação ou depende do servidor?

**Pergunta 4.2:** No caso do `301`, qual cabeçalho da resposta informa ao navegador para onde ir? O que aconteceria se esse cabeçalho não estivesse presente?

**Pergunta 4.3:** Explique a diferença semântica entre `200`, `304` e `404` do ponto de vista do cache do navegador. (Sobre o `304`, veja o exercício bônus abaixo.)

#### Bônus 4.4 (opcional, ~10 min) — `418`, `503` e `304 Not Modified`

Para quem terminar mais cedo:

1. Acesse `https://httpbingo.org/status/418` e `https://httpbingo.org/status/503`. Registre as duas status-lines.
2. Para provocar um `304 Not Modified` de forma controlada:
   - Acesse `https://httpbingo.org/cache` e anote o cabeçalho `Last-Modified` da resposta (visível em **Inspectors → Response → Headers**).
   - No **Composer** do Fiddler, envie um `GET` para `https://httpbingo.org/cache` incluindo o cabeçalho `If-Modified-Since` com o valor **exato** de `Last-Modified` observado (formato `Mon, 01 Jan 2024 12:00:00 GMT`).
   - Confirme que a resposta retornou `304 Not Modified` com body vazio.

> 💡 O endpoint `/cache` do httpbingo é determinístico: retorna `200` se nenhum header condicional vier, e `304` se vier `If-Modified-Since` ou `If-None-Match`. Se você não obteve `304`, verifique se copiou o `Last-Modified` exatamente, incluindo aspas e espaços.

---

### Atividade 5 — Identificação de cabeçalhos ⏱ ~12 min

**Objetivo:** reconhecer o propósito funcional de cada cabeçalho em tráfego real.

> 💡 Esta atividade **consolida** o que você já capturou nas Atividades 1–4. Faça apenas dois acessos adicionais (passos 1 e 2 abaixo) e preencha a tabela com base em todas as suas capturas até aqui.

1. Em uma janela anônima / privativa, acesse [esta URL para provocar cabeçalhos controlados](https://httpbingo.org/response-headers?Cache-Control=max-age%3D3600&Set-Cookie=teste%3D1&Strict-Transport-Security=max-age%3D31536000).

> 💡 A URL acima está percent-encoded. Decodificada, equivale a pedir os parâmetros `Cache-Control=max-age=3600`, `Set-Cookie=teste=1` e `Strict-Transport-Security=max-age=31536000`. **Clique no link acima** em vez de digitar — copiar a versão decodificada quebra a query string.

2. Acessar `https://httpbingo.org/gzip` para observar uma resposta compactada.

3. Selecionar as sessões correspondentes e, na aba **Inspectors → Headers**, analisar request e response.

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
| `Strict-Transport-Security` | | | |

**Pergunta 5.1:** O servidor retornou `Content-Encoding: gzip` (ou `br`)? Se sim, compare o valor de `Content-Length`, quando presente, com o tamanho do conteúdo visível na aba **Response → TextView**. O que explica a diferença?

**Pergunta 5.2:** O cabeçalho `Strict-Transport-Security` apareceu na resposta? Qual seu papel em relação a downgrades para HTTP puro? (Veja RFC 6797.)

---

### Atividade 6 — HTTP vs HTTPS ⏱ ~18 min

**Objetivo:** observar, lado a lado, a diferença entre tráfego claro e cifrado.

1. **Desabilitar** temporariamente a decriptação HTTPS: *Tools → Options → HTTPS → desmarcar Decrypt HTTPS traffic*. Clicar OK.
2. Acessar `http://httpbingo.org/get` (HTTP puro — note a porta 80 sem TLS).
3. Acessar `https://httpbingo.org/get` (HTTPS cifrado, sem decriptação).
4. Comparar ambas as sessões no Fiddler.

**Registrar no relatório:**

- Captura de tela de cada sessão com a aba **Raw** aberta.
- O método, o host e o que é (ou não é) visível em cada caso.

**Pergunta 6.1:** No caso do `https://httpbingo.org/get` sem decriptação, que método HTTP aparece na sessão do Fiddler? Explique o que esse método faz e por que ele existe.

**Pergunta 6.2:** Com a decriptação desabilitada, quais informações sobre o tráfego HTTPS **ainda** são visíveis para o Fiddler e quais estão ocultas?

5. **Reabilitar** a decriptação e repetir o acesso a `https://httpbingo.org/get`.

**Pergunta 6.3:** O que muda na interface do Fiddler quando a decriptação está ativa? Quais dados passam a ser inspecionáveis?

**Pergunta 6.4:** Do ponto de vista de segurança, explique por que a técnica usada pelo Fiddler para decifrar HTTPS **não** funcionaria contra você se um atacante a tentasse sem instalar o certificado na sua máquina.

> 💡 **Vantagem do mesmo host.** Como `httpbingo.org` aceita HTTP e HTTPS no mesmo domínio, a comparação fica mais limpa que comparar sites diferentes.

---

### Atividade 7 — Cookies e sessão ⏱ ~12 min

**Objetivo:** rastrear o ciclo de vida de um cookie ao longo de múltiplas requisições.

1. Em uma janela anônima, acessar `https://httpbingo.org/cookies/set?disciplina=redes&professor=claudio`.
2. Observar o **`Set-Cookie`** na resposta. Anotar os valores.
3. Seguir o redirecionamento automático para `https://httpbingo.org/cookies` (pode ser necessário um clique).
4. Observar o cabeçalho **`Cookie`** enviado nesta segunda requisição.
5. Recarregar mais duas vezes a página `https://httpbingo.org/cookies`.

**Registrar no relatório:**

- Sequência (numerada) das sessões capturadas.
- Valores dos cabeçalhos `Set-Cookie` e `Cookie` em cada uma.

**Pergunta 7.1:** O cabeçalho `Set-Cookie` só aparece uma vez ou em toda requisição? Justifique.

**Pergunta 7.2:** Que atributos o `Set-Cookie` trouxe (`Path`, `Domain`, `Expires`, `Max-Age`, `Secure`, `HttpOnly`, `SameSite`)? Para cada um presente, explique brevemente sua função. Se algum atributo da lista não apareceu, registre como **não observado**.

**Pergunta 7.3:** O cookie observado trouxe o atributo `Secure`? Se não trouxe, em que cenário ele poderia vazar? (Relacione com a Atividade 6.)

**Pergunta 7.4:** Na aba **Inspectors → Cookies** do Fiddler, compare o cookie armazenado com o que o servidor devolve no campo `cookies` do JSON da resposta. Eles coincidem?

---

### Atividade 8 — Manipulação com *breakpoints* ⏱ ~18 min

**Objetivo:** compreender o proxy como agente ativo, capaz de modificar tráfego em trânsito.

1. **Antes de acionar o atalho:** clique uma vez na lista de sessões do Fiddler para garantir que a **janela do Fiddler** tenha o foco. Sem foco no Fiddler, F11 abre tela cheia do navegador em vez do breakpoint.

2. No Fiddler, habilitar **breakpoint de request**: menu **Rules → Automatic Breakpoints → Before Requests** (ou F11 com foco no Fiddler).

3. No navegador, acessar `https://httpbingo.org/user-agent`.

4. A sessão pausará no Fiddler com um ícone vermelho.

5. Na aba **Inspectors → Request → Raw** (clique dentro da área de texto para habilitar a edição), localizar a linha `User-Agent: ...` e substituir o valor por algo inventado, por exemplo:
   `User-Agent: LaboratorioRedes/1.0 (Aluno NOME)`

6. Clicar em **Run to Completion** (botão verde no topo do inspector).

7. Na resposta, observar o JSON retornado.

**Registrar no relatório:**

- Captura de tela da edição do request.
- Trecho do JSON de resposta mostrando o campo `user-agent`.

**Pergunta 8.1:** O servidor tem como detectar que o `User-Agent` foi forjado? Discuta.

**Pergunta 8.2:** Repita o exercício, mas desta vez habilite **breakpoint de response** (**Rules → Automatic Breakpoints → After Responses**). Acesse `https://httpbingo.org/status/200` e, quando o Fiddler pausar, vá em **Inspectors → Response → Raw** e edite a primeira linha (a *status-line*) para `HTTP/1.1 404 Not Found`. Clique em **Run to Completion**. Descreva **o que VOCÊ observou** no navegador (o comportamento varia entre Chrome, Edge e Firefox — qualquer descrição honesta da tela renderizada é válida). Comente sobre o papel do proxy como *man-in-the-middle*: o servidor original retornou `200`, mas o navegador acreditou em `404`.

**Pergunta 8.3:** Desabilite todos os breakpoints (**Rules → Automatic Breakpoints → Disabled**, atalho **Shift+F11**) ao terminar.

---

## 4. Entrega

### O que entregar

Um único arquivo PDF chamado **`SOBRENOME_NOME_RA_LAB_HTTP_FLUXOA.pdf`** (exemplo: `silva_joao_2023123_LAB_HTTP_FLUXOA.pdf`, sem espaços, sem acentos, tudo minúsculo), gerado a partir do `relatorio.md` preenchido.

> 💡 **Sem zip, sem pasta `evidencias/`.** As capturas de tela são arrastadas diretamente para o editor do GitHub e ficam embutidas no `relatorio.md`. Quando o PDF é gerado, o conversor baixa as imagens automaticamente e as embute no arquivo final.

### Como gerar o PDF a partir do `relatorio.md`

| Ferramenta | Quando usar | Como usar |
|---|---|---|
| **Markdown to PDF** (md2pdf) — site público gratuito (**recomendado**) | Funciona em qualquer navegador, sem cadastro, sem instalação. Faz download das imagens hospedadas no GitHub automaticamente. | Abrir <https://md2pdf.netlify.app>, copiar o conteúdo do `relatorio.md` direto do GitHub (botão **Raw → Copy raw file**) e colar. Clicar em **Convert**. Salvar o PDF gerado. |
| **Markdown PDF** — extensão VS Code | Você já está com o repositório clonado e o VS Code aberto. Requer instalação local. | Instalar a extensão *"Markdown PDF"* (autor: yzane). Com o `relatorio.md` aberto, pressionar `Ctrl+Shift+P` → *"Markdown PDF: Export (pdf)"*. |
| **Dillinger** | Alternativa online se md2pdf estiver fora do ar. | <https://dillinger.io> → *Import From → Markdown File* → *Export As → PDF*. |

> 💡 **Garantia de internet.** O gerador de PDF precisa ter acesso à internet no momento da conversão para baixar as imagens hospedadas pelo GitHub. Não gere o PDF offline.

### Como entregar

Submeter o arquivo `SOBRENOME_NOME_RA_LAB_HTTP_FLUXOA.pdf` no **Microsoft Teams**, atividade correspondente, até a data definida em aula.

### Critérios de avaliação

| Critério | Peso |
|---|---|
| Exatidão técnica das respostas | 40% |
| Evidências coerentes (capturas legíveis e pertinentes) | 25% |
| Profundidade da análise das questões de verificação | 25% |
| Organização e clareza do relatório | 10% |

---

## 5. Encerramento (pós-aula, 24h)

> ⚠️ **Tarefa obrigatória, com prazo de 24 horas após o fim da aula.** A remoção do certificado raiz **não** consome tempo de sala — mas é parte integral da avaliação do Fluxo A e tem peso na nota.

Estes passos eliminam um risco de segurança real: enquanto o certificado raiz do Fiddler estiver instalado, qualquer processo com acesso à chave privada poderia interceptar conexões HTTPS desta máquina sem alerta.

1. **Desabilitar a decriptação HTTPS:** *Tools → Options → HTTPS → desmarcar Decrypt HTTPS traffic* → OK.
2. **Remover o certificado raiz:**
   - **Windows:** `Win+R` → `certmgr.msc` → *Autoridades de Certificação Raiz Confiáveis → Certificados* → localizar **`DO_NOT_TRUST_FiddlerRoot`** → clique direito → *Excluir*.
   - **Firefox:** `about:preferences#privacy` → *Ver Certificados* → aba *Autoridades* → selecionar `DO_NOT_TRUST_FiddlerRoot` → *Excluir ou desconfiar*.
3. **Fechar o Fiddler** para liberar a porta 8888.
4. **Anexar ao relatório**, na seção *Encerramento*, **duas capturas** do `certmgr.msc`: uma **antes** da remoção (mostrando o certificado presente) e uma **depois** (mostrando o certificado ausente). Isso comprova que você efetivamente fez a remoção.

> ⚠️ **Por que duas capturas.** Pedir só a captura "depois" verifica apenas que o certificado não está lá no momento da foto — não comprova que você de fato o removeu. As duas capturas (antes/depois) tornam a evidência inequívoca.
