1. Serviço e disponibilidade do Squid
Zabbix deve saber se o proxy está efetivamente operacional.

Monitorizar:

Estado do serviço squid
Processo do Squid ativo
Porta TCP 3128
Disponibilidade do proxy
Tempo de resposta do proxy
Número de processos/workers
Reinícios inesperados
Uptime do Squid


a. apt install squidclient -y

b. se estiver a utilizar a configuração do cmeasy passe estes campos para cima das informacoes comnetadas do 2 
acl manager proto cache_object
http_access allow localhost manager
http_access deny manager

c. cirar item
os intem serao as metricas monitoradas pelo zabbix 

vamos em data doletions host e no host correspondente clicamos em items e de seguida clicamos em create items e criamos os seguites items 

1. controlo do estado do serviço
Name: Uptime do Squid
Type: Zabbix agent
Key: squid.uptime
Type of information: Numeric (float) (Importante, devido às casas decimais)
Units: s
Clique em Add.

2. 
Name: Squid: Número de processos
Type: Zabbix agent
Key: proc.num[squid]
Type of information: Numeric (unsigned)
Clique em Add.

3.
Name: Squid: Porta 3128 aberta
Type: Zabbix agent
Key: net.tcp.service[tcp,,3128]
Type of information: Numeric (unsigned)
Clique em Add.

4.
Name: Squid: Tempo de resposta TCP
Type: Zabbix agent
Key: net.tcp.service.perf[tcp,,3128]
Type of information: Numeric (float)
Units: s
Clique em Add.