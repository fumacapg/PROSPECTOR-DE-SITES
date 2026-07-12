---
name: deploy-hostinger
description: Esta skill deve ser usada ao publicar páginas na hospedagem Hostinger via MCP — criar um subdomínio por cliente, enviar os arquivos estáticos e verificar a URL pública. Acione quando o usuário disser "publicar", "subir o site", "colocar no ar", "deploy", "hostinger" ou rodar /publicar (quando o provedor configurado for Hostinger) ou o teste de conexão do /setup.
---

# Deploy na Hostinger (via MCP)

Publicar cada página do cliente usando as ferramentas MCP do servidor `hostinger-hosting` (pacote `hostinger-api-mcp`, declarado em `.mcp.json` do plugin). Diferente da HostGator, aqui não há FTP/cPanel — tudo passa pelas ferramentas `hosting_*` expostas pelo MCP.

## Pré-requisito

O MCP `hostinger-hosting` carrega sozinho quando o plugin é ativado, mas precisa da variável de ambiente `HOSTINGER_API_TOKEN` definida no sistema operacional do usuário (gerada em hPanel → Perfil → Informações da conta → API → "Gerar token").

**O token NUNCA deve ser digitado no chat.** Ele deve ser configurado como variável de ambiente do próprio sistema operacional (fora do Claude Code), e o Claude Code precisa ser reiniciado depois disso para o MCP carregar o valor. Se as ferramentas `hosting_*` não aparecerem disponíveis (busque por elas antes de prosseguir), avise o usuário que o token provavelmente não está configurado ou que falta reiniciar o Claude Code — não tente adivinhar ou pedir o token de outra forma.

## Por que subdomínio e não subpasta

A ferramenta de deploy da Hostinger (`hosting_deployStaticWebsite`) só aceita um arquivo compactado com os arquivos estáticos e publica na raiz do site/domínio de destino — **não existe parâmetro de subpasta**. Por isso, cada cliente vira **1 subdomínio próprio** (ex.: `jessica-nutri.dominio.com`), que funciona como um "site" isolado na Hostinger, com sua própria raiz. Isso substitui o padrão `dominio.com/clientes/[slug]/` usado na HostGator — na Hostinger o equivalente é `[slug].dominio.com`. Planos Premium/Business da Hostinger suportam até 100 subdomínios, então não há limite prático para o volume de clientes.

## Passo 1 — Garantir o subdomínio do cliente

1. Use `hosting_listWebsitesV1` para confirmar o site principal correspondente a `hostinger.dominio` (salvo em `prospector-config.json`).
2. Verifique se já existe um subdomínio para o slug do cliente (ferramenta de listagem de subdomínios do site). Se não existir, crie um novo subdomínio com o nome do slug (ex.: `jessica-nutri`) apontando para aquele site — use a ferramenta de criação de subdomínio disponível no MCP (inspecione as ferramentas `hosting_*`/`domains_*` carregadas para achar o nome exato; os parâmetros variam por versão do pacote).
3. Se o usuário ainda não tiver domínio próprio configurado, use `hosting_generateAFreeSubdomainV1` para gerar um subdomínio gratuito (`*.hostingersite.com`) por cliente em vez de um domínio próprio.

## Passo 2 — Publicar

Empacote os arquivos do cliente prontos para servir — a página (`sites/[slug]/[slug].html` renomeado para `index.html`) e, se existir, a capa de proposta (`sites/[slug]/proposta.html`), mais eventuais assets/imagens — em um arquivo compactado (a ferramenta não aceita build step) e envie via `hosting_deployStaticWebsite`, apontando para o subdomínio do cliente criado no Passo 1 — nunca para o domínio principal (isso sobrescreveria o site raiz e afetaria todos os outros clientes).

## Passo 3 — Verificação (obrigatória, bloqueante)

1. Buscar a URL pública resultante (`https://[slug].[dominio]/` e, se aplicável, `.../proposta.html`) e confirmar HTTP 200 + conteúdo correto (título do cliente presente). Deploys na Hostinger podem levar alguns minutos para propagar — se der 404 na primeira tentativa, aguardar um pouco e checar de novo antes de diagnosticar erro.
2. **HTTPS obrigatório**: o SSL de subdomínios Hostinger é emitido automaticamente, mas confirme que a URL carrega com cadeado válido antes de considerar publicado — link `http://` NUNCA vai para cliente.
3. Atualize `leads.md` + dashboard com status `publicado` e a URL.

## Organização

- 1 subdomínio por cliente, slug em kebab-case sem acentos (ex.: `jessica-nutri`).
- Nunca reaproveitar ou sobrescrever o subdomínio/site de outro cliente.
- Página de teste do setup: criar um subdomínio `teste` e publicar um "Funcionou!" simples para validar a conexão.
