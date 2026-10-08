# Cobrança WhatsApp — App Web

Painel web que lê os dados exportados do **Gestor de Empréstimos** e envia
mensagens automáticas de cobrança pelo WhatsApp: aviso antes do vencimento,
no dia do vencimento, e reenvios periódicos para parcelas atrasadas.

## Rodando 24/7 sem precisar ligar seu computador (passo a passo, só pelo navegador)

Isso sobe o app na nuvem direto, sem instalar nada no seu computador e sem
usar terminal:

1. **Suba este projeto para o GitHub** (sem usar git no terminal):
   - Vá em https://github.com/new, crie um repositório (pode ser privado).
   - Na página do repositório recém-criado, clique em **"uploading an
     existing file"** e arraste todos os arquivos desta pasta (exceto a
     pasta `node_modules`, que não existe ainda mesmo).
   - Clique em "Commit changes".
2. **Crie uma conta no Railway**: https://railway.app (pode entrar com a
   conta do GitHub).
3. Clique em **"New Project" → "Deploy from GitHub repo"** e selecione o
   repositório que você acabou de criar.
4. O Railway detecta automaticamente que é um projeto Node.js e já roda
   `npm install` + `npm start` (o arquivo `railway.json` incluso já define
   isso). Em "Settings → Networking", gere um domínio público (botão
   "Generate Domain") — é o link do seu painel.
5. **Importante — adicione um Volume** (disco persistente) para a pasta
   `data/`: em "Settings → Volumes", crie um volume e monte em `/app/data`.
   Sem isso, toda vez que o Railway reiniciar o serviço, o backup
   importado e as configurações da Z-API se perdem.
6. Abra o domínio gerado no navegador — é o mesmo painel: importe o
   backup, configure a Z-API, ative a cobrança automática.

O Railway tem um período de teste gratuito; depois disso cobra por uso
(um app pequeno como este custa poucos dólares por mês). Alternativa
gratuita com limitações: **Render.com** (plano free "Web Service") — só
que no plano free ele "dorme" depois de um tempo sem acesso, o que
interrompe o agendamento automático; nesse caso é melhor usar o plano
pago mais barato ou configurar algo para "acordar" o serviço no horário
da cobrança.

## Alternativa: instalar e rodar no seu computador

```bash
npm install
npm start
```

Abra **http://localhost:3000** no navegador — é o painel. (Só funciona
enquanto o processo estiver ativo no seu computador.)

## 2. No painel, em ordem

1. **Importe os dados**: no Gestor de Empréstimos, aba **Backup → Exportar
   backup**, baixe o `.json` e envie na seção "1. Importar dados do app".
2. **Configure a Z-API**: crie uma conta em https://www.z-api.io, crie uma
   instância, escaneie o QR code com o WhatsApp que vai enviar as mensagens,
   e cole o Instance ID / Token / Client-Token no painel.
3. **Ajuste as regras**: nome da empresa (aparece na mensagem), quantos
   dias antes do vencimento avisar, cada quantos dias reenviar cobrança de
   atrasados, e o horário do envio diário.
4. Marque **"Ativar cobrança automática diária"** e clique em **Salvar**.

Use **"👁 Ver quem seria cobrado hoje"** para checar antes de ativar de
verdade, e **"🚀 Enviar cobranças agora"** para disparar manualmente quando
quiser, sem esperar o horário agendado.

## Importante: isso precisa ficar rodando sempre

A cobrança só roda sozinha no horário configurado enquanto o processo
estiver de pé. Seguindo o passo a passo do Railway acima, ele fica de pé
sozinho na nuvem — você não precisa fazer nada além de configurar o
volume persistente (passo 5).

## Manter os dados sempre atualizados

O robô só conhece os clientes/empréstimos do último backup que você
importou pelo painel — ele não lê o banco de dados do app em tempo real.
Reimporte o backup sempre que fizer mudanças relevantes (novo cliente,
nova parcela paga, renegociação etc.).

## Observações importantes

- O WhatsApp pode bloquear números que enviam muitas mensagens automáticas
  não solicitadas — use com responsabilidade, só para clientes que já têm
  relação comercial com você.
- A Z-API funciona "simulando" um WhatsApp comum; para volume alto ou uso
  totalmente dentro das regras do WhatsApp, considere migrar para a Meta
  Cloud API oficial com templates aprovados (troque a função em
  `src/whatsapp.js` — o resto do projeto não muda).
