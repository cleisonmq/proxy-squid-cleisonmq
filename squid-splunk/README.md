# Integração do Squid com Splunk

Esta pasta reúne a documentação do **Splunk Universal Forwarder** e um dashboard para analisar os logs de acesso do Squid.

## Conteúdo

| Pasta | Conteúdo |
| --- | --- |
| [Agente Splunk](agente-slpunk/README.md) | Instalação do Universal Forwarder em Linux, ligação ao servidor e verificações |
| [Dashboard](Dashboard/README.md) | Apresentação dos painéis e definição em Simple XML |

## Sequência de utilização

1. Consulte o guia de instalação do Universal Forwarder na máquina cliente.
2. Siga o guia de configuração da ligação ao servidor Splunk.
3. Prepare a recolha de `/var/log/squid/access.log` e confirme que os eventos estão disponíveis no Splunk.
4. Consulte o README do dashboard e utilize a definição XML para visualizar os dados.

Os guias do agente cobrem a instalação, o destino de envio e a gestão do Forwarder. A configuração da entrada de monitorização do `access.log` não está incluída nesses documentos.

O [exemplo de configuração do Squid](../arquivo-de-configuracao/proxy-squid-config.txt) define o formato de log com os campos usados nas consultas do dashboard.

[Voltar à apresentação do projeto](../README.md)
