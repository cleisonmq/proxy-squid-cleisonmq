# Configuração do Squid

Exemplo comentado de configuração do proxy Squid, com regras de acesso, autenticação, limites de recursos e registo de atividade.

## Ficheiro disponível

[proxy-squid-config.txt](proxy-squid-config.txt) reúne as diretivas destinadas ao ficheiro `squid.conf`, organizadas por função:

- Escuta na porta `3128` e identificação do proxy.
- Portas de destino permitidas, métodos HTTP e regras para `CONNECT`.
- Exemplo comentado de bloqueio por IP e listas externas de domínios e padrões de URL.
- Limites de ligações, tamanhos de pedidos e respostas e tempos de espera.
- Tratamento de cabeçalhos de pedidos e respostas.
- Autenticação Basic com NCSA, acesso da rede local e bloqueio final.
- Formato de logs, rotação e conta de execução do serviço.

## Preparação do ambiente

O exemplo define a rede local como `10.0.1.0/24`. Adapte-a ao ambiente e confirme os caminhos do helper de autenticação e dos ficheiros utilizados.

A configuração referencia três ficheiros que não estão incluídos nesta pasta:

| Ficheiro no servidor | Finalidade |
| --- | --- |
| `/etc/squid/bad-domains.acl` | Lista de domínios bloqueados |
| `/etc/squid/bad-patterns.acl` | Padrões de URL bloqueados |
| `/etc/squid/passwd` | Credenciais utilizadas pelo helper NCSA |

O formato `secure_format` regista os acessos em `/var/log/squid/access.log`, incluindo User-Agent e campos de cabeçalhos de resposta usados na monitorização.

## Documentação relacionada

- [Instalação com CMEasy](../squid-cmeasy/README.md)
- [Monitorização com Wazuh](../squid-wazuh/README.md)
- [Integração com Splunk](../squid-splunk/README.md)
- [Apresentação do projeto](../README.md)
