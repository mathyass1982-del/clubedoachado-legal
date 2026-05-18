---
created: 2026-05-13
tags: [#branding, #projeto/ativo]
area: Negócios
aliases: [Clara v2, Atualização Brand Engine Clara]
---

# ATUALIZAÇÃO BRAND ENGINE — CLARA v2.0

> Alinhamento completo da persona visual da Clara com o Brand Engine oficial do Clube do Achado.
> Data: 2026-05-13

---

## ALTERAÇÕES REALIZADAS

### `_meta`
- Versão atualizada: `1.0.0` → `2.0.0`
- Adicionado campo `atualizado_em: 2026-05-13`
- Adicionado campo `brand_engine_ref` apontando para o hub central

### `estilo_visual.paleta_de_cores` — SUBSTITUIÇÃO COMPLETA

| Campo | Antes | Depois |
|-------|-------|--------|
| `primaria` | `#FF6B6B` (vermelho) | `#0D1B4B` (azul escuro) |
| `secundaria` | `#FFD93D` (amarelo velho) | `#FFD600` (amarelo oficial) |
| `accent` | `#6BCB77` (verde) | `#FFFFFF` (branco) — renomeado para `neutro_claro` |
| `neutro_escuro` | `#2D2D2D` | `#1C1C1C` (preto suave oficial) |
| Adicionado | — | `primaria_variante: #1A237E` |
| Adicionado | — | `destaque_suave: #FFF9C4` |
| Adicionado | — | `apoio_escuro: #212121` |
| `descricao` | "quente, viva e moderna" | "premium e tecnológica — azul ancora confiança, amarelo sinaliza oportunidade" |

### `estilo_visual.fotografia_e_imagem`
- `mood`: de "leve, aspiracional" → "premium acessível — confiante, inteligente"
- Adicionado campo `iluminacao`: natural/estúdio suave, temperatura fria a neutra
- Adicionado campo `temperatura_de_cor`: fria a neutra, amarelo apenas como acento
- `proibido`: expandido com restrições de paleta quente, cores neon e estética de panfleto

### `aparencia_visual_clara` — NOVO BLOCO COMPLETO
Adicionado bloco com:
- Etnia, cabelo, idade aparente, expressão
- Roupas aprovadas e proibidas
- Cenários aprovados (4) e proibidos (4)

### `prompts_geracao_ia` — NOVO BLOCO COMPLETO
Adicionado 4 prompts prontos para geração em IA:
- `avatar_perfil` — foto de perfil oficial
- `story_urgencia` — story de oferta urgente
- `feed_curadoria` — post de curadoria no feed
- `card_produto_com_clara` — card dividido produto + clara

### `estetica`
- `referencias_visuais`: removidos "toques de cor quente" — substituído por paleta azul/amarelo
- `o_que_a_clara_nao_posta`: adicionada restrição explícita a paleta vermelha/laranja/rosa
- `sensacao_que_o_feed_deve_transmitir`: reescrito para "curadoria inteligente e confiável"

### `uso_pelos_agentes.campos_de_referencia_rapida`
- `cor_primaria`: `#FF6B6B` → `#0D1B4B`
- Adicionados: `cor_destaque`, `cor_texto_principal`, `cor_texto_escuro`

---

## CAMPOS NÃO ALTERADOS (mantidos integralmente)

- Personalidade (`personalidade`)
- Tom da marca (`tom_da_marca`)
- Linguagem (`linguagem`)
- Frases preferidas (`frases_preferidas`)
- Frases proibidas (`frases_proibidas`)
- Estilo de copy (`estilo_de_copy`)
- CTAs (`estilo_de_cta`)
- Diretrizes sociais (`diretrizes_sociais`)
- Posicionamento (`posicionamento`)
- Missão (`missao_da_marca`)
- Energia da personagem (`energia_da_personagem`)
- Voz futura (`voz_futura`)

---

## IMPACTO VISUAL ESPERADO

| Aspecto | Antes | Depois |
|---------|-------|--------|
| Tom geral | Quente, vibrante, informal | Premium, técnico, confiável |
| Cor dominante | Vermelho (#FF6B6B) | Azul escuro (#0D1B4B) |
| Acento | Amarelo dessaturado | Amarelo oficial (#FFD600) |
| Aparência Clara | Indefinida | Especificada: brasileira, 27-33, casual premium |
| Cenários | Não especificados | 4 aprovados, 4 proibidos |
| Prompts IA | Inexistentes | 4 prompts prontos para geração |
| Estética geral | "instagram de pessoa real" | "mídia premium de curadoria" |

---

## PRÓXIMOS PASSOS

- [ ] Gerar avatar da Clara usando `prompts_geracao_ia.avatar_perfil` (Midjourney / Leonardo.ai)
- [ ] Criar templates de card no Canva com nova paleta
- [ ] Validar consistência visual entre os cards gerados e o [[Guideline Visual]]
- [ ] Atualizar foto de perfil das páginas Facebook e Instagram
- [ ] Rever cards existentes em `output/cards/` — identificar os que usam paleta antiga
- [ ] Atualizar banner de capa das páginas com nova identidade

---

## CONEXÕES

- [[Brand Engine]] — hub central da marca
- [[Guideline Visual]] — paleta e tipografia de referência
- [[Clara — Influencer IA]] — direção visual completa da personagem
- [[Sistema Visual Operacional]] — como aplicar no dia a dia
- `config/persona-clara.json` — arquivo atualizado (v2.0.0)
