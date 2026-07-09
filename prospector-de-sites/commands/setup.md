---
description: Configura o plugin — assinatura, preferências e conexão com a HostGator (roda uma vez)
---

Configure o ambiente do Prospector de Sites. Siga esta ordem:

## 1. Pasta de trabalho

Verifique se há uma pasta do usuário conectada. Se não houver, peça para conectar uma pasta (ex.: "Clientes") usando a ferramenta de solicitação de pasta — tudo (config, leads e sites criados) será salvo nela para persistir entre sessões.

## 2. Verificar config existente

Procure `prospector-config.json` na pasta conectada. Se existir, mostre um resumo (sem exibir a senha) e pergunte o que o usuário quer atualizar. Se não existir, colete os dados abaixo.

## 3. Dados do usuário (perguntar via AskUserQuestion / formulário)

Colete:

- **Assinatura da proposta**: nome completo, como quer se apresentar (ex.: "Designer de páginas de alta conversão") e WhatsApp/telefone de contato.
- **Nichos padrão de prospecção**: sugira nutricionistas, psicólogos, advogados e psiquiatras como ponto de partida, mas deixe o usuário editar livremente.
- **Cidade/região padrão**.
- **Leads qualificados por busca**: padrão 10.
- **Modo de envio da proposta**: padrão "criar rascunho no Gmail para revisão" (recomendado). Alternativa: enviar direto.

## 4. Escolha e conexão da hospedagem

Pergunte qual hospedagem o usuário quer usar: **HostGator** (FTP/cPanel) ou **Hostinger** (via MCP). Salve a escolha em `hospedagem.provedor` (`"hostgator"` ou `"hostinger"`).

### Opção A — HostGator

Pergunte se o usuário já contratou a hospedagem HostGator.

- **Se ainda não contratou**: explique brevemente que ele precisa de um plano que aceite múltiplos sites (plano M ou superior), que ao contratar ganha domínio grátis, e que depois de ativar deve voltar e rodar `/setup` de novo. Salve o config parcial e encerre.
- **Se já contratou**: peça 3 informações do cPanel (elas aparecem na tela inicial do cPanel, coluna "General Information"):
  1. **Usuário** do cPanel
  2. **Domínio principal**
  3. **Servidor** (nome do servidor ou IP, ex.: `br123.hostgator.com.br`)

  **A senha NUNCA deve ser digitada no chat.** Salve o config com o campo `"senha": ""` e instrua o usuário a abrir `prospector-config.json` na pasta conectada e preencher a senha ele mesmo (avisando que ela fica em texto no computador dele). Só depois disso rode o teste de conexão. Nunca exiba, imprima ou registre a senha em nenhuma saída.

### Opção B — Hostinger

O servidor MCP `hostinger-hosting` é declarado no `.mcp.json` do plugin e carrega sozinho. Verifique se as ferramentas `hosting_*` estão disponíveis:

- **Se não estiverem**: peça para o usuário gerar um token em hPanel → Perfil → Informações da conta → API → "Gerar token". **O token NUNCA deve ser digitado no chat.** Instrua o usuário a definir a variável de ambiente `HOSTINGER_API_TOKEN` no sistema operacional dele (fora do Claude Code) e reiniciar o Claude Code para o MCP carregar o valor. Salve o config parcial e encerre até isso ser feito.
- **Se já estiverem disponíveis**: pergunte o domínio principal do site (ou avise que é possível gerar um subdomínio gratuito da Hostinger via `hosting_generateAFreeSubdomainV1` se ele ainda não tiver domínio próprio). Explique que cada cliente vira um subdomínio (ex.: `cliente.dominio.com`), já que a ferramenta de deploy da Hostinger não publica em subpasta. Salve em `hostinger.dominio`.

## 5. Salvar e testar

Salve tudo em `prospector-config.json` na pasta conectada, neste formato:

```json
{
  "assinatura": { "nome": "", "apresentacao": "", "whatsapp": "" },
  "prospeccao": { "nichos": ["nutricionistas", "psicologos", "advogados", "psiquiatras"], "cidade": "", "leadsPorBusca": 10 },
  "envio": { "modo": "rascunho" },
  "hospedagem": { "provedor": "hostgator" },
  "hostgator": { "usuario": "", "dominio": "", "servidor": "", "senha": "", "pastaBase": "clientes" },
  "hostinger": { "dominio": "" }
}
```

Se os dados da hospedagem escolhida foram informados, teste a conexão seguindo a skill correspondente (`deploy-hostgator` ou `deploy-hostinger`): publique uma página de teste simples e informe a URL pública ao usuário. Se o teste falhar, diagnostique (credenciais/token, servidor, método de upload) antes de concluir.

## 6. Encerrar

Confirme o que foi salvo e explique o ciclo: `/prospectar` → `/redesenhar` → `/publicar` → `/proposta`, com `/editor` opcional para ajustes manuais.
