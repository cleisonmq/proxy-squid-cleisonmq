# Agente Splunk — Universal Forwarder

Guias para instalar o **Splunk Universal Forwarder** num cliente Linux e configurar a ligação ao servidor Splunk Enterprise.

## Documentação

| Guia | Conteúdo |
| --- | --- |
| [Instalação](instalacao-do-agente-splunk.md) | Obtenção do pacote, preparação do utilizador e diretório, instalação por pacote Debian e primeiro arranque |
| [Configuração](configuracao-do-agente-splunk.md) | Porta de receção, destino de envio, servidor de gestão, reinício e verificações |

## Valores usados nos exemplos

| Valor | Utilização no guia |
| --- | --- |
| `/opt/splunkforwarder` | Diretório de instalação |
| `10.0.1.75` | Endereço de exemplo do servidor Splunk |
| `9997/TCP` | Receção de eventos |
| `8089/TCP` | Gestão do Forwarder |

Adapte os endereços, caminhos e pacote ao ambiente. O guia de configuração inclui verificações com `list forward-server`, consulta de `deploymentclient.conf` e confirmação na interface do Splunk.

## Ligação ao Squid

Estes documentos apresentam a instalação e a comunicação do agente. A entrada de monitorização de `/var/log/squid/access.log` ainda precisa de ser configurada para alimentar o [dashboard do projeto](../Dashboard/README.md).

[Voltar à integração com Splunk](../README.md)
