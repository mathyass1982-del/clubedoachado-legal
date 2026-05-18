---
created: 2026-05-13
updated: 2026-05-14
tags: [#branding, #projeto/ativo]
area: Negócios
aliases: [Template Engine, Gerador de Posts, Motor Visual]
---

# TEMPLATE ENGINE v2.0 — CLUBE DO ACHADO

> Módulo Python responsável por gerar imagens de posts automaticamente a partir de produtos reais coletados pelo Discovery Engine. Alinhado com o Brand Engine v2.

---

## ARQUIVOS CRIADOS

| Arquivo | Função |
|---------|--------|
| `scripts/media/template_engine.py` | Motor de geração — classe `TemplateEngine`, paleta, estilos, download de imagem |
| `scripts/media/gerar_post_produto.py` | Runner CLI — processa produtos da pasta `dados/produtos/` |
| `scripts/media/enriquecer_imagem_url.py` | Enriquecedor — adiciona `imagem_url` em produtos existentes via scraping |
| `scripts/media/__init__.py` | Exposição do módulo como pacote Python |

---

## ESTRUTURA DE SAÍDA

```
assets/posts/gerados/
├── feed/       ← 1080×1350px (4:5) — FASE 1 ✅
└── stories/    ← 1080×1920px (9:16) — FASE 2 🔜
```

---

## PALETA OFICIAL (Brand Engine v2)

| Cor | Hex | RGB | Uso |
|-----|-----|-----|-----|
| Navy | `#0D1B4B` | 13, 27, 75 | Fundo principal, CTA navy |
| Amarelo | `#FFD600` | 255, 214, 0 | Badge desconto, destaque |
| Branco | `#FFFFFF` | 255, 255, 255 | Fundo clean, textos |
| Preto suave | `#1C1C1C` | 28, 28, 28 | Texto sobre fundo claro |

---

## ESTILOS DISPONÍVEIS

| Estilo | Gatilho | Aparência |
|--------|---------|-----------|
| `BRAND_PREMIUM` | padrão / SCF alto | Fundo navy, texto branco, badge amarelo |
| `BRAND_CLEAN` | desconto ≥ 40% | Fundo branco, elementos navy + amarelo |

### Seleção automática:
```python
desconto >= 40%  → BRAND_CLEAN
qualquer outro   → BRAND_PREMIUM
```

---

## FORMATO DE ENTRADA (produto dict)

Campos usados pelo engine:

```json
{
  "id":                  "DISC-022",
  "nome":                "Nome do produto",
  "categoria":           "beleza",
  "preco_original":      219.90,
  "preco_com_desconto":  89.90,
  "percentual_desconto": 59,
  "economia_reais":      130.00,
  "imagem_url":          "https://...",
  "link_afiliado":       "https://...",
  "url_afiliado":        "https://...",
  "plataforma_origem":   "Mercado Livre",
  "frete_gratis":        true,
  "cupom":               "CLARA10",
  "dados_brutos": {
    "avaliacoes":  22713,
    "nota_media":  4.7
  }
}
```

### Campos gravados de volta no JSON após geração:

```json
{
  "post_feed_status":   "ok",
  "post_feed_arquivo":  "assets/posts/gerados/feed/DISC-022.png",
  "post_feed_data":     "2026-05-14T...",
  "post_feed_estilo":   "BRAND_PREMIUM",
  "post_feed_engine_v": "2.0.0"
}
```

---

## HIERARQUIA VISUAL DO CARD (4:5 — 1080×1350px)

```
┌─────────────────────────────────────────┐
│  ▬▬ linha topo (amarelo ou navy) ▬▬    │ 6–8px
│  Clara • Clube do Achado    [CATEGORIA] │ header
│                          [-XX% badge]   │ badge desconto
│  ┌─────────────────────────────────┐   │
│  │                                 │   │
│  │      IMAGEM DO PRODUTO          │   │ 500px
│  │      (real ou placeholder)      │   │
│  └─────────────────────────────────┘   │
│  ────────────────────────────────────   │ separador
│  NOME DO PRODUTO (2 linhas max)         │
│  via Mercado Livre                      │ marketplace
│  De  R$ 219,90 (riscado)               │
│  R$ 89,90  ← herói visual 72px bold    │
│  [Economia R$ 130] [FRETE GRÁTIS]      │ pills
│  ●●●●● 4.7/5 • 22.713 avaliações      │ círculos dourados
│                                         │
│  ┌─────── VER OFERTA ───────────────┐  │ CTA 88px
│  │   amarelo/navy, bold, centrado   │  │
│  └──────────────────────────────────┘  │
│  link afiliado curto                    │
│  [avatar] Clara  •  Preços sujeitos... │ rodapé
│  ▬▬ linha base (amarelo ou navy) ▬▬   │ 5px
└─────────────────────────────────────────┘
```

---

## COMO USAR

### Linha de comando:

```bash
# Todos os monetizados (padrão)
python scripts/media/gerar_post_produto.py

# Produto específico
python scripts/media/gerar_post_produto.py --id DISC-022

# Dry-run (sem fetch de imagem)
python scripts/media/gerar_post_produto.py --limite 3 --dry-run

# Forçar estilo
python scripts/media/gerar_post_produto.py --estilo BRAND_CLEAN

# Regenerar todos
python scripts/media/gerar_post_produto.py --forcar

# Todos os produtos (sem filtro de monetização)
python scripts/media/gerar_post_produto.py --todos --limite 10
```

### Como módulo Python:

```python
from scripts.media.template_engine import TemplateEngine

engine = TemplateEngine(dry_run=True)
img    = engine.gerar_feed(produto_dict)
path   = engine.salvar_feed(img, "DISC-022")
```

---

## PIPELINE IMAGEM_URL — SOLUÇÃO COMPLETA (2026-05-14)

### Problema raiz
Todos os 103 produtos em `dados/produtos/` tinham `imagem_url` vazio porque:
1. O coletor ML não extraía imagens do SSR HTML
2. URLs tipo `/p/MLB...` e `/up/MLBU...` são SPA shells — HTML renderizado no browser, não no servidor
3. O banner do Meli+ (`940933-MLA111228888941`) está embutido em TODAS as páginas ML e contamina extrações genéricas

### Estrutura SSR das Ofertas ML (2026)

A página `mercadolivre.com.br/ofertas` retorna blocos `ORGANIC_ITEM` com:
```json
"pictures": {
  "scale": "FILL",
  "pictures": [{"id": "836450-MLA100062682691_122025"}]
}
```

URL construída a partir do ID:
```
https://http2.mlstatic.com/D_NQ_{picture_id}-O.jpg
```

### Fix no Coletor (coleta/fontes/mercadolivre_real_coletor.py)

Adicionado na função `_parsear_pagina()`:
```python
m_pic_id = re.search(
    r'"pictures"\s*:\s*\{[^{]*"pictures"\s*:\s*\[\s*\{"id"\s*:\s*"([^"]+)"',
    bloco_html
)
if m_pic_id:
    imagem_url = f"https://http2.mlstatic.com/D_NQ_{m_pic_id.group(1)}-O.jpg"
```

**Resultado: 41/41 produtos coletados com imagem_url real e distinta por produto.**

### Correções no Template Engine

1. **Estrelas** — `★☆` não renderizam com Pillow + Segoe UI no Windows. Solução: círculos desenhados com PIL:
   - Cheios (dourado `#FFBE00`) = estrelas preenchidas
   - Vazios (cor secundária) = estrelas vazias

2. **Emojis de categoria** — Segoe UI não suporta emojis color. Solução: fonte `seguiemj.ttf` (Segoe UI Emoji, nativa do Windows) carregada separadamente.

---

## VALIDAÇÃO REALIZADA (2026-05-14)

```
✅ Coletor ML: 41/41 produtos com imagem_url única e correta
✅ Imagens validadas: JPEG real, ~38KB, status 200
✅ DEMO-001 (Creatina Dark Lab): 1080×1350px | 246KB | BRAND_CLEAN
✅ Imagem real do produto renderizada no card
✅ Círculos dourados substituindo ★☆
✅ Emoji de categoria renderizado com seguiemj.ttf
✅ Pipeline completo: Coletar → imagem_url → Gerar Card → PNG salvo
```

---

## PROBLEMAS CONHECIDOS

### 1. Produtos antigos sem imagem_url
103 produtos em `dados/produtos/` foram coletados antes da correção. Estratégia:
- Re-coletar via coletor atualizado substituindo progressivamente
- Produtos com URL direta podem ser enriquecidos via `enriquecer_imagem_url.py`
- Produtos com `/p/MLB...` raiz precisam ser substituídos

### 2. Banner Meli+ como falso positivo
A URL `D_NQ_940933-MLA111228888941_052026-OO.jpg` aparece em TODAS as páginas ML como preload de imagem. Qualquer extração genérica de URL mlstatic vai pegar essa imagem.

**Solução:** bloquear explicitamente esse ID nas extrações:
```python
BANNER_MELIMAIS = '940933-MLA111228888941'
if BANNER_MELIMAIS in imagem_url:
    imagem_url = ""
```

### 3. clara_design.py ainda com paleta antiga
O módulo de cards 1:1 usa `#FF6B6B` (vermelho). Não foi alterado para não quebrar o pipeline existente.

---

## RELAÇÃO COM CÓDIGO EXISTENTE

| Módulo | Função | Status |
|--------|--------|--------|
| `scripts/gerar-card-oferta.py` | Cards 1:1 (1080×1080) | Mantido, funcional |
| `scripts/clara_design.py` | Estilos para cards 1:1 | ⚠️ Paleta antiga (#FF6B6B) |
| `scripts/media/template_engine.py` | Cards 4:5 (1080×1350) | ✅ Brand Engine v2 |
| `coleta/fontes/mercadolivre_real_coletor.py` | Coletor ML | ✅ Fixado em 2026-05-14 |
| `scripts/media/enriquecer_imagem_url.py` | Enriquecedor batch | ✅ Criado em 2026-05-14 |

---

## PRÓXIMOS PASSOS

- [x] Enriquecer produtos com `imagem_url` via coletor fixado
- [x] Testar geração real com imagem válida
- [x] Corrigir renderização de estrelas (círculos dourados)
- [x] Corrigir renderização de emojis (seguiemj.ttf)
- [ ] Integrar `gerar_post_produto.py` no pipeline de publicação automática
- [ ] Implementar stories 9:16 (Fase 2)
- [ ] Atualizar `clara_design.py` para paleta Brand Engine v2
- [ ] Adicionar watermark da logo no canto do card

---

## CONEXÕES

- [[Brand Engine]] — hub central da identidade visual
- [[Guideline Visual]] — paleta e tipografia de referência
- [[Clara — Influencer IA]] — avatar usado no rodapé dos cards
- [[Sistema Visual Operacional]] — onde os cards gerados se encaixam
- [[Identidade Emocional]] — sentimento que os cards devem transmitir
