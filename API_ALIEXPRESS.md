# Integração AliExpress Affiliate API

Motor de Achadinhos — documentação da integração com a [AliExpress Open Platform](https://portals.aliexpress.com/).

---

## Índice

1. [Configuração inicial](#1-configuração-inicial)
2. [Estrutura de arquivos](#2-estrutura-de-arquivos)
3. [Autenticação](#3-autenticação)
4. [Cliente HTTP](#4-cliente-http)
5. [Funções de produtos](#5-funções-de-produtos)
6. [Funções de afiliados](#6-funções-de-afiliados)
7. [Coletor do discovery engine](#7-coletor-do-discovery-engine)
8. [Tratamento de erros](#8-tratamento-de-erros)
9. [Testes](#9-testes)
10. [Segurança](#10-segurança)
11. [Referências](#11-referências)

---

## 1. Configuração inicial

### 1.1 Obter credenciais

1. Acesse [portals.aliexpress.com](https://portals.aliexpress.com/)
2. Crie um app em **Developer Center → My Apps**
3. Copie o **APP_KEY** e o **APP_SECRET**
4. Defina seu **Tracking ID** no painel de afiliados

### 1.2 Variáveis de ambiente

Adicione ao arquivo `.env` na raiz do projeto:

```env
# AliExpress Affiliate API
ALIEXPRESS_APP_KEY=sua_app_key_aqui
ALIEXPRESS_APP_SECRET=seu_app_secret_aqui
ALIEXPRESS_TRACKING_ID=seu_tracking_id
ALIEXPRESS_GATEWAY=api-sg.aliexpress.com
```

> **Gateway recomendado para o Brasil:** `api-sg.aliexpress.com` (Singapura — melhor latência para América do Sul)

### 1.3 Variáveis opcionais do coletor

```env
ALIEXPRESS_PRODUTOS_POR_KEYWORD=10    # itens por busca (máx 50)
ALIEXPRESS_HOT_PRODUCTS=true          # coletar hot products?
ALIEXPRESS_HOT_LIMITE=20              # limite de hot products
ALIEXPRESS_GERAR_LINKS=true           # gerar link afiliado automaticamente?
ALIEXPRESS_COMISSAO_MIN=0             # comissão mínima para incluir produto (0 = sem filtro)
ALIEXPRESS_NOTA_MIN=0                 # nota mínima (0 = sem filtro)
```

---

## 2. Estrutura de arquivos

```
backend/integracoes/aliexpress/
├── __init__.py        re-exports principais (AliExpressClient + exceções)
├── auth.py            assinatura HMAC-SHA256 das requisições
├── client.py          cliente HTTP com retry, rate-limit e logs
├── produtos.py        busca, detalhes, preços e imagens
├── afiliados.py       geração de links e consulta de comissão
└── exceptions.py      hierarquia de erros

coleta/fontes/
└── aliexpress_coletor.py   integração com o discovery engine

tests/
└── test_aliexpress.py      suite completa de testes

docs/
└── API_ALIEXPRESS.md       este arquivo
```

---

## 3. Autenticação

A API utiliza **HMAC-SHA256** como algoritmo de assinatura.

### Algoritmo de assinatura

```
1. Remover 'sign' e 'format' dos parâmetros
2. Ordenar pares chave=valor alfabeticamente pela chave
3. Concatenar: APP_SECRET + chave1 + valor1 + chave2 + valor2 + ... + APP_SECRET
4. HMAC-SHA256(key=APP_SECRET, msg=string_concatenada)
5. Resultado em UPPERCASE hexadecimal
```

### Parâmetros obrigatórios em toda requisição

| Parâmetro   | Descrição                          | Exemplo                                  |
|-------------|-------------------------------------|------------------------------------------|
| `app_key`   | Chave pública do app               | `12345678`                               |
| `method`    | Nome do método da API              | `aliexpress.affiliate.link.generate`     |
| `timestamp` | Timestamp em milissegundos (13 dig) | `1715625600000`                          |
| `sign_method` | Sempre `sha256`                  | `sha256`                                 |
| `v`         | Versão da API                      | `2.0`                                    |
| `sign`      | Assinatura calculada               | `A1B2C3...` (64 chars hex uppercase)     |

### Uso direto do módulo auth

```python
from backend.integracoes.aliexpress.auth import preparar_requisicao

params = preparar_requisicao(
    app_key    = "MINHA_KEY",
    app_secret = "MEU_SECRET",
    method     = "aliexpress.affiliate.link.generate",
    params_extras = {
        "source_values":       "https://aliexpress.com/item/123.html",
        "promotion_link_type": "0",
    }
)
# params já contém 'sign' calculado, pronto para enviar
```

---

## 4. Cliente HTTP

### Inicialização

```python
from backend.integracoes.aliexpress import AliExpressClient

# Lê credenciais do .env automaticamente
client = AliExpressClient()

# Com parâmetros explícitos (útil em testes)
client = AliExpressClient(
    app_key     = "MINHA_KEY",
    app_secret  = "MEU_SECRET",
    tracking_id = "meu_tracking",
    dry_run     = False,
)

# Modo de teste — NÃO faz chamadas reais
client_teste = AliExpressClient(
    app_key    = "FAKE",
    app_secret = "FAKE",
    dry_run    = True,
)
```

### Método `chamar()`

```python
resposta = client.chamar(
    method = "aliexpress.affiliate.product.query",
    params = {
        "keywords":  "air fryer",
        "page_no":   "1",
        "page_size": "20",
    }
)
```

### Comportamento de retry

| Situação          | Comportamento                                           |
|-------------------|---------------------------------------------------------|
| Erro de rede      | Retry com backoff exponencial (2s → 4s → 8s), máx 3x  |
| Rate limit (429)  | Pausa de 60s e retry automático                         |
| Erro de auth      | Lança `AliExpressAuthError` imediatamente (sem retry)  |
| Erro de negócio   | Lança `AliExpressAPIError` imediatamente               |

---

## 5. Funções de produtos

### 5.1 Buscar produtos por keyword

```python
from backend.integracoes.aliexpress.produtos import buscar_produtos

resultado = buscar_produtos(
    client,
    keyword      = "air fryer",
    pagina       = 1,
    limite       = 20,
    ordenar_por  = "LAST_VOLUME_DESC",   # ou DISCOUNT_DESC, SALE_PRICE_ASC, etc.
    categoria_id = "",                    # opcional
)

# resultado = {
#   "produtos": [ {...produto normalizado...}, ... ],
#   "total":    1543,
#   "pagina":   1,
#   "limite":   20,
#   "keyword":  "air fryer",
# }
```

**Opções de ordenação (`ordenar_por`):**

| Valor                | Descrição              |
|----------------------|------------------------|
| `LAST_VOLUME_DESC`   | Maior volume de vendas |
| `DISCOUNT_DESC`      | Maior desconto         |
| `SALE_PRICE_ASC`     | Menor preço            |
| `SALE_PRICE_DESC`    | Maior preço            |
| `COMMISSION_RATE_DESC` | Maior comissão       |

### 5.2 Detalhes de um produto

```python
from backend.integracoes.aliexpress.produtos import obter_detalhes_produto

produto = obter_detalhes_produto(client, produto_id="9876543210")
```

### 5.3 Hot products

```python
from backend.integracoes.aliexpress.produtos import obter_hot_products

resultado = obter_hot_products(
    client,
    categoria_id = "",    # opcional
    pagina       = 1,
    limite       = 20,
)
```

### 5.4 Extrair imagens e preços

```python
from backend.integracoes.aliexpress.produtos import (
    obter_imagens_produto,
    obter_preco_produto,
)

imagens = obter_imagens_produto(produto)
# ["https://ae01.alicdn.com/img1.jpg", "https://ae01.alicdn.com/img2.jpg"]

preco = obter_preco_produto(produto)
# {
#   "preco_atual":    89.99,
#   "preco_original": 149.99,
#   "desconto_pct":   40,
#   "tem_desconto":   True,
#   "moeda":          "USD",
#   "formatado":      "USD 89.99 (40% off)",
# }
```

### Schema normalizado do produto

Todos os produtos retornados pelas funções acima seguem este schema:

```python
{
    "produto_id":          "9876543210",
    "titulo":              "Air Fryer Digital 5L",
    "preco_atual":         89.99,
    "preco_original":      149.99,
    "desconto_pct":        40,
    "moeda":               "USD",
    "nota_media":          4.9,      # escala 0-5
    "avaliacoes":          2345,
    "comissao_rate":       "8.50",   # string percentual
    "comissao_percentual": 8.5,      # float
    "imagem_principal":    "https://ae01.alicdn.com/imagem.jpg",
    "imagens_extras":      ["https://...", "https://..."],
    "url":                 "https://aliexpress.com/item/9876543210.html",
    "shop_id":             "555001",
    "shop_url":            "https://aliexpress.com/store/555001",
    "categoria_nome":      "Kitchen Appliances",
    "categoria_id":        "901",
    "fonte":               "aliexpress",
}
```

---

## 6. Funções de afiliados

### 6.1 Gerar link afiliado

```python
from backend.integracoes.aliexpress.afiliados import gerar_link_afiliado

resultado = gerar_link_afiliado(
    client,
    url_produto = "https://aliexpress.com/item/9876543210.html",
    tracking_id = "meu_tracking",   # opcional — usa client.tracking_id
    tipo_link   = 0,                 # 0 = normal | 2 = encurtado
)

# resultado = {
#   "url_afiliado":  "https://s.click.aliexpress.com/e/_...",
#   "url_original":  "https://aliexpress.com/item/9876543210.html",
#   "tracking_id":   "meu_tracking",
#   "comissao_rate": "8.50%",
#   "valido":        True,
# }
```

### 6.2 Gerar links em lote

```python
from backend.integracoes.aliexpress.afiliados import gerar_links_em_lote

urls = [
    "https://aliexpress.com/item/1.html",
    "https://aliexpress.com/item/2.html",
    "https://aliexpress.com/item/3.html",
]

resultados = gerar_links_em_lote(
    client,
    urls               = urls,
    tracking_id        = "meu_tracking",
    continuar_em_erros = True,   # False = aborta na primeira falha
)

# Cada item pode ter campo 'erro' se continuar_em_erros=True
```

### 6.3 Consultar comissão de um produto

```python
from backend.integracoes.aliexpress.afiliados import obter_comissao_produto

comissao = obter_comissao_produto(client, produto_id="9876543210")

# comissao = {
#   "produto_id":          "9876543210",
#   "comissao_rate":       "8.50",
#   "comissao_percentual": 8.5,
#   "comissao_tipo":       "base",
#   "moeda":               "USD",
# }
```

---

## 7. Coletor do discovery engine

O arquivo `coleta/fontes/aliexpress_coletor.py` integra automaticamente o AliExpress ao pipeline principal.

### Uso pelo engine

O discovery engine chama `coletar()` automaticamente ao encontrar o arquivo na pasta `coleta/fontes/`. Nenhuma configuração adicional é necessária além do `.env`.

### Execução manual

```bash
python coleta/fontes/aliexpress_coletor.py
```

### Personalizar keywords de busca

Edite a constante `KEYWORDS_TENDENCIA` no início do arquivo:

```python
KEYWORDS_TENDENCIA: list[tuple[str, str]] = [
    ("air fryer",    "cozinha"),
    ("smartwatch",   "tecnologia"),
    ("yoga mat",     "fitness"),
    # adicione pares (keyword, categoria_engine) aqui
]
```

### Schema de saída (campos do discovery engine)

| Campo               | Tipo    | Descrição                               |
|---------------------|---------|------------------------------------------|
| `fonte`             | str     | Sempre `"aliexpress"`                   |
| `timestamp_coleta`  | str     | ISO 8601 UTC                            |
| `nome`              | str     | Título do produto                        |
| `url`               | str     | URL do produto                           |
| `preco_atual`       | float   | Preço com desconto em USD               |
| `categoria`         | str     | Categoria válida do engine              |
| `nota_media`        | float   | Nota de 0 a 5                           |
| `avaliacoes`        | int     | Número de avaliações                    |
| `tendencia_estimada`| str     | `subindo` \| `estavel` \| `caindo`      |

**Campos extras** (enriquecimento, não validados pelo engine):

| Campo               | Tipo    |
|---------------------|---------|
| `produto_id`        | str     |
| `preco_original`    | float   |
| `desconto_pct`      | int     |
| `moeda`             | str     |
| `imagem_url`        | str     |
| `comissao_rate`     | str     |
| `comissao_percentual` | float |
| `url_afiliado`      | str     |

---

## 8. Tratamento de erros

### Hierarquia de exceções

```
AliExpressError                   ← base (catch genérico)
├── AliExpressConfigError         ← credenciais ausentes no .env
├── AliExpressAuthError           ← API rejeitou as credenciais
├── AliExpressAPIError            ← erro de negócio da API
│       .code     → código de erro (ex: "50000100")
│       .sub_code → sub-código quando presente
├── AliExpressRateLimitError      ← limite de requisições atingido
│       .retry_after → segundos para aguardar
└── AliExpressNetworkError        ← falha de rede / timeout
```

### Padrão de uso recomendado

```python
from backend.integracoes.aliexpress import AliExpressClient
from backend.integracoes.aliexpress.exceptions import (
    AliExpressConfigError,
    AliExpressAuthError,
    AliExpressRateLimitError,
    AliExpressAPIError,
    AliExpressError,
)

try:
    client = AliExpressClient()
    resultado = buscar_produtos(client, "air fryer")

except AliExpressConfigError as e:
    # Credenciais não configuradas — verificar .env
    logger.error("Configuração incompleta: %s", e)

except AliExpressAuthError as e:
    # APP_KEY ou APP_SECRET inválidos
    logger.error("Autenticação falhou: %s", e)

except AliExpressRateLimitError as e:
    # Aguardar e tentar novamente (já há retry automático no client)
    logger.warning("Rate limit: aguardar %ds", e.retry_after)

except AliExpressAPIError as e:
    # Erro de negócio da API
    logger.warning("Erro de API [%s]: %s", e.code, e)

except AliExpressError as e:
    # Qualquer outro erro da integração
    logger.error("Erro AliExpress: %s", e)
```

---

## 9. Testes

### Rodar todos os testes

```bash
python -m pytest tests/test_aliexpress.py -v
```

### Rodar por categoria

```bash
# Apenas testes de autenticação
python -m pytest tests/test_aliexpress.py -v -k "TestAuth"

# Apenas testes do cliente
python -m pytest tests/test_aliexpress.py -v -k "TestAliExpressClient"

# Apenas testes de produtos
python -m pytest tests/test_aliexpress.py -v -k "TestProdutos"

# Apenas testes de afiliados
python -m pytest tests/test_aliexpress.py -v -k "TestAfiliados"

# Apenas testes do coletor
python -m pytest tests/test_aliexpress.py -v -k "TestAliexpressColetor"
```

### Cobertura de testes

Os testes cobrem:

| Área              | Cobertura                                                      |
|-------------------|----------------------------------------------------------------|
| `auth.py`         | Algoritmo HMAC-SHA256, timestamp, mascaramento de credenciais |
| `exceptions.py`   | Hierarquia, atributos, mensagens, catch genérico              |
| `client.py`       | Inicialização, dry_run, processamento de resposta, erros      |
| `produtos.py`     | Busca, detalhes, hot products, imagens, preços, normalização  |
| `afiliados.py`    | Link individual, lote, comissão, erros, continuar_em_erros    |
| `coletor.py`      | Schema, categorias, tendências, ausência de credenciais       |

> **Todos os testes usam mocks — nenhuma chamada real à API é feita.**

---

## 10. Segurança

### Regras aplicadas nesta integração

| Regra | Implementação |
|-------|---------------|
| APP_SECRET nunca aparece em logs | `mascarar_credenciais()` em auth.py |
| APP_SECRET nunca é enviado na requisição | Apenas a assinatura calculada é enviada |
| Credenciais nunca em código-fonte | Leitura exclusiva do `.env` |
| `.env` fora do controle de versão | Listado no `.gitignore` |
| `dry_run=True` para testes | Nenhuma chamada real sem confirmação |
| Retry sem expor credenciais nos logs | Logs mascarados no backoff |

### Arquivo `.gitignore` recomendado

Verifique se as linhas abaixo estão no `.gitignore`:

```
.env
.env.local
*.env
```

---

## 11. Referências

- [AliExpress Open Platform — Documentação oficial](https://openservice.aliexpress.com/doc/doc.htm#naviId=58)
- [Portal de afiliados AliExpress](https://portals.aliexpress.com/)
- [Catálogo de métodos de afiliados](https://openservice.aliexpress.com/doc/doc.htm#naviId=26)
- [Códigos de erro da API](https://openservice.aliexpress.com/doc/doc.htm#naviId=88)

### Métodos utilizados nesta integração

| Método                                        | Função no projeto        |
|-----------------------------------------------|--------------------------|
| `aliexpress.affiliate.product.query`          | `buscar_produtos()`      |
| `aliexpress.affiliate.product.detail.get`     | `obter_detalhes_produto()` + `obter_comissao_produto()` |
| `aliexpress.affiliate.hotproduct.query`       | `obter_hot_products()`   |
| `aliexpress.affiliate.link.generate`          | `gerar_link_afiliado()`  |
