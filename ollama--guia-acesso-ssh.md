---
title: "Ollama no Proxmox: Conversar com IA via SSH"
date_created: 2026-08-22
tags:
  - proxmox/ssh
  - ollama
---

# 🔌 Guia de Acesso Prático: Conversando com a IA via SSH no Proxmox

> [!info] Este guia prático ensina como se conectar ao seu contêiner LXC do **Ollama** rodando no seu servidor **Proxmox VE**, utilizando apenas o terminal do seu laptop pessoal através de uma conexão **SSH** direta, segura e extremamente leve.

Esta abordagem permite que você aproveite o poder de aceleração do seu servidor diretamente no terminal do seu laptop, sem precisar instalar nenhum software adicional, plugin de terceiros ou sobrecarregar a sua máquina local.

---

## 📋 Pré-requisitos

Antes de iniciar, certifique-se de que:

1. O seu contêiner LXC com Ollama está ativo no Proxmox.
2. Você possui o endereço IP do contêiner LXC (ex: `192.168.1.150`).
3. Você se lembra da senha do usuário `root` definida durante a criação do contêiner.
4. O modelo de IA (ex: `gemma`) já está baixado no servidor (caso contrário, ele será baixado automaticamente ao ser executado).

---

## 🚀 Passo a Passo

### Passo 1: Descobrir o IP do Contêiner no Proxmox
Caso ainda não tenha o IP do contêiner anotado:

1. Acesse o painel web do seu **Proxmox VE**.
2. Na barra lateral esquerda, localize e clique no seu contêiner do Ollama (LXC).
3. Na aba **Summary** (Resumo), localize o campo **Network** (Rede) para identificar o endereço IPv4 ativo.

---

### Passo 2: Estabelecer a Conexão SSH
Abra o aplicativo de terminal nativo do seu laptop:

- **macOS / Linux:** Abra o aplicativo "Terminal".
- **Windows:** Abra o "PowerShell" ou o "Prompt de Comando".

Digite o seguinte comando, substituindo pelo IP real do seu contêiner:

```bash
ssh root@IP_DO_SEU_CONTAINER
```

> [!warning] Se ocorrer erro de "host key" (host key changed)
> Limpe a chave antiga do host e tente novamente:
> ```bash
> ssh-keygen -R IP_DE_ACESSO
> ```

> **Nota para o primeiro acesso:** O terminal exibirá uma mensagem de aviso de segurança perguntando se deseja continuar a conexão (`Are you sure you want to continue connecting?`). Digite **`yes`** e pressione **`Enter`**. Em seguida, insira a senha do usuário `root` do contêiner.

---

### Passo 3: Executar a IA
Agora que o prompt do terminal mudou para indicar que você está controlando o contêiner LXC (por exemplo, `root@ollama:~#`), digite o comando para iniciar a IA:

```bash
ollama run gemma
```

O Ollama carregará instantaneamente as camadas do modelo e abrirá a interface de conversa interativa:

```text
>>> Send a message (/? for help)
```

> [!tip] Pronto! Você já pode digitar suas perguntas e interagir com a IA diretamente no seu terminal local.

---

## 🛠️ Dicas de Otimização e Produtividade

### 1. Comandos Úteis do Prompt do Ollama
Dentro do prompt de conversa do Ollama (`>>>`), você pode utilizar alguns comandos rápidos:

- `/bye` ou `/exit`: Encerra o chat e volta ao terminal do contêiner LXC.
- `/?` ou `/help`: Lista todas as opções e comandos adicionais do Ollama.

### 2. Criando um Atalho Rápido (Alias) no seu Laptop
Se você não quer ficar digitando o comando SSH toda vez, pode criar um comando personalizado no seu laptop para conectar e iniciar a IA instantaneamente.

#### No macOS ou Linux:
1. Abra o arquivo de perfil do seu terminal (geralmente `~/.bashrc` ou `~/.zshrc`):
   ```bash
   nano ~/.zshrc
   ```
2. Adicione a seguinte linha no final do arquivo:
   ```bash
   alias gemma="ssh -t root@IP_DO_SEU_CONTAINER 'ollama run gemma'"
   ```
3. Salve o arquivo e atualize o terminal:
   ```bash
   source ~/.zshrc
   ```
4. Agora, basta digitar **`gemma`** no terminal do seu laptop para conectar e abrir o chat automaticamente!

#### No Windows (PowerShell):
1. Abra o seu perfil do PowerShell:
   ```powershell
   notepad $PROFILE
   ```
2. Adicione a seguinte função ao arquivo:
   ```powershell
   function gemma { ssh -t root@IP_DO_SEU_CONTAINER "ollama run gemma" }
   ```
3. Salve o arquivo, feche e abra novamente o PowerShell. Agora você pode chamar a IA apenas digitando **`gemma`**.

---

> [!note] Guia gerado com base nas configurações recomendadas de virtualização Proxmox e aceleração local.

## 🔗 Notas Relacionadas
- 🌐 [Proxmox: SyncThing](./ollama--guia-instalacao-syncthing.md) — Sincronizar o cofre com o container LXC
- 🌐 [Proxmox: Repositório Gratuito](./01-como-ativar-repositorio-gratuito-gui.md) — Configurar repositórios
- 🪟 [Windows: SSH Remoto](../windows/ssh-remoto-configuracao.md) — Configuração remota via SSH
- 🎓 [T.I. — Mapa de Conteúdo](../README.md) — Índice geral
