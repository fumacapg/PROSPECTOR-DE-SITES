---
description: Publica as páginas redesenhadas na hospedagem configurada (HostGator ou Hostinger) e retorna as URLs públicas
argument-hint: "[nome do cliente ou todos]"
---

Publique páginas na hospedagem escolhida pelo usuário, seguindo a skill `deploy-hostgator` ou `deploy-hostinger` conforme o `prospector-config.json`.

## Passos

1. Leia `prospector-config.json` e confira `hospedagem.provedor` (`"hostgator"` ou `"hostinger"`). Se não estiver definido, pergunte qual o usuário quer usar e siga o passo 4 do `/setup` para coletar os dados daquele provedor. Não prossiga sem eles.
2. Determine o que publicar: `$ARGUMENTS` (um cliente ou "todos"), ou liste as páginas com status `redesenhado` em `leads.md` e pergunte.
3. Para cada página, siga a skill correspondente ao provedor configurado:
   - **HostGator** (`deploy-hostgator`): envie `sites/[slug]/[slug].html` para `public_html/[pastaBase]/[slug]/index.html` (o arquivo local leva o nome do cliente; no servidor ele vira index.html), tentando primeiro o método programático (FTP) e usando o fallback pelo navegador (cPanel File Manager) se necessário.
   - **Hostinger** (`deploy-hostinger`): use as ferramentas `hosting_*` do MCP `hostinger-hosting` para criar (se ainda não existir) um subdomínio próprio do cliente (`[slug].[dominio]`) e publicar `sites/[slug]/[slug].html` (renomeado para `index.html`) nele — a ferramenta de deploy da Hostinger não suporta subpasta, então cada cliente precisa do seu subdomínio.
4. Verifique cada publicação abrindo a URL pública (`https://[dominio]/[pastaBase]/[slug]/` na HostGator, ou `https://[slug].[dominio]/` na Hostinger) e confirmando que a página carrega corretamente.
5. Atualize `leads.md`: status `publicado` + coluna com a URL pública.

## Saída

Liste as URLs públicas de cada cliente. Sugira o próximo passo: `/proposta` para enviar os e-mails.
