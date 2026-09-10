# Proxy Squid — Controlo de acesso e monitorização

Projeto de implementação de um **Forward Proxy com Squid**, com instalação através do **CMEasy**, configuração de controlo de acesso e integração com **Wazuh** e **Splunk** para auditoria e monitorização.

Desenvolvido por **Cleison Alves José Máquina**, no âmbito do curso de **Técnico de Cibersegurança — 2026**, o repositório reúne guias, exemplos de configuração e imagens de validação do ambiente.

## Sobre o projeto

O Squid atua como intermediário entre os clientes da rede interna e a Internet. A configuração disponibilizada combina autenticação de utilizadores, regras de acesso, bloqueio de domínios e registo de atividade, permitindo estudar a administração de um proxy e a análise dos seus eventos de segurança.

O projeto está organizado em quatro áreas:

- **CMEasy:** guia de instalação e ativação do serviço Squid.
- **Configuração do Squid:** exemplo comentado com ACLs, autenticação e medidas de reforço de segurança (hardening).
- **Wazuh:** monitorização da integridade dos ficheiros e análise dos logs do proxy.
- **Splunk:** dashboard para consultar acessos, bloqueios, metadados e volume de pedidos.

## Configuração disponibilizada

O [ficheiro de configuração do Squid](arquivo-de-configuracao/proxy-squid-config.txt) inclui:

- Escuta na porta `3128` e identificação personalizada do proxy.
- Restrição de portas de destino e de métodos HTTP, incluindo regras para túneis `CONNECT`.
- Bloqueio de domínios e padrões de URL através de listas externas.
- Limites de ligações por IP, tamanhos de pedidos e respostas e tempos de espera.
- Remoção de cabeçalhos selecionados para reduzir a exposição de informação.
- Autenticação Basic com o helper NCSA e acesso da rede local condicionado à autenticação.
- Bloqueio final dos pedidos que não correspondam às permissões definidas.
- Logs de acesso com utilizador, User-Agent e campos de cabeçalhos de resposta para análise.

## Documentação

| Área | Conteúdo |
| --- | --- |
| Instalação | [Guia de instalação do Squid com CMEasy](squid-cmeasy/proxy-squid-cmeasy.md) |
| Configuração | [Exemplo comentado de squid.conf](arquivo-de-configuracao/proxy-squid-config.txt) |
| Wazuh — integridade | [Monitorização de alterações em /etc/squid/](squid-wazuh/01_integridade_de_ficheiros/File_Integrity_Monitoring_squid.md) |
| Wazuh — logs | [Recolha de logs, decodificadores, regras e Threat Hunting](squid-wazuh/02_monitoramento_de_logs/Threat_Hunting_squid.md) |
| Splunk | [Apresentação do dashboard](squid-splunk/Dashboard/README.md) e [definição em XML](squid-splunk/Dashboard/conf.xml) |

## Monitorização e análise

### Wazuh

A documentação apresenta duas vertentes de monitorização:

- **File Integrity Monitoring (FIM):** configuração de vigilância sobre `/etc/squid/`, com passos de teste e consulta dos eventos no dashboard.
- **Threat Hunting:** recolha dos logs do Squid, exemplos de decodificadores e regras para analisar acessos, transferências e identificação de navegadores, com validação através de `wazuh-logtest` e do dashboard.

### Splunk

O dashboard em **Simple XML** consulta eventos provenientes de `/var/log/squid/access.log` e apresenta painéis para análise de:

- Métodos HTTP utilizados nos pedidos.
- Destinos, portas e resultados das ligações em túnel.
- Pedidos de reconhecimento e cabeçalhos de resposta registados nos logs.
- Acessos negados e classificação por padrões de URL.
- Volume de pedidos por IP de origem ao longo do tempo.

## Como explorar o projeto

1. Consulte o guia de instalação com CMEasy para instalar e ativar o Squid.
2. Analise o exemplo de configuração e adapte a rede local, os caminhos e os limites ao seu ambiente.
3. Prepare os ficheiros externos referidos na configuração: `/etc/squid/bad-domains.acl`, `/etc/squid/bad-patterns.acl` e `/etc/squid/passwd`. Estes ficheiros não estão incluídos no repositório.
4. Siga os guias do Wazuh para configurar a monitorização de integridade e a recolha e análise dos logs.
5. Consulte a documentação do dashboard Splunk e utilize o XML num ambiente com os logs do Squid já disponíveis para pesquisa.

Os materiais são exemplos de configuração e validação. A instalação das plataformas, a preparação das credenciais e listas de bloqueio e o envio dos logs para o Splunk dependem do ambiente utilizado.

## Autor

**Cleison Alves José Máquina**
Técnico de Cibersegurança · 2026
