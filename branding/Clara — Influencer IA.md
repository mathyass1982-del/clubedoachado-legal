---
created: 2026-05-13
tags: [#branding, #permanente]
area: Negócios
aliases: [Clara, Influencer IA, Persona Clara, Clara Achadinhos]
---

# CLARA — DIREÇÃO VISUAL DA INFLUENCER IA

> Este documento define a aparência, estética e direção visual da Clara. Para personalidade e tom de voz, consultar `config/persona-clara.json`.

---

## CONCEITO VISUAL

Clara é uma **influencer digital gerada por IA** que representa o rosto humano de uma operação automatizada de curadoria.

Ela deve parecer:
- **Real, mas claramente polida** — não enganosamente humana, mas visualmente atraente
- **Acessível, não celebridade** — parece alguém que você poderia conhecer, não uma modelo de campanha
- **Brasileira** — traços, estilo e contexto visual claramente brasileiro
- **Profissional inteligente** — visual de quem entende do que fala, não de quem apenas posta foto bonita

---

## APARÊNCIA FÍSICA

### ETNIA E TRAÇOS
- Brasileira, traços miscigenados (pardo/moreno claro)
- Cabelo: castanho escuro, liso ou levemente ondulado
- Idade aparente: 27–33 anos
- Olhos: castanhos ou verdes claros
- Sorriso: natural, não forçado — expressão de quem está compartilhando algo bom

### ESTILO DE VESTIMENTA
| Contexto | Roupa |
|----------|-------|
| Feed padrão | Casual premium — blusa simples em cores neutras (branco, azul, bege) |
| Story de urgência | Mais informal — como se tivesse mandando uma mensagem rápida |
| Apresentação de produto tech | Leve toque moderno — mas sem exagerar |
| Promoção de moda/beleza | Mais cuidada — mas sempre acessível |

**Regra:** nunca vestimenta ostensiva. Clara não usa luxo visível. Ela é acessível.

---

## PALETA VISUAL DA CLARA (alinhada ao Brand Engine)

| Elemento | Cor |
|----------|-----|
| Fundo preferido (backgrounds) | Azul escuro `#0D1B4B` ou branco `#FFFFFF` |
| Elementos decorativos | Amarelo `#FFD600` |
| Roupas dominantes | Branco, bege, azul marinho, preto suave |
| Evitar | Vermelho, rosa, verde vibrante (foge da paleta da marca) |

> ⚠️ Conflito com `persona-clara.json`: a paleta definida no JSON usa `#FF6B6B` (vermelho) como cor primária. A direção oficial do Brand Engine é azul escuro. Este conflito precisa ser resolvido antes de gerar novos cards com a Clara.

---

## EXPRESSÃO E ENERGIA

| Situação | Expressão visual |
|----------|-----------------|
| Achado comum | Sorriso moderado, olhar direto para câmera |
| Achado explosivo (SCF alto) | Empolgação genuína — expressão de "olha isso!" |
| Alerta de urgência | Seriedade amigável — como avisar um amigo |
| Curadoria de categoria | Postura mais calma, intelectual — a especialista |
| Story casual | Descontraída — como gravar um áudio para alguém próximo |

---

## CENÁRIOS VISUAIS APROVADOS

| Cenário | Descrição |
|---------|-----------|
| Ambiente doméstico moderno | Cozinha clara, sala minimalista — vida real premium |
| Fundo neutro clean | Parede branca ou cinza claro — foco no produto |
| Fundo com gradiente azul | Fundo da marca — para posts mais formais |
| Contexto de compra | Segurando celular, olhando tela — naturalidade |

### CENÁRIOS PROIBIDOS
- ❌ Estúdio fotográfico óbvio (fundo infinito)
- ❌ Paisagem exótica, viagem, luxo
- ❌ Cenário de academia ou esporte (fora do nicho)
- ❌ Produtos alimentícios na mão (conflito de nicho)

---

## PROMPTS PARA GERAÇÃO VISUAL (IA Generativa)

### AVATAR / FOTO DE PERFIL
```
Portrait of a Brazilian woman, 28-32 years old, mixed ethnicity,
straight dark brown hair, warm smile, natural expression.
Wearing a simple white blouse. Clean white or soft blue background.
Casual premium style. Natural lighting. High quality, photorealistic.
Not overly glamorous, approachable and intelligent.
No heavy makeup. Instagram influencer aesthetic, but real and warm.
```

### STORY — URGÊNCIA
```
Brazilian woman, 28-32 years old, looking excited at her phone,
casual setting, home environment, natural lighting.
Wearing navy or white casual clothing.
Expression: "oh wow, look at this!" — genuine surprise and excitement.
Vertical framing 9:16. Instagram story format.
```

### FEED — CURADORIA
```
Brazilian woman, 28-32 years old, confident and warm expression,
looking directly at camera, slight smile.
Clean background (white wall or deep navy blue).
Casual professional outfit in neutral tones.
Holding or gesturing toward a product (optional).
Square format 1:1. Soft natural or studio lighting.
```

### CARD COM CLARA (PRODUTO + PESSOA)
```
Split composition: left side — product image on white background,
right side — Brazilian woman smiling, pointing to product.
Deep navy blue (#0D1B4B) overall background.
Yellow (#FFD600) accent badge showing discount percentage.
Clean, modern, marketplace premium aesthetic.
```

---

## WATERMARK / ASSINATURA NOS CARDS

- Nome: **Clara** — em Poppins SemiBold
- Cor: branco `#FFFFFF` sobre fundo escuro / azul `#0D1B4B` sobre fundo claro
- Acompanhada do ícone da lupa (logo símbolo) em tamanho pequeno
- Posição: canto inferior direito ou esquerdo dos cards
- Não deve competir com o preço ou produto

---

## EVOLUÇÃO VISUAL PLANEJADA

| Fase | Formato |
|------|---------|
| Fase 1 (atual) | Avatar estático + cards com imagem gerada |
| Fase 2 | Vídeos curtos com avatar animado (CapCut / HeyGen) |
| Fase 3 | Reels com voz e lip-sync da Clara |
| Fase 4 | Live simulada com Clara respondendo dúvidas |

---

## CONEXÕES

- [[Brand Engine]] — hub central
- [[Guideline Visual]] — paleta e tipografia
- [[Manifesto da Marca]] — o que a Clara representa
- [[Identidade Emocional]] — como a Clara deve fazer sentir
- `config/persona-clara.json` — tom de voz e personalidade operacional
