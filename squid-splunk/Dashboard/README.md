# Dashboard Splunk para o Squid

Dashboard para analisar os eventos de `/var/log/squid/access.log`, com tabelas de pedidos e bloqueios e um gráfico de volume de atividade por origem.

## Ficheiro disponível

[conf.xml](conf.xml) contém a definição do dashboard em **Splunk Simple XML**, com o título `squid-server-cleisonmq`.

## Painéis e consultas

| Painel | Comportamento da consulta |
| --- | --- |
| Métodos HTTP | Lista pedidos com origem, método, URI, utilizador, estado e campo de agente |
| Ligações em túnel | Extrai destinos no formato host:porta e classifica os códigos de resposta |
| Metadados e cabeçalhos | Seleciona pedidos HEAD, consultas ao gestor e alguns serviços de verificação de IP; analisa campos de cabeçalhos registados |
| Acessos bloqueados | Seleciona eventos com DENIED ou 403 e atribui uma classificação através de padrões no URL |
| Volume por origem | Conta pedidos por IP em intervalos de 10 segundos, na última hora |

## Preparação dos dados

O dashboard pressupõe que os logs já estão disponíveis no Splunk com `source="/var/log/squid/access.log"`. Consulte os [guias do Universal Forwarder](../agente-slpunk/README.md) para instalação e ligação do agente.

O [ficheiro de configuração do Squid](../../arquivo-de-configuracao/proxy-squid-config.txt) inclui o formato com User-Agent e os campos `Via_Resp`, `Server_Resp` e `Cache_Resp` utilizados na análise de metadados.

## Alcance das consultas atuais

- O seletor de servidor no topo é aplicado apenas à tabela de métodos HTTP.
- A tabela de métodos lista os pedidos sem restringir a pesquisa a TRACE ou TRACK.
- A consulta de destinos host:porta não filtra explicitamente pelo método CONNECT nem por portas proibidas.
- A classificação de acessos negados é uma heurística baseada no URL; não identifica a ACL que causou o bloqueio.
- O gráfico conta pedidos registados, sem medir diretamente ligações simultâneas.
- A extração do agente na primeira tabela procura o último campo entre aspas. No formato com cabeçalhos adicionais, esse campo corresponde a `Cache_Resp`, pelo que a extração precisa de ajuste para apresentar o User-Agent.

[Voltar à integração com Splunk](../README.md)
