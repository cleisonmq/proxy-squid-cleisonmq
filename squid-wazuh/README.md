# Monitorização do Squid com Wazuh

Documentação para acompanhar alterações nos ficheiros de configuração do Squid e analisar os eventos produzidos pelo proxy.

## Conteúdo

| Pasta | Objetivo |
| --- | --- |
| [Integridade de ficheiros](01_integridade_de_ficheiros/README.md) | Configurar a vigilância de `/etc/squid/`, testar alterações e consultar os eventos no módulo FIM |
| [Monitorização de logs](02_monitoramento_de_logs/README.md) | Recolher logs, consultar exemplos de decodificadores e regras e validar eventos de Threat Hunting |

As duas áreas incluem imagens de referência do ambiente.

## Como utilizar

1. Disponha de um servidor Wazuh e de um agente no servidor Squid.
2. Consulte o guia de integridade para acompanhar os ficheiros de configuração.
3. Consulte o guia de logs para preparar a recolha e analisar os eventos.
4. Execute os passos de validação apresentados em cada guia.

O [exemplo de configuração do Squid](../arquivo-de-configuracao/proxy-squid-config.txt) contém o formato de log utilizado no projeto. Os exemplos XML do Wazuh estão nos documentos Markdown das respetivas pastas.

[Voltar à apresentação do projeto](../README.md)
