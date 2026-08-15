# Guia de Instalação — Servidor Proxy Squid com CMEasy

**Autor:** Cleison Alves José Máquina  
**Curso:** Técnico de Cibersegurança  
**Ano:** 2026

---

## Índice

1. [Introdução](#introdução)
2. [O que é o Squid](#o-que-é-o-squid)
3. [O que é o CMEasy](#o-que-é-o-cmeasy)
4. [Requisitos](#requisitos)
5. [Módulo SSH — Configuração Inicial](#módulo-ssh--configuração-inicial)
   - [Visão Geral](#visão-geral-ssh)
   - [Segurança SSH (Hardening)](#segurança-ssh-hardening)
   - [Gestão do Serviço SSH](#gestão-do-serviço-ssh)
6. [Módulo Squid — Visão Geral](#módulo-squid--visão-geral)
7. [Instalação do Squid](#instalação-do-squid)
8. [Configuração do Squid](#configuração-do-squid)
   - [Configuração Base](#configuração-base)
   - [Bloqueio de Sites](#bloqueio-de-sites)
   - [Autenticação de Utilizadores](#autenticação-de-utilizadores)
9. [Módulo Wazuh SIEM — Visão Geral](#módulo-wazuh-siem--visão-geral)
10. [Integração Squid + Wazuh](#integração-squid--wazuh)
11. [Comandos de Operação e Logs](#comandos-de-operação-e-logs)
12. [Testes de Validação](#testes-de-validação)
13. [Considerações Finais](#considerações-finais)
14. [Referências](#referências)

---

## Introdução

Este guia documenta a implementação de um servidor **Forward Proxy** baseado na solução open-source **Squid**, num ambiente virtualizado com **Ubuntu Server 22.04 LTS**.

O objetivo é mitigar vetores de ataque baseados na Web através do controlo, filtragem granular e auditoria do tráfego de rede. A implementação inclui:

- Políticas de controlo de acessos (ACLs)
- Bloqueio de domínios não autorizados
- Autenticação de utilizadores
- Monitorização centralizada de logs via Wazuh SIEM

> A instalação e configuração foram automatizadas com a ferramenta **CMEasy**, reduzindo o tempo de implementação em cerca de **60%**.

---

## O que é o Squid

O **Squid** é um proxy de cache para a Web que suporta HTTP, HTTPS e FTP. Atua como intermediário entre os utilizadores e a Internet, permitindo:

- **Cache de conteúdos** — reduz consumo de largura de banda
- **Controlo de acesso (ACLs)** — bloqueia ou permite sites e conteúdos
- **Autenticação** — suporte a autenticação básica (htpasswd), LDAP e Active Directory
- **Monitorização** — registo e auditoria de tráfego web
- **Proteção da rede interna** — oculta os IPs internos, expondo apenas o IP do proxy

É compatível com Linux e Windows, distribuído sob licença **GNU GPL**.  
Site oficial: https://www.squid-cache.org/

---

## O que é o CMEasy

O **CMEasy** é uma ferramenta de automatização desenvolvida internamente que simplifica a instalação, configuração e gestão de serviços de segurança em sistemas Linux.

No contexto deste projeto, o CMEasy disponibiliza dois módulos principais:

| Módulo | Função |
|---|---|
| `menus_08_proxySquid` | Gestão completa do Squid Proxy |
| `menus_09_wazuh` | Gestão do Wazuh SIEM e integrações |

---

## Requisitos

| Componente | Especificação |
|---|---|
| Sistema Operativo | Ubuntu Server 22.04 LTS |
| CPU | 1 núcleo (mínimo) |
| RAM | 512 MB (mínimo) / 2 GB (recomendado) |
| Armazenamento | 25 GB em disco |
| Rede | Interface com IP fixo |
| Porta do proxy | 3128 (TCP) |

---

## Módulo SSH — Configuração Inicial

Antes de instalar o Squid, o acesso remoto ao servidor deve ser configurado e protegido. O CMEasy disponibiliza o módulo SSH (`menus_01_ssh`) para este efeito.

### Visão Geral SSH

| Protocolo | Secure Shell (SSH) |
|---|---|
| Porta padrão | 22 |
| Função | Acesso remoto seguro e administração de sistemas |
| Sistemas suportados | Debian / Ubuntu / RedHat / CentOS / Rocky Linux |

**Capacidades do módulo:**

- Acesso remoto seguro (CLI)
- Execução remota de comandos
- Transferência de ficheiros (SCP / SFTP)
- Tunelamento seguro (port forwarding)
- Autenticação por chave pública (RSA / ED25519)

**Opções do menu CMEasy SSH:**

| Opção | Descrição |
|---|---|
| `[1]` Instalar | Instala e configura o OpenSSH |
| `[2]` Desinstalar | Remove o serviço SSH |
| `[3]` Comandos | Referência de comandos úteis |
| `[4]` Configurações | Ficheiro de configuração (`sshd_config`) |
| `[5]` Ver logs do sistema | Logs de autenticação SSH |
| `[6]` Segurança | Medidas de hardening SSH |
| `[7]` Verificar erros nas configurações | Valida o `sshd_config` |
| `[8]` Ver logs do CMEasy | Registos de auditoria do CMEasy |

---

### Segurança SSH (Hardening)

No menu SSH, selecionar `[6] Segurança`. O CMEasy apresenta as seguintes medidas de hardening a aplicar no ficheiro `/etc/ssh/sshd_config`:

#### 1. Desativar login de root

```
PermitRootLogin no
```

#### 2. Desativar autenticação por password e ativar chave pública

```
PasswordAuthentication no
```

Gerar o par de chaves no cliente:

```bash
ssh-keygen -t ed25519
```

Copiar a chave pública para o servidor:

```bash
ssh-copy-id UTILIZADOR@IP_SERVIDOR
```

#### 3. Desativar passwords vazias

```
PermitEmptyPasswords no
```

#### 4. Alterar a porta padrão

```
Port 2222
```

> Escolher uma porta alternativa à 22 para mitigar ataques de força bruta e port scanning. Após alterar, configurar o firewall UFW para permitir a nova porta.

```bash
sudo ufw allow 2222/tcp
sudo ufw reload
```

**Após aplicar as alterações, reiniciar o serviço:**

```bash
sudo systemctl restart ssh
sudo systemctl status ssh
```

**Verificar erros de configuração** (`[7]` no menu CMEasy):

```bash
sudo sshd -t
```

**Resultado esperado:** sem erros de configuração.

---

### Gestão do Serviço SSH

O CMEasy disponibiliza um submenu de gestão de serviços (`[3] Comandos`) aplicável ao SSH e a outros serviços do sistema:

| Opção | Comando equivalente |
|---|---|
| `[1]` Iniciar serviço | `sudo systemctl start ssh` |
| `[2]` Ativar serviço | `sudo systemctl enable ssh` |
| `[3]` Desativar serviço | `sudo systemctl disable ssh` |
| `[4]` Estado do serviço | `sudo systemctl status ssh` |
| `[5]` Parar serviço | `sudo systemctl stop ssh` |
| `[6]` Reiniciar serviço | `sudo systemctl restart ssh` |
| `[7]` Versão do serviço | `ssh -V` |

---

## Módulo Squid — Visão Geral

O módulo CMEasy para o Squid (`menus_08_proxySquid`) disponibiliza as seguintes opções:

| Opção | Descrição |
|---|---|
| `[1]` Instalar | Instalação e configuração automatizada do Squid |
| `[2]` Desinstalar | Remoção do serviço |
| `[3]` Comandos | Referência de comandos úteis |
| `[4]` Configurações | Submenu de configuração (ver abaixo) |
| `[5]` Logs do sistema | Logs do serviço Squid (`/var/log/squid/`) |
| `[6]` Logs Cache | Logs do cache do proxy |
| `[7]` Logs CMEasy | Registos de auditoria do CMEasy |
| `[8]` Logs de acesso | `access.log` em tempo real |
| `[9]` Listar sites banidos | Lista de domínios bloqueados |
| `[10]` Listar Utilizadores | Utilizadores com acesso autorizado |
| `[11]` Verificar configuração | Validação do `squid.conf` |

### Submenu de Configuração (`[4]`)

| Opção | Descrição |
|---|---|
| `[1]` Configuração Base | Aplica configuração padrão do `squid.conf` |
| `[2]` Arquivo de configuração | Abre o ficheiro de configuração |
| `[3]` Banir um site | Adiciona domínio à lista de bloqueio |
| `[4]` Criar utilizador | Cria utilizador com acesso ao proxy |
| `[5]` Remover utilizador | Remove utilizador existente |
| `[6]` Criar utilizador admin | Cria utilizador com privilégios elevados |

---

## Instalação do Squid

### Passo 1 — Executar instalação via CMEasy

No menu principal do Squid, selecionar a opção `[1] Instalar`.

O CMEasy executa automaticamente os seguintes passos:

1. Verifica privilégios administrativos (root)
2. Atualiza o sistema (`apt update && apt upgrade`)
3. Instala o Squid e ferramentas auxiliares (`apache2-utils`)
4. Cria um **backup automático** da configuração original do Squid
5. Gera o ficheiro de credenciais `htpasswd` com permissões restritas:
   - Owner: `proxy`
   - Modo: `640`
6. Configura ACLs base (bloqueio de sites, restrição por IP, portas seguras)
7. Aplica política de **"deny by default"**
8. Deteta automaticamente a sub-rede local e configura o UFW para permitir apenas tráfego interno na porta `3128`
9. Ativa e inicia o serviço via `systemd` (persistência após reboot)
10. Gera registo de instalação com rotação de ficheiros para auditoria

**Instalação manual equivalente:**

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install squid apache2-utils -y
```

### Passo 2 — Verificar se o serviço está ativo

```bash
sudo systemctl status squid
```

**Resultado esperado:** `Active: active (running)`

### Passo 3 — Validar a configuração

No menu CMEasy, selecionar `[11] Verificar configuração`, ou manualmente:

```bash
sudo squid -k parse
```

**Resultado esperado:** sem erros de configuração.

---

## Configuração do Squid

### Configuração Base

No submenu de Configurações, selecionar `[1] Configuração Base`.  
O CMEasy aplica automaticamente as seguintes diretivas no `squid.conf`:

```squid
# Porta do proxy HTTP
http_port 3128

# Bloqueio de IP específico
acl ip_bloqueado src 192.168.1.100
http_access deny ip_bloqueado

# Lista de sites bloqueados
acl bad_url dstdomain "/etc/squid/bad-sites.acl"
http_access deny bad_url

# Portas permitidas
acl SSL_ports port 443
acl Safe_ports port 80 443

# Bloqueia portas não seguras
http_access deny !Safe_ports

# Método CONNECT (HTTPS via proxy)
acl CONNECT method CONNECT
http_access deny CONNECT !SSL_ports

# Autenticação básica
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic realm Proxy Autenticado
auth_param basic credentialsttl 2 hours

acl autenticados proxy_auth REQUIRED
http_access allow autenticados

# Rede local (opcional — descomentar apenas se NÃO usar autenticação obrigatória)
# acl localnet src 192.168.1.0/24
# http_access allow localnet

# Rejeição final de todo o tráfego não autorizado
http_access deny all
```

> **Notas de segurança:**
> 1. Evitar duplicação de regras no `squid.conf`
> 2. Confirmar a ordem das regras `http_access` (a primeira regra que corresponder é aplicada)
> 3. Reiniciar o serviço após qualquer alteração
> 4. Validar ficheiros ACL antes de aplicar
> 5. Proteger o ficheiro de passwords (`/etc/squid/passwd`)

Após editar o `squid.conf`, aplicar as alterações:

```bash
sudo systemctl restart squid
sudo systemctl status squid
```

---

### Bloqueio de Sites

No submenu de Configurações, selecionar `[3] Banir um site`.

O CMEasy solicita o domínio a bloquear e adiciona-o automaticamente à lista de ACL.

**Adicionar domínio manualmente:**

```bash
echo ".facebook.com" | sudo tee -a /etc/squid/bad-sites.acl
sudo systemctl reload squid
```

**Verificar sites bloqueados:**

No menu CMEasy, selecionar `[9] Listar sites banidos`, ou manualmente:

```bash
cat /etc/squid/bad-sites.acl
```

**Verificação do bloqueio:**

A partir de um cliente configurado para usar o proxy, tentar aceder a um domínio bloqueado. O acesso deve ser negado pelo Squid.

Confirmar nos logs:

```bash
sudo tail -f /var/log/squid/access.log
```

---

### Autenticação de Utilizadores

No submenu de Configurações, selecionar `[4] Criar utilizador`.

**Criar utilizador manualmente:**

```bash
sudo htpasswd /etc/squid/passwd nome_utilizador
```

**Criar utilizador admin:**

No menu CMEasy, selecionar `[6] Criar utilizador admin`.

**Remover utilizador:**

```bash
sudo htpasswd -D /etc/squid/passwd nome_utilizador
```

**Listar utilizadores:**

No menu CMEasy, selecionar `[10] Listar Utilizadores`, ou manualmente:

```bash
cat /etc/squid/passwd
```

**Verificação:**

Ao tentar aceder à Internet através do proxy, o navegador deve apresentar uma janela de autenticação solicitando utilizador e palavra-passe.

---

## Módulo Wazuh SIEM — Visão Geral

O módulo CMEasy para o Wazuh (`menus_09_wazuh`) disponibiliza as seguintes opções:

| Opção | Descrição |
|---|---|
| `[1]` Instalar | Orientação para instalação oficial do Wazuh |
| `[2]` Desinstalar | Remoção do serviço |
| `[3]` Comandos | Referência de comandos úteis |
| `[4]` Configuração | Configuração do agente e integrações |
| `[5]` Logs do sistema | Logs do Wazuh |
| `[6]` Logs do CMEasy | Registos de auditoria do CMEasy |
| `[7]` Integração de ferramentas | Submenu de integração (ex.: Squid Proxy) |
| `[8]` Checar Configurações | Validação da configuração atual |

> **Nota:** O CMEasy recomenda a utilização da documentação oficial para instalação do Wazuh Manager, garantindo compatibilidade e atualizações contínuas.
>
> Links oficiais:
> - https://documentation.wazuh.com/current/quickstart.html
> - https://documentation.wazuh.com/current/installation-guide/index.html

---

## Integração Squid + Wazuh

No módulo Wazuh, selecionar `[7] Integração de ferramentas` → `[1] Proxy Squid`.

Este módulo configura a leitura segura dos logs do Squid pelo agente Wazuh, permitindo monitorização centralizada de tráfego, cache e acessos HTTP.

### Logs monitorizados

| Ficheiro | Conteúdo |
|---|---|
| `/var/log/squid/access.log` | Tráfego de utilizadores |
| `/var/log/squid/cache.log` | Operações de cache do proxy |

### Passo 1 — Configurar permissões de leitura

```bash
sudo usermod -a -G adm wazuh
sudo chgrp adm /var/log/squid/access.log
sudo chmod 640 /var/log/squid/access.log
```

### Passo 2 — Configurar o agente Wazuh

Editar o ficheiro de configuração do agente:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Adicionar as seguintes entradas `<localfile>`:

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/squid/access.log</location>
</localfile>

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/squid/cache.log</location>
</localfile>
```

### Passo 3 — Reiniciar o agente Wazuh

```bash
sudo systemctl restart wazuh-agent
sudo systemctl status wazuh-agent
```

### Verificação

Simular uma tentativa de acesso com credenciais inválidas e verificar no painel do Wazuh:

1. Alerta de autenticação falhada gerado
2. Alerta por email enviado com notificação em tempo real

> **Notas de segurança:**
> 1. Evitar duplicação de regras no `squid.conf`
> 2. Reiniciar o serviço após qualquer alteração
> 3. Validar ACLs antes de aplicar
> 4. Proteger o ficheiro de autenticação (`/etc/squid/passwd`)

---

## Comandos de Operação e Logs

### Gestão do serviço Squid

```bash
# Iniciar
sudo systemctl start squid

# Parar
sudo systemctl stop squid

# Reiniciar
sudo systemctl restart squid

# Recarregar configuração (sem interrupção)
sudo systemctl reload squid

# Verificar estado
sudo systemctl status squid

# Validar configuração
sudo squid -k parse
```

### Consulta de logs

```bash
# Logs de acesso em tempo real
sudo tail -f /var/log/squid/access.log

# Logs de cache
sudo tail -f /var/log/squid/cache.log

# Logs do CMEasy
# Disponível na opção [7] Logs CMEasy do menu principal
```

---

## Testes de Validação

| Teste | Procedimento | Resultado esperado |
|---|---|---|
| Serviço ativo | `systemctl status squid` | `active (running)` |
| Configuração válida | `squid -k parse` | Sem erros |
| Bloqueio de sites | Aceder a domínio bloqueado pelo cliente | Acesso negado pelo proxy |
| Autenticação — sem credenciais | Aceder à Internet sem fazer login | Janela de login apresentada |
| Autenticação — credenciais válidas | Login com utilizador criado | Acesso concedido |
| Autenticação — credenciais inválidas | Login com dados errados | Acesso negado |
| Logs no Wazuh | Simular acesso não autorizado | Alerta gerado no painel Wazuh |
| Alerta por email | Acesso não autorizado simulado | Email de notificação recebido |

---

## Considerações Finais

A implementação do servidor proxy Squid com o CMEasy demonstra uma solução robusta para gestão, filtragem e auditoria de tráfego de rede. A solução é versátil e pode ser aplicada em:

- **Ambientes domésticos / laboratórios** — controlo parental e segurança básica
- **Infraestruturas empresariais** — controlo de acesso corporativo

Para ambientes empresariais, recomenda-se a aplicação de controlos adicionais de hardening para garantir maior resiliência.

A integração com o **Wazuh SIEM** reforça a visibilidade sobre o perímetro da rede, permitindo:

- Centralização de dados de segurança
- Correlação de eventos em tempo real
- Geração automatizada de alertas
- Resposta proativa a incidentes de cibersegurança

As configurações implementadas estão alinhadas com as boas práticas da **Diretiva NIS2** e da norma **ISO/IEC 27001**.

---

## Referências

- Squid Cache — [squid-cache.org](https://www.squid-cache.org/)
- Wazuh Documentation — [documentation.wazuh.com](https://documentation.wazuh.com/current/quickstart.html)
- Fortinet — [Proxy Server](https://www.fortinet.com/br/resources/cyberglossary/proxy-server)
- Microsoft — [Usar um servidor proxy no Windows](https://support.microsoft.com/pt-br/windows/usar-um-servidor-proxy-no-windows-03096c53-0554-4ffe-b6ab-8b1deee8dae1)
- Wikipedia — [Proxy](https://pt.wikipedia.org/wiki/Proxy)
- CMEasy — [github.com/cleisonmq/CMEASY-V1.0-CLEISON-MAQUINA-2026](https://github.com/cleisonmq/CMEASY-V1.0-CLEISON-MAQUINA-2026)

---

*Cleison Alves José Máquina — Técnico de Cibersegurança — 2026*
