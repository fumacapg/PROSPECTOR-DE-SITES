# Prospector de Sites

Prospecção semi-automática de clientes com sites ruins: acha, redesenha, publica e oferta.

## O ciclo

1. `/setup` — roda uma vez: assinatura, nichos padrão, escolha da hospedagem (HostGator ou Hostinger) e dados de acesso (com teste de publicação).
2. `/prospectar [nicho] [cidade]` — busca no Google Maps negócios nota ≥ 4.7 com site fraco e gera `leads.md` com e-mail, motivo e ranking (padrão: 10 leads).
3. `/redesenhar` — recria as páginas dos 5+ melhores leads com estética premium, mantendo o conteúdo, logo e paleta reais do cliente.
4. `/editor [cliente]` — gera versão editável no navegador (textos e imagens) com exportação da página final.
5. `/publicar [cliente|todos]` — sobe na hospedagem escolhida (HostGator ou Hostinger), gera a página-capa de apresentação (antes/depois personalizado, `proposta.html`) e só conclui com HTTPS validado.
6. `/proposta [cliente|todos]` — escreve o e-mail (rapport, sem preço), passa pela checklist anti-spam e cria o rascunho no Gmail com a página-capa como único link.
7. `/respostas` — verifica no Gmail quem respondeu e atualiza o dashboard sozinho (dica: agende a verificação diária).
8. `/followup [cliente]` — 3+ dias sem resposta? Gera o follow-up gentil (1 por lead, nunca repete) já checando quem respondeu antes.
9. `/contrato [cliente]` — cliente fechou? Gera a minuta do contrato (pronta pra PDF) com os dados do negócio e deixa o rascunho no Gmail.

## Manual e publicação automática

O pacote inclui `manual.html` — o manual completo do usuário, copiado pra pasta no `/setup` e **atualizado a cada versão do plugin**. Na HostGator, a publicação é automática: senha preenchida uma vez no `prospector-config.json` + `publicar-agora.bat` (2 cliques) quando a rede do sandbox não alcança o FTP — sem login no cPanel. Na Hostinger, a publicação passa pelo MCP `hostinger-hosting` (ver "Hospedagem" abaixo) — sem scripts locais.

## Dashboard local

O plugin mantém um painel de controle na sua pasta: `prospector.db` (banco SQLite) + `dashboard.html`. Duplo clique em `iniciar-dashboard.bat` (requer Python) abre o painel completo em http://localhost:8765 — kanban com drag & drop, edição, exclusão, funil, comparador antes/depois integrado, follow-ups, controle de contratos (pendente/enviado/assinado) e painel financeiro (recebido, a receber e MRR das manutenções), tudo salvo no banco.

## Hospedagem

Duas opções, escolhidas no `/setup`:

- **HostGator** (acesso ao cPanel) — publicação via FTP/publicador automático local, configurada pela aba Configurações do dashboard (nunca pelo chat).
- **Hostinger** (token de API do hPanel, usado pelo MCP `hostinger-hosting`) — cada cliente vira um subdomínio próprio (`cliente.dominio.com`), já que a ferramenta de deploy da Hostinger não publica em subpasta. O `.mcp.json` do plugin está configurado com `npx.cmd` (requer Windows); em Mac/Linux troque para `npx`. O token nunca é digitado no chat: fica na variável de ambiente `HOSTINGER_API_TOKEN` do sistema operacional.

## Requisitos

- Extensão Claude in Chrome conectada (prospecção no Maps e fallback de deploy)
- Conector do Gmail (rascunhos de proposta)
- Pasta conectada no Cowork (armazena config, leads e sites)
- Hospedagem HostGator (com acesso ao cPanel) **ou** Hostinger (ver seção acima)

## Onde ficam os dados

Tudo na pasta conectada: `prospector-config.json` (preferências e credenciais — a senha do cPanel fica em texto no seu computador, nunca no chat), `leads.md` (pipeline) e `sites/[slug]/` (páginas criadas).
