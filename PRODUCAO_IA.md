# Motor de Achadinhos — Modo de Produção IA

> Documentação oficial validada em 2026-05-12  
> Baseada em testes Fase 1 + Fase 2 com 13 produtos VIRAIS reais

---

## Comando oficial de produção diária

```bash
python scripts/pipeline-lote.py --pasta dados/produtos --modo ia
```

A configuração de produção é carregada automaticamente de `config/producao_ia.json`.  
Não é necessário passar `--paralelo` nem `--ia-score-minimo` — os valores corretos já estão na config.

### Overrides manuais disponíveis

```bash
# Processar pasta diferente
python scripts/pipeline-lote.py --pasta dados/produtos_novos --modo ia

# Forçar sequencial (debug ou cota reduzida)
python scripts/pipeline-lote.py --pasta dados/produtos --modo ia --paralelo 1

# Testar apenas N produtos
python scripts/pipeline-lote.py --pasta dados/produtos --modo ia --limite 5

# Ativar geração de vídeo (AGT-04)
python scripts/pipeline-lote.py --pasta dados/produtos --modo ia --gerar-video

# Testar sem consumir IA
python scripts/pipeline-lote.py --pasta dados/produtos --modo simulado
```

---

## Configuração ativa (`config/producao_ia.json`)

| Parâmetro | Valor | Descrição |
|---|---|---|
| `ia_modelo` | `claude-haiku-4-5-20251001` | Modelo mais rápido e barato |
| `ia_paralelo` | `3` | Workers simultâneos — validado sem rate limit |
| `ia_score_minimo` | `70` | SCF mínimo para chamar IA (abaixo = template local) |
| `ia_prompt_caching` | `true` | Cache de system prompts entre produtos |
| `ia_fallback_template` | `true` | Fallback automático se IA falhar |

---

## Benchmarks de produção

Medições reais com 13 produtos VIRAIS (2026-05-12):

| Cenário | Tempo parede | Tempo médio | Custo total | Cache hits |
|---|---|---|---|---|
| Fase 0 — sem otimização | 24,0 min | 110,8s/prod | US$ 0,3132 | — |
| Fase 1 — cache + max_tokens | 14,8 min | 68,2s/prod | US$ 0,1234 | 413.280 |
| **Fase 2 — 3 workers (produção)** | **~5,5 min** | **68s/prod** | **~US$ 0,12** | ✅ ativo |

### Custo médio estimado

| Lote | Produtos | Tempo estimado | Custo estimado |
|---|---|---|---|
| Pequeno | 10 | ~4 min | ~R$ 0,50 |
| Médio | 30 | ~11 min | ~R$ 1,50 |
| Grande | 60 | ~22 min | ~R$ 3,00 |
| Diário máximo | 100 | ~35 min | ~R$ 5,00 |

> Câmbio de referência: US$ 1 = R$ 5,50. Custo real pode variar com cache hits.

---

## Paralelismo — guia de escolha

| Situação | Recomendação | Por quê |
|---|---|---|
| Rotina diária normal | `--paralelo 3` ✅ | Validado sem rate limit |
| Lotes pequenos (≤ 6 prods) | `--paralelo 4` | Cota sobrando, mais rápido |
| API com cota reduzida | `--paralelo 1` | Sequencial, sem risco |
| Debug ou investigação | `--paralelo 1` | Logs limpos e legíveis |
| Pressa extrema (≤ 10 prods) | `--paralelo 4` | Aceitar rate limit ocasional |

---

## Fallback automático

O pipeline tem dois níveis de proteção:

1. **Rate limit por produto** — se `call_claude()` recebe 429, o produto continua com  
   copy gerado pelo template local (Clara). O produto aparece como `OK` no relatório,  
   mas a copy é de qualidade template, não IA. Identificado pela flag `⚠️ RATE LIMIT` no log.

2. **Retry com workers reduzidos** — se um produto **falha completamente** (sem summary JSON),  
   o sistema automaticamente retry com `N-1` workers, uma única vez.

---

## Filtro de score mínimo (`ia_score_minimo: 70`)

- Produtos com SCF preliminar **< 70**: pipeline roda normalmente (scores, analytics, copy),  
  mas **sem chamar a API** — usa template local.
- Produtos com SCF preliminar **≥ 70**: recebem IA completa (AGT-01 + AGT-02 + AGT-03).
- Threshold de **descarte total** permanece em 40 (produtos abaixo são rejeitados no AGT-01).

Isso significa que um produto com SCF 55 (BOM) é processado, gera copy válido via template,  
mas não consome tokens da API.

---

## Segurança de produção

| Proteção | Status |
|---|---|
| Streamlit automático | ❌ Desabilitado |
| Loop contínuo | ❌ Desabilitado |
| Background após finalizar | ❌ Desabilitado |
| Timeout por produto | ✅ 300s (5 min) |
| Workers máximo absoluto | ✅ 4 (config) |
| Processo encerra limpo | ✅ ThreadPoolExecutor com context manager |

---

## Arquivos de saída

Após cada execução:

| Arquivo | Conteúdo |
|---|---|
| `output/relatorio-geral.md` | Relatório completo em Markdown |
| `output/relatorio-geral.json` | Dados estruturados — ranking, métricas, tokens |
| `output/relatorio-geral.csv` | Planilha para análise externa |
| `output/produtos/*.md` | Copy final de cada produto |
| `output/logs/<id>.log` | Log individual por produto |

---

## Boas práticas

1. **Sempre use `--modo simulado` para validar novos produtos** antes de consumir IA.
2. **Mantenha `ANTHROPIC_API_KEY` no `.env`** — não cole a chave direto no terminal.
3. **Rode o Discovery Engine antes do pipeline** — garante produtos frescos na fila.
4. **Monitore o `relatorio-geral.json`** — o campo `tokens_cache_read` confirma que o  
   Prompt Caching está ativo (deve ser > 0 a partir do 2º produto).
5. **Rotacione a API key** se ela foi exposta em chat, terminal ou commit.
6. **Priorize a fila `⚡ POSTAR`** — são os produtos com SCF mais alto, prontos para publicação.

---

## Roadmap de otimização

| Fase | Status | Ganho |
|---|---|---|
| Fase 1 — Prompt Caching + max_tokens + Singleton | ✅ Produção | −38% tempo, −60% custo |
| Fase 2 — Paralelismo 3 workers | ✅ Produção | −67% tempo total vs original |
| Fase 3 — Anthropic Batch API (overnight) | 🔜 Planejado | −50% custo adicional |

---

*Motor de Achadinhos — Achadinhos Projeto*
