# Clara — Especificação de Voz e Síntese de Fala
## Guia Técnico para Motores TTS · Versão 1.0

> **Referência cruzada:** `config/persona-clara.json` · `config/clara_voz_config.json`
> **Uso:** carregar antes de qualquer geração de áudio pela Clara. Este documento define
> os parâmetros exatos de síntese, as regras de marcação fonética e os critérios
> de qualidade para aprovação do áudio final.

---

## 1. PERFIL DA PERSONA DE VOZ

| Atributo | Especificação | Justificativa |
|---|---|---|
| **Gênero** | Feminino | Alinhado com a persona visual aprovada |
| **Idade aparente** | 27 – 32 anos | Maturidade sem distância — amiga antenada, não jovem inexperiente |
| **Sotaque** | Português do Brasil neutro · leve dinamismo urbano paulistano | Máxima inteligibilidade nacional · sem regionalismo que exclua audiência |
| **Dicção** | Clara, ligeiramente informal — sem erros, sem formalidade excessiva | Imita a fala coloquial culta que o público reconhece como "igual a mim" |
| **Energia geral** | 7,5 / 10 | Consistentemente animada sem ser cansativa · pico de 9/10 em hooks e urgências |
| **Temperatura emocional** | Quente e próxima, com pulsos de empolgação real | Nunca fria, nunca histérica — amiga de confiança que achou algo bom |
| **Arquétipo de voz** | "A Amiga Inteligente" | Não apresentadora de TV, não locutora de rádio, não vendedora de telemarketing |

### O que a voz da Clara NÃO é

- ❌ Voz de assistente virtual (fria, robótica, uniforme)
- ❌ Locutora de comercial anos 90 (exagerada, artificial)
- ❌ Influencer gritando (energia forçada, inautêntica)
- ❌ Apresentadora de telejornal (formal, distante, sem emoção)
- ✅ **É:** uma mensagem de voz enviada pela amiga mais antenada do grupo, animada por um motivo real

---

## 2. PARÂMETROS TÉCNICOS DE SÍNTESE

### 2.1 Parâmetros Base — Compatíveis com SSML (Universal)

> SSML (Speech Synthesis Markup Language) é o padrão suportado por Google Cloud TTS,
> Azure Cognitive Services, Amazon Polly e a maioria dos motores modernos.

```xml
<!-- Envelope padrão para todo áudio da Clara -->
<speak>
  <prosody rate="108%" pitch="-1.5st" volume="+2dB">
    <!-- conteúdo do script aqui -->
  </prosody>
</speak>
```

| Parâmetro SSML | Valor padrão | Faixa dinâmica | Descrição |
|---|---|---|---|
| `rate` (velocidade) | `108%` | 95% – 130% | Ligeiramente acima do neutro (100%) · dá dinamismo urbano sem atropelo |
| `pitch` (tom) | `-1.5st` | -3st a +1st | Semitom abaixo do neutro · voz mais assentada, confiante · evita agudeza robótica |
| `volume` | `+2dB` | +0dB a +3dB | Presença sem distorção · importante para compressão de áudio social |
| `emphasis` forte | `<emphasis level="strong">` | — | Aplica pitch +15% e rate -10% na palavra marcada |
| `emphasis` moderada | `<emphasis level="moderate">` | — | Pitch +8% e rate -5% |

### 2.2 Parâmetros por Motor — Google Cloud TTS (Lyria 3 / Neural2 / Studio)

```json
{
  "audioConfig": {
    "audioEncoding": "MP3",
    "speakingRate": 1.08,
    "pitch": -1.5,
    "volumeGainDb": 2.0,
    "effectsProfileId": ["headphone-class-device"]
  },
  "voice": {
    "languageCode": "pt-BR",
    "name": "pt-BR-Neural2-C",
    "ssmlGender": "FEMALE"
  }
}
```

**Vozes recomendadas — Google Cloud TTS (hierarquia de qualidade):**

| Prioridade | Voice ID | Tipo | Característica |
|---|---|---|---|
| 1ª | `pt-BR-Studio-C` | Studio | Melhor naturalidade — uso em produção final |
| 2ª | `pt-BR-Neural2-C` | Neural2 | Excelente custo-benefício — uso em testes e rascunhos |
| 3ª | `pt-BR-Wavenet-E` | WaveNet | Fallback — qualidade inferior ao Neural2 |

### 2.3 Parâmetros por Motor — ElevenLabs (alternativa premium)

```json
{
  "model_id": "eleven_multilingual_v2",
  "voice_settings": {
    "stability": 0.48,
    "similarity_boost": 0.76,
    "style": 0.62,
    "use_speaker_boost": true
  },
  "language_code": "pt-BR"
}
```

| Parâmetro ElevenLabs | Valor | Efeito |
|---|---|---|
| `stability` | `0.48` | Abaixo de 0.5 = mais variação natural · evita monotonia robótica |
| `similarity_boost` | `0.76` | Alta fidelidade à voz clonada · menos deriva emocional |
| `style` | `0.62` | Expressividade moderada-alta · aplica inflexões emocionais naturais |
| `speaker_boost` | `true` | Clareza de dicção · fundamental para compressão de áudio vertical |

### 2.4 Parâmetros por Bloco — Mapa de Variação Dinâmica

| Bloco do Roteiro | Rate | Pitch | Energia | Nota de Direção |
|---|---|---|---|---|
| **A — Hook** (0–4s) | 118% | -1.0st | 9/10 | Velocidade máxima · revelação urgente · sem pausa antes do gancho |
| **B — Problema** (4–14s) | 105% | -1.5st | 7/10 | Ritmo de conversa · identificação emocional · leve queda de pitch no "pois é" |
| **C — Solução** (14–32s) | 110% | -2.0st | 8/10 | Tom mais sério e confiante ao falar do sistema · pitch cai em "verificou e aprovou" |
| **D — Segurança** (32–46s) | 100% | -1.5st | 7/10 | Desacelera intencionalmente · cuidado e clareza · como explicar algo importante |
| **E — CTA** (46–56s) | 120% | -1.0st | 9/10 | Volta à energia máxima · urgência genuína · sem arrastamento |
| **F — Fechamento** (56–60s) | 90% | -2.5st | 6/10 | Desaceleração proposital · marca fica ecoando · fim com calma e autoridade |

---

## 3. MARCAÇÃO FONÉTICA DO ROTEIRO INSTITUCIONAL

> Legenda dos marcadores:
> `[P300]` = pausa de 300ms · `[P500]` = pausa de 500ms · `[P700]` = pausa dramática 700ms
> `[ÊNF]` = ênfase forte · `[mod]` = ênfase moderada · `[LENTO]` = rate -20% · `[RÁPIDO]` = rate +15%

---

### BLOCO A — HOOK (0s – 4s)

**Script original:**
> *"E se eu te dissesse que você tá pagando caro em produto que tem mais barato, agora, nesse exato momento?"*

**Script com marcação fonética:**

```
E se eu te dissesse
[P200]
que você tá pagando [ÊNF]CARO[/ÊNF]
[P150]
em produto que tem mais barato[P300] agora[P200]
[RÁPIDO]nesse exato momento?[/RÁPIDO]
```

**Versão SSML:**
```xml
<s>E se eu te dissesse
  <break time="200ms"/>
  que você tá pagando <emphasis level="strong">caro</emphasis>
  <break time="150ms"/>
  em produto que tem mais barato?
  <break time="300ms"/>
  Agora.
  <break time="200ms"/>
  <prosody rate="118%">Nesse exato momento.</prosody>
</s>
```

> 🎯 **Regra fonética A:** o `CARO` deve ter pitch ligeiramente mais alto + leve alongamento
> da vogal (como quando alguém diz "cara-a-a" em tom de surpresa). Rate cai 10% na
> palavra, sobe 15% no final "nesse exato momento" — efeito de revelação acelerada.

---

### BLOCO B — PROBLEMA (4s – 14s)

**Script original:**
> *"Todo dia aparece promoção boa na internet. Só que você não tem tempo de ficar vasculhando Mercado Livre, Shopee, AliExpress, e mais dez lojas diferentes pra achar o melhor preço. Ninguém tem. E quando você finalmente acha, já vendeu. Ou o preço voltou. Sabe essa sensação? Pois é."*

**Script com marcação fonética:**

```
Todo dia aparece promoção boa na internet.[P300]
Só que você não tem tempo de ficar vasculhando
[mod]Mercado Livre[/mod], [mod]Shopee[/mod], [mod]AliExpress[/mod],
e mais [ÊNF]dez lojas diferentes[/ÊNF]
pra achar o melhor preço.[P400]
[LENTO]Ninguém tem.[/LENTO]
[P500]
E quando você finalmente acha[P200]
já vendeu.
[P300]
Ou o preço voltou.
[P400]
[LENTO]Sabe essa sensação?[/LENTO]
[P300]
Pois é.
[P500]
```

**Versão SSML:**
```xml
<s>Todo dia aparece promoção boa na internet.
  <break time="300ms"/>
  Só que você não tem tempo de ficar vasculhando
  <emphasis level="moderate">Mercado Livre</emphasis>,
  <emphasis level="moderate">Shopee</emphasis>,
  <emphasis level="moderate">AliExpress</emphasis>,
  e mais <emphasis level="strong">dez lojas diferentes</emphasis>
  pra achar o melhor preço.
  <break time="400ms"/>
  <prosody rate="90%">Ninguém tem.</prosody>
  <break time="500ms"/>
  E quando você finalmente acha
  <break time="200ms"/>
  já vendeu.
  <break time="300ms"/>
  Ou o preço voltou.
  <break time="400ms"/>
  <prosody rate="88%">Sabe essa sensação?</prosody>
  <break time="300ms"/>
  Pois é.
  <break time="500ms"/>
</s>
```

> 🎯 **Regra fonética B:** `"Ninguém tem"` e `"Sabe essa sensação? Pois é"` são os dois
> momentos de identificação máxima — rate cai 12%, pitch desce 0.5st. O público precisa
> sentir que a Clara entende a frustração deles. Pausa longa (500ms) após "Pois é" — ela
> deixa a dor assentar antes de oferecer a solução.

---

### BLOCO C — SOLUÇÃO / ECOSSISTEMA (14s – 32s)

**Script original:**
> *"Aí nasceu o Clube do Achado. A gente tem um sistema que varre a internet 24 horas por dia — Mercado Livre, AliExpress, e outras grandes lojas — comparando preço, histórico de desconto e avaliação real. O sistema filtra os produtos com desconto real — não aquele desconto fake que aumentou o preço antes pra fingir que baixou. A gente só posta quando a oferta é verdadeira."*

**Script com marcação fonética:**

```
[P300]Aí nasceu o [ÊNF]Clube do Achado.[/ÊNF]
[P400]
A gente tem um [ÊNF]sistema[/ÊNF]
que [ÊNF]varre a internet[/ÊNF] [mod]24 horas por dia[/mod] —
Mercado Livre, AliExpress, e outras grandes lojas —
comparando preço, [mod]histórico de desconto[/mod] e avaliação real.
[P400]
O sistema filtra os produtos com [ÊNF]desconto real[/ÊNF]
[P300]
[LENTO]— não aquele desconto fake[/LENTO]
[P200]
[LENTO]que aumentou o preço antes[/LENTO]
[P200]
[LENTO]pra fingir que baixou.[/LENTO]
[P600]
[RÁPIDO]A gente só posta quando a oferta é [ÊNF]verdadeira.[/ÊNF][/RÁPIDO]
```

**Versão SSML:**
```xml
<break time="300ms"/>
<s>Aí nasceu o <emphasis level="strong">Clube do Achado.</emphasis>
  <break time="400ms"/>
  A gente tem um <emphasis level="strong">sistema</emphasis>
  que <emphasis level="strong">varre a internet</emphasis>
  <emphasis level="moderate">24 horas por dia</emphasis> —
  Mercado Livre, AliExpress, e outras grandes lojas —
  comparando preço,
  <emphasis level="moderate">histórico de desconto</emphasis>
  e avaliação real.
  <break time="400ms"/>
  O sistema filtra os produtos com
  <emphasis level="strong">desconto real</emphasis>
  <break time="300ms"/>
  <prosody rate="88%" pitch="-2.5st">
    — não aquele desconto fake
    <break time="200ms"/>
    que aumentou o preço antes
    <break time="200ms"/>
    pra fingir que baixou.
  </prosody>
  <break time="600ms"/>
  <prosody rate="112%">
    A gente só posta quando a oferta é
    <emphasis level="strong">verdadeira.</emphasis>
  </prosody>
</s>
```

> 🎯 **Regra fonética C — A Virada do Desconto Fake:**
> Este é o momento de maior variação emocional do roteiro. O bloco "não aquele desconto
> fake" recebe tratamento especial: rate desce para 88%, pitch cai -2.5st (tom mais grave,
> mais sério). É o único ponto onde a Clara soa levemente indignada. A pausa de 600ms
> depois serve como "ponto de viragem" — o público respira, processa e está pronto para
> ouvir "A gente só posta quando é verdadeiro" com impacto máximo.

---

### BLOCO D — QUEBRA DA OBJEÇÃO (32s – 46s)

**Script original:**
> *"E antes que você pense 'ai, link suspeito' — para. Todos os links que a gente compartilha levam direto pro Mercado Livre e pro AliExpress oficiais. São links de parceria certificada, igual a um vendedor credenciado dessas lojas. Você clica, cai direto na loja, compra pelo método de pagamento normal que você já usa. A gente só recebe uma comissão da loja — sem custo nenhum a mais pra você. O preço é o mesmo. Você economiza. A loja vende. A gente sustenta o canal. Todo mundo sai ganhando."*

**Script com marcação fonética:**

```
E antes que você pense [P200] 'ai, link suspeito' [P300]
[ÊNF]para.[/ÊNF]
[P500]
[LENTO]Todos os links que a gente compartilha[/LENTO]
levam [ÊNF]direto[/ÊNF] pro Mercado Livre e pro AliExpress [ÊNF]oficiais.[/ÊNF]
[P300]
São links de [mod]parceria certificada[/mod],
igual a um vendedor credenciado dessas lojas.
[P400]
Você clica, cai direto na loja,
compra pelo método de pagamento [mod]normal[/mod] que você já usa.
[P300]
A gente só recebe uma comissão da loja —
[ÊNF]sem custo nenhum a mais pra você.[/ÊNF]
[P300]
[ÊNF]O preço é o mesmo.[/ÊNF]
[P500]
[LENTO]Você economiza.[/LENTO] [P200]
[LENTO]A loja vende.[/LENTO] [P200]
[LENTO]A gente sustenta o canal.[/LENTO] [P300]
[ÊNF]Todo mundo sai ganhando.[/ÊNF]
[P400]
```

**Versão SSML:**
```xml
<s>E antes que você pense
  <break time="200ms"/>
  <prosody pitch="+3st" rate="95%">'ai, link suspeito'</prosody>
  <break time="300ms"/>
  <emphasis level="strong">para.</emphasis>
  <break time="500ms"/>
  <prosody rate="95%">
    Todos os links que a gente compartilha
    levam <emphasis level="strong">direto</emphasis>
    pro Mercado Livre e pro AliExpress
    <emphasis level="strong">oficiais.</emphasis>
  </prosody>
  <break time="300ms"/>
  São links de <emphasis level="moderate">parceria certificada</emphasis>,
  igual a um vendedor credenciado dessas lojas.
  <break time="400ms"/>
  Você clica, cai direto na loja,
  compra pelo método de pagamento
  <emphasis level="moderate">normal</emphasis> que você já usa.
  <break time="300ms"/>
  A gente só recebe uma comissão da loja —
  <emphasis level="strong">sem custo nenhum a mais pra você.</emphasis>
  <break time="300ms"/>
  <emphasis level="strong">O preço é o mesmo.</emphasis>
  <break time="500ms"/>
  <prosody rate="92%">
    Você economiza. <break time="200ms"/>
    A loja vende. <break time="200ms"/>
    A gente sustenta o canal. <break time="300ms"/>
  </prosody>
  <emphasis level="strong">Todo mundo sai ganhando.</emphasis>
  <break time="400ms"/>
</s>
```

> 🎯 **Regra fonética D — A Tríade Final:**
> "Você economiza. A loja vende. A gente sustenta o canal." é uma sequência rítmica
> intencional — três frases curtas, pausa de 200ms entre cada uma, rate 92%.
> O ritmo deve soar como três batidas de tambor. A última frase, "Todo mundo sai
> ganhando", volta ao rate normal com ênfase forte — é o fechamento emocional do bloco.
>
> **Atenção especial:** a frase `'ai, link suspeito'` deve ser dita com pitch levemente
> mais agudo (+3st) imitando o tom de quem pensa isso — efeito de "voz interna do
> espectador". Isso cria conexão imediata com quem estava pensando exatamente isso.

---

### BLOCO E — CHAMADA PARA AÇÃO (46s – 56s)

**Script original:**
> *"Se você quer parar de perder oferta boa — segue o @achadinhosia82 agora. Ativa o sininho. E entra no nosso grupo exclusivo pelo link da bio. Porque a próxima oferta que a gente postar pode ser exatamente o que você tava procurando. E vai acabar rápido."*

**Script com marcação fonética:**

```
Se você quer parar de [ÊNF]perder oferta boa[/ÊNF] [P300]
segue o [ÊNF]@achadinhosia82[/ÊNF] agora.
[P200]
[ÊNF]Ativa o sininho.[/ÊNF]
[P200]
E entra no nosso [mod]grupo exclusivo[/mod] pelo link da bio.
[P500]
Porque a próxima oferta que a gente postar
pode ser [ÊNF]exatamente o que você tava procurando.[/ÊNF]
[P300]
[LENTO]E vai acabar [ÊNF]rápido.[/ÊNF][/LENTO]
```

**Versão SSML:**
```xml
<prosody rate="115%">
  <s>Se você quer parar de
    <emphasis level="strong">perder oferta boa</emphasis>
    <break time="300ms"/>
    segue o <emphasis level="strong">arroba achadinhosia oitenta e dois</emphasis> agora.
    <break time="200ms"/>
    <emphasis level="strong">Ativa o sininho.</emphasis>
    <break time="200ms"/>
    E entra no nosso
    <emphasis level="moderate">grupo exclusivo</emphasis>
    pelo link da bio.
    <break time="500ms"/>
  </s>
</prosody>
<s>Porque a próxima oferta que a gente postar
  pode ser
  <emphasis level="strong">exatamente o que você tava procurando.</emphasis>
  <break time="300ms"/>
  <prosody rate="92%">
    E vai acabar <emphasis level="strong">rápido.</emphasis>
  </prosody>
</s>
```

> 🎯 **Regra fonética E — Instrução de @handle:**
> O handle `@achadinhosia82` deve ser sintetizado como `"arroba achadinhosia oitenta e dois"`.
> Nunca deletrear `a-c-h-a-d-i-n-h-o-s-i-a`. Usar number-to-speech para "82 → oitenta e dois".
> Alguns motores sintetizam handles incorretamente — testar e ajustar no script SSML se necessário.
>
> **A última frase "E vai acabar rápido"** recebe tratamento inverso ao esperado: rate
> DESACELERA para 92% (efeito de peso). O "rápido" com ênfase forte cria paradoxo
> intencional — ela fala devagar para dizer que vai acabar rápido. Isso prende mais
> atenção do que acelerar.

---

### BLOCO F — FECHAMENTO DE MARCA (56s – 60s)

**Script original:**
> *"Clube do Achado. A gente garimpou pra você."*

**Script com marcação fonética:**

```
[P600]
[LENTO][ÊNF]Clube do Achado.[/ÊNF][/LENTO]
[P500]
[LENTO]A gente garimpou [ÊNF]pra você.[/ÊNF][/LENTO]
[P800]
```

**Versão SSML:**
```xml
<break time="600ms"/>
<prosody rate="85%" pitch="-2.5st">
  <emphasis level="strong">Clube do Achado.</emphasis>
  <break time="500ms"/>
  A gente garimpou <emphasis level="strong">pra você.</emphasis>
</prosody>
<break time="800ms"/>
```

> 🎯 **Regra fonética F — O Silêncio que Vende:**
> O fechamento de marca deve soar como o final de um discurso — rate 85%, pitch -2.5st
> (o ponto mais grave de todo o roteiro). A pausa final de 800ms não é desperdício de
> tempo: ela cria o espaço onde o nome "Clube do Achado" ecoa na memória do espectador.
> Menos é mais. Não tentar preencher o silêncio.

---

## 4. MAPA DE PALAVRAS-CHAVE COM ÊNFASE OBRIGATÓRIA

Estas palavras, **independentemente do roteiro**, recebem sempre marcação de ênfase
quando usadas em qualquer áudio da Clara:

| Palavra / Expressão | Nível de ênfase | Efeito esperado |
|---|---|---|
| `CARO` / `pagando caro` | Forte | Ativa a dor financeira — ancora o problema |
| `SISTEMA` / `nosso sistema` | Forte | Posiciona inteligência tecnológica por trás |
| `VARRE` / `varre a internet` | Forte | Imagem de poder e abrangência |
| `REAL` / `desconto real` | Forte | Diferencia de desconto fake |
| `FAKE` / `desconto fake` | Forte + LENTO | Indignação controlada — cria contraste |
| `DIRETO` / `vai direto` | Forte | Elimina o medo do redirecionamento suspeito |
| `OFICIAIS` / `lojas oficiais` | Forte | Âncora de segurança — palavra-chave de confiança |
| `SEGURO` / `é seguro` | Forte | Quebra de objeção direta |
| `MESMO` / `preço é o mesmo` | Forte | Elimina dúvida sobre custo extra |
| `RÁPIDO` / `vai acabar` | Forte | Urgência sem histeria |
| `Clube do Achado` | Forte + LENTO | Fixação de marca — sempre com peso e pausa posterior |
| `@achadinhosia82` | Moderada | Clareza de CTA — pronunciar por extenso |

---

## 5. REGRAS DE PRODUÇÃO E CONTROLE DE QUALIDADE

### 5.1 Checklist pré-aprovação de áudio

Antes de usar qualquer áudio gerado em produção, verificar:

- [ ] **Naturalidade:** a voz não soa robótica em nenhuma das 3 primeiras reproduções
- [ ] **Pausas:** as pausas dramáticas estão presentes e no timing correto
- [ ] **Ênfases:** palavras-chave se destacam sem soar forçadas
- [ ] **Bloco C — Fake:** o tom ficou visivelmente mais sério e lento no bloco "desconto fake"
- [ ] **Bloco D — Handle:** `@achadinhosia82` foi pronunciado corretamente por extenso
- [ ] **Bloco F — Fechamento:** os últimos 4 segundos são os mais lentos e graves de todo o áudio
- [ ] **Duração total:** entre 55 e 62 segundos (teste em velocidade 1x)
- [ ] **Qualidade de exportação:** MP3 320kbps ou WAV 44.1kHz/16bit

### 5.2 Ajustes finos por plataforma

| Plataforma | Ajuste de rate | Ajuste de volume | Observação |
|---|---|---|---|
| **Instagram Reels** | +5% (compressão de codec) | +1.5dB | Áudio comprimido pelo Instagram — compensar |
| **TikTok** | +3% | +1dB | Equalização agressiva na plataforma |
| **YouTube Shorts** | Neutro | Neutro | Melhor fidelidade — usar como master |
| **WhatsApp / Telegram** | -3% | Neutro | Audiência ouve no alto-falante do celular sem fone |

### 5.3 Arquivos de saída — convenção de nomenclatura

```
output/audio/
├── institucional/
│   ├── clara-institucional-v1-master.wav        # Master sem compressão
│   ├── clara-institucional-v1-reels.mp3         # Instagram/TikTok (320kbps)
│   ├── clara-institucional-v1-shorts.mp3        # YouTube Shorts (320kbps)
│   └── clara-institucional-v1-wa.mp3            # WhatsApp/Telegram (192kbps)
└── hooks-ab/
    ├── hook-a-revelacao.mp3                     # "E se eu te dissesse..."
    ├── hook-b-curiosidade.mp3                   # "Cara, você não vai acreditar..."
    ├── hook-c-fomo.mp3                          # "Sabe aquele produto..."
    └── hook-d-promessa.mp3                      # "Em 60 segundos eu te explico..."
```

---

## 6. REFERÊNCIA RÁPIDA — PARÂMETROS PADRÃO CLAROS

```
VOZ:         Feminina · pt-BR · 27-32 anos · Sotaque neutro urbano
MOTOR:       Google Studio-C (prod) · Neural2-C (teste) · ElevenLabs multilingual v2 (alt)
RATE BASE:   108% — varia de 85% (fechamento) a 120% (CTA/hook)
PITCH BASE:  -1.5st — varia de -2.5st (fechamento/fake) a -1.0st (hook/CTA)
VOLUME:      +2dB base · ajustar por plataforma (+1.5dB Reels · +1dB TikTok)
ENERGIA:     7.5/10 base · 9/10 hooks e CTA · 6/10 fechamento

PALAVRAS COM ÊNFASE OBRIGATÓRIA:
CARO · SISTEMA · VARRE · REAL · FAKE · DIRETO · OFICIAIS · SEGURO · MESMO · RÁPIDO

PAUSAS DRAMÁTICAS OBRIGATÓRIAS:
→ 500ms após o Hook ("...nesse exato momento?")
→ 600ms após "desconto fake" (virada de bloco)
→ 500ms antes do tripé "Você economiza. A loja vende. A gente sustenta."
→ 800ms após "pra você" no fechamento de marca
```

---

## 7. EVOLUÇÃO E VERSIONAMENTO

| Versão | Data | Alteração |
|---|---|---|
| 1.0 | 2026-05-16 | Criação inicial — roteiro institucional + parâmetros base |

> **Próximas versões previstas:**
> - v1.1 — Variações de hook (A/B/C/D) com marcação SSML individual
> - v1.2 — Parâmetros para formato "Story rápido" (15s) e "Oferta relâmpago" (30s)
> - v2.0 — Voz clonada aprovada (ElevenLabs) com ID de voz fixo

---

*Documento gerado em 2026-05-16 · Motor de Achadinhos — Tópico 14: Voz e Síntese*
*Referência: `config/persona-clara.json` v2.0.0*
