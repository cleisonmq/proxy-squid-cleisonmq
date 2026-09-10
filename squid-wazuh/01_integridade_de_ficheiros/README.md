# Integridade dos ficheiros do Squid

Esta pasta documenta a monitorização de alterações na configuração do Squid através do **File Integrity Monitoring (FIM)** do Wazuh.

## Conteúdo

- [Guia de configuração e validação](File_Integrity_Monitoring_squid.md): exemplo de `syscheck` para o diretório `/etc/squid/` ou um ficheiro específico.
- [Imagens de referência](imagens/README.md): captura utilizada na documentação.

## O que o guia apresenta

O exemplo utiliza as opções `check_all`, `report_changes`, `whodata` e `realtime`. O teste consiste em alterar um ficheiro monitorizado e consultar os eventos do agente Squid em **File Integrity Monitoring → Events**.

O caminho `/etc/squid/nome_do_arquivo.extensao` é um exemplo a substituir pelo ficheiro pretendido.

[Voltar à documentação Wazuh](../README.md)
