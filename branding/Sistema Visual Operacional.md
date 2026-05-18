---
created: 2026-05-13
tags: [#branding, #permanente]
area: Negócios
aliases: [Sistema Visual, Operacional Visual, Design Operacional]
---

# SISTEMA VISUAL OPERACIONAL — CLUBE DO ACHADO

> Como aplicar a identidade visual no dia a dia da operação automatizada. Este documento é o manual de uso — não o de conceito.

---

## TIPOS DE PEÇA VISUAL

### 1. CARD DE PRODUTO (principal)
**Uso:** feed Instagram, post Facebook, grupo Telegram, WhatsApp

**Estrutura obrigatória:**
```
┌─────────────────────────────┐
│  [BADGE DESCONTO -60%]      │  ← Amarelo, canto superior esquerdo
│                             │
│    [IMAGEM DO PRODUTO]      │  ← Centralizada, fundo branco interno
│                             │
│  R$ 89,90  ~~R$ 219,90~~   │  ← Preço atual bold + riscado
│  ⚡ FRETE GRÁTIS + CUPOM   │  ← Badges secundários
│                             │
│  [CTA BUTTON]               │  ← Amarelo sobre azul
│  🔍 Clara — Clube do Achado │  ← Assinatura, canto inferior
└─────────────────────────────┘
```

**Especificações:**
- Fundo geral: azul escuro `#0D1B4B`
- Área do produto: branco `#FFFFFF`
- Badge de desconto: amarelo `#FFD600` + texto azul escuro
- CTA button: amarelo `#FFD600` + texto azul escuro em bold

---

### 2. STORY DE OFERTA
**Uso:** Instagram Stories, Facebook Stories

**Estrutura:**
```
┌──────────────┐
│  [LOGO]      │  ← Canto superior, pequena
│              │
│  [PRODUTO]   │  ← Ocupa 50% do frame
│              │
│  -60% OFF    │  ← Grande, amarelo, centro
│  R$ 89,90   │  ← Preço atual
│              │
│  [BARRA CTA] │  ← "Ver oferta →" em amarelo
└──────────────┘
```

---

### 3. CARD DE CURADORIA (lista de produtos)
**Uso:** carrossel, post de "top 5 achados da semana"

**Regras:**
- Slide 1: capa com título ("5 achados que valem muito hoje")
- Slides 2–6: 1 produto por slide, estrutura de card
- Último slide: CTA + assinatura da Clara
- Máximo de 7 slides por carrossel

---

### 4. CAPA DE REELS / THUMBNAIL
**Uso:** thumbnail de vídeo no Instagram e Facebook

**Estrutura:**
- Fundo: azul escuro ou imagem do produto
- Clara (avatar ou foto) em destaque
- Texto de gancho em branco/amarelo bold, máximo 6 palavras
- Badge de urgência se aplicável

---

### 5. BANNER DE COMUNIDADE
**Uso:** capa do grupo WhatsApp, banner do canal Telegram, capa da página Facebook

**Conteúdo obrigatório:**
- Logo do Clube do Achado
- Slogan: "Inteligência que garimpa. Curadoria que vale."
- Elementos visuais: lupa, raio, etiqueta
- Não deve ter produtos específicos — é identidade, não oferta

---

## FLUXO DE CRIAÇÃO DE CARD

```
1. PRODUTO APROVADO pelo pipeline
       ↓
2. IMAGEM DO PRODUTO coletada (fundo branco ou removido)
       ↓
3. TEMPLATE DE CARD carregado (por categoria)
       ↓
4. DADOS PREENCHIDOS: preço, desconto %, badges ativos
       ↓
5. VALIDAÇÃO: hierarquia visual, paleta, legibilidade
       ↓
6. ASSINATURA CLARA aplicada
       ↓
7. EXPORTAR nas dimensões corretas por plataforma
```

---

## TEMPLATES POR CATEGORIA

| Categoria | Variação visual | Cor de acento |
|-----------|----------------|---------------|
| Tecnologia | Fundo azul escuro dominante | Azul + amarelo |
| Casa e cozinha | Fundo branco, produto grande | Branco + azul |
| Beleza e cuidados | Fundo suave, produto central | Branco + amarelo |
| Fitness e saúde | Fundo escuro com energia | Azul + amarelo |
| Moda | Fundo neutro clean | Branco + preto suave |
| Genérico | Template padrão da marca | Azul + amarelo |

---

## CLASSIFICAÇÃO DE URGÊNCIA (visual)

| Nível | Badge | Cor | Quando usar |
|-------|-------|-----|-------------|
| Normal | Sem badge especial | — | Oferta boa, sem prazo |
| Alto | ⚡ EXPLOSIVO | Azul escuro + amarelo | SCF > 80, desconto > 50% |
| Crítico | 🔴 ÚLTIMAS UNIDADES | Vermelho + branco | Estoque real baixo |
| Flash | ⚡ OFERTA RELÂMPAGO | Amarelo piscando (animado) | Tempo limitado verificado |

---

## CHECKLIST DE VALIDAÇÃO VISUAL

Antes de publicar qualquer peça:

- [ ] Paleta está correta? (azul/amarelo/branco/preto suave)
- [ ] Hierarquia: desconto → produto → preço → CTA?
- [ ] Logo ou assinatura da Clara presente?
- [ ] Texto legível em mobile (mínimo 16px)?
- [ ] Imagem do produto em alta resolução?
- [ ] Badges condizem com a oferta real?
- [ ] Nenhuma frase proibida no texto?
- [ ] Formato correto para a plataforma de destino?

---

## NOMENCLATURA DE ARQUIVOS

```
[tipo]-[categoria]-[data]-[id].png

Exemplos:
card-tech-20260513-001.png
story-moda-20260513-002.png
banner-capa-facebook-20260513.png
```

---

## PLATAFORMAS E ESPECIFICAÇÕES

| Plataforma | Formato preferido | Dimensão | Qualidade |
|------------|------------------|----------|-----------|
| Instagram Feed | PNG ou JPG | 1080×1080px | 90%+ |
| Instagram Story | PNG ou JPG | 1080×1920px | 90%+ |
| Instagram Reels | MP4 | 1080×1920px | — |
| Facebook Feed | PNG ou JPG | 1200×628px | 90%+ |
| Telegram | PNG | 800×800px | 90%+ |
| WhatsApp | JPG comprimido | 800×800px | 80% |

---

## CONEXÕES

- [[Brand Engine]] — hub central
- [[Guideline Visual]] — paleta, tipografia e regras
- [[Conceito Logo]] — uso da logo nas peças
- [[Clara — Influencer IA]] — como integrar a Clara nas peças
