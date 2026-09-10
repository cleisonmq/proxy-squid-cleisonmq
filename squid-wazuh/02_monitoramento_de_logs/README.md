# Logs do Squid e Threat Hunting

Esta pasta reúne exemplos de recolha e análise dos logs do proxy Squid no Wazuh.

## Conteúdo

- [Guia de monitorização e Threat Hunting](Threat_Hunting_squid.md): recolha, decodificador, regras e validação.
- [Imagens de referência](imagens/README.md): capturas associadas à análise de eventos.

## Conteúdo do guia

- Exemplos de `localfile` no agente para ler `/var/log/squid/*.log` ou `/var/log/squid/access.log`.
- Decodificador `squid-custom` com campos como IP de origem, estado, bytes, método, destino e utilizador.
- Regras de exemplo para acessos web, transferências, extensões de ficheiros e identificação de navegadores.
- Validação no servidor com `/var/ossec/bin/wazuh-logtest`.
- Pesquisa no dashboard em **Threat Hunting → Events**, com o filtro `rule.groups: squid`.

## Pontos a conferir na utilização

Consulte o [ficheiro de configuração atual do Squid](../../arquivo-de-configuracao/proxy-squid-config.txt) para o formato de log. O guia ainda contém uma ligação antiga para esse ficheiro na raiz do repositório.

O esquema de hierarquia do documento refere a regra raiz `100349`, enquanto o bloco XML usa `35000` como pai das regras principais. Confirme a associação entre regras e decodificadores no ambiente e valide os exemplos com linhas reais do log antes de os aplicar.

[Voltar à documentação Wazuh](../README.md)
