---
title: "Proxmox VE: Ativar Repositório Gratuito (No-Subscription) pela Interface Web"
date_created: 2026-08-22
tags:
  - proxmox/repositorios
  - proxmox/atualizacao
---

# 🌐 Proxmox VE: Ativar o Repositório Gratuito (No-Subscription) pela Interface Web

> [!info] Este guia ensina como configurar os repositórios do Proxmox VE diretamente pela interface gráfica (Web UI), eliminando os erros de atualização (como o erro `401 Unauthorized`) de forma segura e rápida.

---

## 1. Por que o Proxmox apresenta erro após a instalação?

Por padrão, uma nova instalação do Proxmox VE vem configurada para buscar atualizações e novos pacotes a partir de um repositório de nível corporativo conhecido como **pve-enterprise**.

No entanto, o acesso a esse repositório exige uma **chave de assinatura comercial ativa**. Se o seu servidor não possui uma licença paga inserida:

- O comando `apt update` falhará ao tentar obter pacotes desse repositório seguro (retornando erros de autenticação HTTP `401 Unauthorized`).
- O sistema se recusará a atualizar os pacotes devido à falta de assinaturas válidas.
- Um alerta visual sobre a falta de assinatura será exibido no seu painel.

> [!tip] Para ambientes de laboratório, testes ou uso não comercial
> A solução oficial recomendada pela Proxmox é desativar as fontes corporativas e habilitar o repositório comunitário gratuito **pve-no-subscription**.

---

## 2. Passo a Passo para Ativação via Interface Gráfica (GUI)

Desde as versões recentes do Proxmox VE (PVE 7, 8 e as mais novas do PVE 9), a administração de repositórios pode ser realizada inteiramente no navegador de forma automatizada, o que evita a necessidade de editar arquivos de configuração complexos no terminal.

### Passo 1: Acesse a Interface Web
Abra o navegador e acesse o endereço IP do seu servidor Proxmox na porta `8006` (usando protocolo HTTPS):

```
https://ip-do-seu-servidor:8006
```

### Passo 2: Selecione o Servidor (Nó)
No painel de navegação à esquerda (menu em árvore), clique sobre o nome do seu **Node** (o servidor físico principal).

### Passo 3: Acesse o Menu de Repositórios
No painel central, navegue pela aba de opções:

1. Clique em **Updates** (Atualizações).
2. Clique no submenu **Repositories** (Repositórios).

> [!note] Você verá aqui uma listagem completa do status de todas as fontes de pacotes configuradas no sistema, incluindo os repositórios Debian e Proxmox.

### Passo 4: Desative os Repositórios Pagos (Enterprise)
1. Selecione a linha correspondente ao repositório do Proxmox corporativo (**pve-enterprise**).
2. Clique no botão **Disable** (Desativar) localizado na barra de ferramentas superior.
3. Se o seu nó tiver o sistema de arquivos de armazenamento Ceph configurado ou pré-instalado por padrão, selecione também a linha do repositório **ceph-enterprise** (ex: `ceph-tentacle enterprise` ou similar) e clique em **Disable**.

> **Nota:** Desativar o repositório através desta tela apenas adiciona um parâmetro `Enabled: false` nos arquivos de configuração do sistema (como os arquivos de formato deb822 `.sources` no Proxmox VE 9), preservando as linhas originais caso você compre uma licença no futuro.

### Passo 5: Adicione o Repositório Gratuito (No-Subscription)
1. Clique no botão **Add** (Adicionar) na parte superior da tela.
2. No painel que se abre, você verá um aviso de que esse repositório comunitário não é recomendado para ambientes de produção crítica. Clique no menu suspenso **Repository**.
3. Selecione a opção **No-Subscription** (Sem Assinatura).
4. Clique em **Add** (Adicionar).

> [!info] O painel do Proxmox detectará automaticamente qual a versão do Debian base do seu sistema (ex: Debian 12 Bookworm para PVE 8 ou Debian 13 Trixie para PVE 9) e adicionará a URL de download perfeitamente configurada, sem risco de erros de digitação.

### Passo 6: Configure o Ceph Gratuito (Se aplicável)
Se você estiver utilizando ou pretender gerenciar armazenamento distribuído com o Ceph no seu servidor:

1. Clique novamente em **Add** (Adicionar).
2. Selecione a opção **Ceph No-Subscription**.
3. Clique em **Add**.

---

## 3. Verificando e Aplicando as Atualizações

Após alterar as fontes, você precisa atualizar as listas locais do gerenciador de pacotes (`apt`) para sincronizar com os novos repositórios comunitários.

1. Ainda na aba do seu Nó, vá para a seção **Updates** (Atualizações) na barra lateral esquerda.
2. Clique no botão **Refresh** (Atualizar/Recarregar).
3. Uma janela de terminal em segundo plano será aberta e executará o comando equivalente ao `apt update`. Aguarde até que a mensagem "TASK OK" seja exibida sem erros de autenticação HTTP `401`.
4. Feche a janela de log do Refresh. Agora você verá a lista de todos os pacotes do sistema que possuem atualizações disponíveis.
5. Clique no botão **Upgrade** (Atualizar) na parte superior para iniciar o processo de atualização de segurança e de recursos do sistema através da interface gráfica.

---

> [!note] Guia elaborado com base nas melhores práticas da documentação oficial do Proxmox VE.

## 🔗 Notas Relacionadas
- 🌐 [Proxmox: Ollama via SSH](./ollama--guia-acesso-ssh.md) — Acessar o Ollama no Proxmox via terminal
- 🌐 [Proxmox: SyncThing](./ollama--guia-instalacao-syncthing.md) — Sincronizar o cofre com o container LXC
- 🐧 [Linux: Atualizar Pacotes](../linux/1-atualizar-pacotes.md) — Comandos apt/dnf
- 🎓 [T.I. — Mapa de Conteúdo](../README.md) — Índice geral
