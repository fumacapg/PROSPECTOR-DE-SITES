---
description: Publica as páginas redesenhadas na hospedagem configurada (HostGator ou Hostinger) e retorna as URLs públicas
argument-hint: "[nome do cliente ou todos]"
---

Publique páginas na hospedagem escolhida pelo usuário, seguindo a skill `deploy-hostgator` ou `deploy-hostinger` conforme o `prospector-config.json`.

## Passos

1. Leia `prospector-config.json` e confira `hospedagem.provedor` (`"hostgator"` ou `"hostinger"`). Se não estiver definido, pergunte qual o usuário quer usar e siga o passo 4 do `/setup` para coletar os dados daquele provedor (HostGator: nunca pelo chat, sempre via aba Configurações do dashboard; Hostinger: token via variável de ambiente). Não prossiga sem eles.
2. Determine o que publicar: `$ARGUMENTS` (um cliente ou "todos"), ou liste as páginas com status `redesenhado` em `leads.md` e pergunte.
3. **Gere a página-capa de cada cliente**: preencha `references/capa-proposta-template.html` (skill `proposta-email`) com os dados do lead + assinatura do config e salve como `sites/[slug]/proposta.html`. É ela que vai no e-mail de proposta.
4. **Publique seguindo a skill correspondente ao provedor configurado**:
   - **HostGator** (`deploy-hostgator`), nesta ordem: tente o FTP silencioso do sandbox; se a rede bloquear, use o publicador automático local — garanta os 4 arquivos do publicador na pasta, monte a `fila-publicacao.txt` com página (`index.html`) e capa (`proposta.html`) de cada cliente e aguarde ~90s: a tarefa agendada publica sozinha (confira a fila renomeada e o `publicador-log.txt`). Se a tarefa ainda não foi instalada, peça o duplo clique único no `instalar-publicador.bat`. Sem cPanel, sem login, senha só no config.
   - **Hostinger** (`deploy-hostinger`): use as ferramentas `hosting_*` do MCP `hostinger-hosting` para criar (se ainda não existir) um subdomínio próprio do cliente (`[slug].[dominio]`) e publicar nele tanto a página (`sites/[slug]/[slug].html`, renomeada para `index.html`) quanto a capa (`proposta.html`) — a ferramenta de deploy da Hostinger não suporta subpasta, então cada cliente precisa do seu subdomínio.
5. **Verificação HTTPS (bloqueante)**: abra cada URL com `https://` e confirme que carrega com cadeado válido. Se o HTTPS falhar na HostGator, siga a seção "HTTPS obrigatório" da skill `deploy-hostgator` (AutoSSL no cPanel) antes de considerar publicado; na Hostinger o SSL do subdomínio é automático, mas confirme mesmo assim. Link `http://` NUNCA vai para cliente.
6. Atualize `leads.md` e o banco do dashboard: status `publicado` + URL pública nova.

## Saída

Liste, por cliente: URL da página nova e URL da capa (`.../proposta.html`), ambas testadas em https. Sugira o próximo passo: `/proposta` para enviar os e-mails.
