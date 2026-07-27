# Portal GR — BaseGR

Painéis internos de controle manual: **GR Consórcio**, **GRO** e **Comissões**.
Acesso restrito — login compartilhado com o CRM (mesmo projeto Supabase), sem tela de cadastro.

## Arquivos

- `gr.html` — Dashboard Executivo GR Consórcio (financeiro, pagamentos por dia de vencimento, cancelados, vendas, ranking)
- `gro.html` — mesmo painel, para a GRO
- `comissoes.html` — planilha de comissões por consultor

## Como abrir

São páginas estáticas — abra qualquer uma direto pelo GitHub Pages (depois de habilitado em Settings → Pages) ou publique num servidor estático qualquer. Não precisa de build nem servidor próprio.

## Login

Mesmo e-mail/senha já usados no CRM (`prquggwhjcvenzuziaox`). Acesso liberado só para 2 contas (Deivid e Rafael Messias) — qualquer outro login é rejeitado automaticamente, mesmo que a senha esteja correta. Pra adicionar/trocar quem tem acesso, é preciso editar a lista `PGR_ALLOWED` no início do `<script>` de cada arquivo **e** a policy correspondente no Supabase (tabela `portal_gr_estado` / `portal_comissoes_estado`).

## Onde os dados ficam

- **Fonte de verdade**: Supabase, tabelas `portal_gr_estado` (uma linha por empresa, GR/GRO) e `portal_comissoes_estado`.
- **Backup local automático**: cada salvamento também grava no `localStorage` do navegador — se a internet cair, o app continua funcionando com o último dado local.
- **Histórico/auditoria**: toda vez que alguém salva, uma cópia completa vai pra `portal_gr_historico` (retenção indefinida, sempre cobre mais que 60 dias). Consulta:
  ```sql
  select * from portal_gr_historico where empresa='GR' order by criado_em desc;
  ```

## Importar dados

Cada painel já tem um botão **"Importar JSON"** — sobe um `.json` exportado por este mesmo app (ou pelo botão "Exportar JSON") e substitui/mescla com os dados atuais. Depois de importar, o próximo "Salvar" já sincroniza com o Supabase.

## Bugs corrigidos nessa integração

- Dados reais de uma exportação anterior estavam cravados direto no HTML (visíveis via "view-source" mesmo sem logar) — limpo em `gr.html`, `gro.html` e `comissoes.html`.
- `comissoes.html` nunca lia do `localStorage` no carregamento (só escrevia) — agora lê antes de buscar a nuvem.
