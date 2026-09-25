---
title: "Ollama no Proxmox: Sincronizar o Cofre Obsidian com SyncThing"
date_created: 2026-08-22
tags:
  - ollama
  - syncthing
  - obsidian
---

# 🔄 Guia Prático: Sincronização do Cofre Obsidian com SyncThing no Proxmox LXC

> [!info] Este guia descreve o processo de instalação e configuração do **SyncThing** dentro do contêiner LXC do **Ollama** no Proxmox VE. Esta arquitetura permite manter as suas notas no laptop enquanto realiza a sincronização automática e em tempo real para o contêiner de IA para processamento.

---

## Passo 1: Instalação do SyncThing no Contêiner LXC

Acesse o console do seu contêiner LXC do Ollama (via SSH ou pela interface web do Proxmox) e execute os seguintes comandos como `root`:

```bash
# Atualizar as listas de pacotes
apt update

# Instalar o SyncThing
apt install -y syncthing
```

---

## Passo 2: Configuração de Inicialização Automática (Systemd)

Como o contêiner roda sob o usuário `root` por padrão, vamos configurar o serviço systemd do SyncThing para iniciar automaticamente sempre que o contêiner for inicializado:

```bash
# Habilitar o serviço para o usuário root
systemctl enable syncthing@root.service

# Iniciar o serviço imediatamente
systemctl start syncthing@root.service
```

---

## Passo 3: Exposição da Interface Web para a Rede Local

Por padrão, a interface administrativa do SyncThing só escuta conexões locais (`127.0.0.1`). Para que você possa acessá-la e configurá-la pelo navegador do seu laptop, configure-a para escutar em todas as interfaces de rede (`0.0.0.0`):

1. Pare o serviço temporariamente:
   ```bash
   systemctl stop syncthing@root.service
   ```

2. Descubra onde o arquivo de configuração realmente está:
   ```bash
   syncthing --paths
   ```

3. Altere o endereço de escuta de `127.0.0.1` para `0.0.0.0` no arquivo `config.xml`:
   ```bash
   sed -i 's/127.0.0.1:8384/0.0.0.0:8384/g' /root/.local/state/syncthing/config.xml
   ```

4. Reinicie o serviço:
   ```bash
   systemctl start syncthing@root.service
   ```

> [!warning] Segurança
> Expor o SyncThing em `0.0.0.0` o torna acessível a qualquer dispositivo da rede. Prefira fazê-lo somente em redes locais confiáveis. Alternativamente, use o endereço IP específico da interface em vez de `0.0.0.0` para limitar o acesso.

---

## Passo 4: Conexão e Sincronização com o Laptop

1. **No seu Laptop:**
   - Instale o SyncThing (recomenda-se o **SyncTrayzor** para Windows, **SyncThing-macOS** para Mac, ou o pacote `syncthing` nativo no Linux).
   - Abra a interface do SyncThing no seu laptop.

2. **Acessar a Interface do Proxmox:**
   - No navegador do seu laptop, acesse: `http://IP_DO_LXC_OLLAMA:8384`.

3. **Interligar os Dispositivos:**
   - No painel do contêiner (Proxmox), clique em **Ações -> Mostrar ID** e copie o código identificador.
   - No painel do seu laptop, clique em **Adicionar Dispositivo Remoto**, cole o ID e dê um nome (ex: `MiniPC-Ollama`).

4. **Sincronizar a Pasta do Cofre:**
   - No seu laptop, adicione a pasta do seu cofre do Obsidian (ex: `~/caminho/para/o/cofre`).
   - Na aba **Compartilhamento** dessa pasta, marque a caixa correspondente ao dispositivo `MiniPC-Ollama` para compartilhar a pasta com o contêiner.
   - No painel do contêiner, clique em **Aceitar** na notificação que aparecerá e defina o caminho de destino no Proxmox (ex: `/root/obsidian-cofre`).

---

## 💡 Dicas de Uso

- **Monitoramento:** Você pode abrir a interface web de qualquer dispositivo para acompanhar o progresso da sincronização das suas notas.
- **Leitura pela IA:** O seu modelo de IA poderá ler diretamente os arquivos dentro do diretório de destino no LXC (ex: `/root/obsidian-cofre`) de forma instantânea.

## 🔗 Notas Relacionadas
- 🌐 [Proxmox: Ollama via SSH](./ollama--guia-acesso-ssh.md) — Acessar o Ollama no Proxmox via terminal
- 🌐 [Proxmox: Repositório Gratuito](./01-como-ativar-repositorio-gratuito-gui.md) — Configurar repositórios
- 🎓 [T.I. — Mapa de Conteúdo](../README.md) — Índice geral
