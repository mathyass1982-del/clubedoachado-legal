# TikTok — Guia de Aprovação de Produção

## PARTE 1 — Publicar GitHub Pages (fazer PRIMEIRO)

### Passo 1: Criar repositório no GitHub

1. Acesse https://github.com/new
2. Nome do repositório: `clubedoachado-legal`
3. Visibilidade: **Public** (obrigatório para GitHub Pages)
4. Clique em **Create repository**

### Passo 2: Subir os arquivos

No terminal (ou GitHub Desktop):

```bash
cd "C:\Users\roger\Documents\Minha Mente\Achadinhos Projeto\docs"
git init
git add .
git commit -m "Legal pages: Privacy Policy + Terms of Service"
git remote add origin https://github.com/SEU_USUARIO/clubedoachado-legal.git
git push -u origin main
```

### Passo 3: Ativar GitHub Pages

1. No repositório, vá em **Settings** → **Pages**
2. Source: **Deploy from a branch**
3. Branch: **main** / pasta: **/ (root)**
4. Clique **Save**
5. Aguarde 2-3 minutos

### Passo 4: Verificar URLs (substitua SEU_USUARIO pelo seu username do GitHub)

- Home:            https://SEU_USUARIO.github.io/clubedoachado-legal/
- Privacy Policy:  https://SEU_USUARIO.github.io/clubedoachado-legal/legal/privacy-policy.html
- Terms of Service: https://SEU_USUARIO.github.io/clubedoachado-legal/legal/terms-of-service.html

---

## PARTE 2 — Preencher o formulário do TikTok Developer

Acesse: https://developers.tiktok.com/apps/

### App Name
```
Clube do Achado
```

### App Description (campo "Description")
```
Clube do Achado é um sistema automatizado de marketing de afiliados que publica
vídeos curtos de recomendação de produtos no TikTok (@clubedoachadoia) e no
Instagram (@achadinhosia82). O sistema usa a TikTok Content Posting API para
publicar automaticamente 1 a 3 vídeos por dia com recomendações de produtos
do Mercado Livre e AliExpress, incluindo links de afiliado. Os vídeos são
gerados automaticamente a partir de dados de produtos (imagem, preço, título)
e publicados no horário de maior engajamento. A conta é operada pelo proprietário
do projeto — não há usuários finais além do operador.
```

### Use Case para o scope "video.publish" (campo específico do scope)
```
Nossa aplicação publica automaticamente vídeos de recomendação de produtos na
conta TikTok @clubedoachadoia, operada pelo proprietário do projeto (Rogerio
Matias). Os vídeos são gerados a partir de dados de produtos coletados via APIs
do Mercado Livre e AliExpress, e publicados 1-3 vezes por dia nos horários de
pico (18h-21h horário de Brasília). Respeitamos rigorosamente o limite de 15
publicações por 24 horas. O operador revisa e aprova cada lote de vídeos antes
da publicação automática. Não há usuários finais — a integração é exclusiva
para a conta do operador.
```

### Privacy Policy URL
```
https://SEU_USUARIO.github.io/clubedoachado-legal/legal/privacy-policy.html
```

### Terms of Service URL
```
https://SEU_USUARIO.github.io/clubedoachado-legal/legal/terms-of-service.html
```

### Platform
```
Web
```

### Category
```
Entertainment / Content Creation
```

---

## PARTE 3 — Verificação de Domínio (DNS)

O TikTok vai gerar uma string de verificação no formato:
`tiktok-developers-site-verification=XXXXXXXXXXXXXXXX`

Para GitHub Pages **sem domínio customizado**, você precisará usar uma alternativa:
- Criar um arquivo `tiktok-domain-verification.txt` na raiz do repositório
- OU adicionar uma meta tag de verificação no `index.html`

O TikTok também aceita verificação via arquivo acessível em:
`https://SEU_USUARIO.github.io/clubedoachado-legal/tiktok-domain-verification.txt`

**Conteúdo do arquivo:** (o TikTok vai te dar o código)
```
tiktok-developers-site-verification=COLE_O_CODIGO_AQUI
```

---

## PARTE 4 — Demo Video (obrigatório)

O TikTok exige 1 vídeo mostrando a integração funcionando no sandbox.

### O que gravar (use Loom ou OBS):

1. **Tela 1:** Mostrar o portal TikTok com o app em sandbox
2. **Tela 2:** Executar `python scripts/testar_tiktok_oauth.py --info-usuario` (após completar OAuth)
3. **Tela 3:** Mostrar o arquivo `publicar_tiktok.py` e executar em dry-run:
   ```
   python scripts/meta/publicar_tiktok.py --dry-run
   ```
4. **Tela 4:** Mostrar o vídeo gerado na pasta `assets/posts/gerados/tiktok/`
5. **Tela 5:** Mostrar o guia manual de upload e a legenda gerada

**Duração recomendada:** 2-5 minutos
**Ferramenta gratuita:** https://www.loom.com (grava tela, exporta mp4)

---

## PARTE 5 — Compliance de UX (já implementado no código)

O `publicar_tiktok.py` já tem:
- `privacy_level: "PUBLIC_TO_EVERYONE"` configurável
- `disable_comment: False` respeitando configurações do criador
- `disable_duet: False`
- `disable_stitch: False`

Para o formulário, informe que o operador seleciona manualmente as configurações
antes de cada lote de publicação via CLI.

---

## PARTE 6 — Próximos passos após submissão

| Etapa | Prazo estimado |
|-------|---------------|
| Review inicial TikTok | 3-7 dias úteis |
| Possível solicitação de info adicional | +3-5 dias |
| Aprovação do scope video.publish | 2-6 semanas total |
| Audit pós-aprovação (para posts públicos) | Após aprovação |

**Durante a espera:** Complete o OAuth para ter os tokens prontos e testar
todas as funcionalidades disponíveis no sandbox (user.info.*).

---

## Checklist Final

- [ ] GitHub Pages publicado e acessível
- [ ] Privacy Policy online e testada
- [ ] Terms of Service online e testada
- [ ] Formulário do TikTok preenchido
- [ ] Domain verification configurado
- [ ] Demo video gravado e enviado
- [ ] OAuth completo (tokens no .env)
- [ ] Teste de user.info em sandbox funcionando
