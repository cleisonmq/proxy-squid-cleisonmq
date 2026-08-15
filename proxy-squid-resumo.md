# Relatório de Projeto: Implementação de Gateway de Segurança Perimetral com Squid Proxy e SIEM Wazuh

## 1. Introdução e Contexto Técnico
O controlo de acessos e a monitorização de recursos de rede representam pilares críticos na arquitetura de segurança das organizações modernas. É imperativo mitigar os riscos de navegação através do bloqueio de domínios maliciosos e conteúdos não autorizados, minimizando vetores de ataque e garantindo a conformidade com as políticas de utilização aceitável. 

Este projeto consistiu no desenho, provisionamento e validação de um servidor **Forward Proxy** baseado na solução *open-source* **Squid**, integrado num ecossistema virtual sobre o sistema operativo **Ubuntu Server 22.04 LTS**. Toda a fase de instalação, *hardening* e parametrização foi otimizada e automatizada com recurso à ferramenta **CMEasy** (versão 1.0), desenvolvida internamente, reduzindo o tempo operacional em aproximadamente 60%.

---

## 2. Fundamentos Teóricos da Tecnologia Proxy
Um servidor proxy atua como um intermediário em sistemas distribuídos, posicionando-se entre os clientes da rede interna (LAN) e os servidores de destino na Internet. 

### 2.1. Fluxo de Funcionamento
1. O cliente realiza um pedido de recurso web (HTTP/HTTPS/FTP).
2. O pedido é intercetado pela **Interface de Receção** do proxy.
3. A **Camada de Processamento e Filtragem** valida as credenciais e confronta o destino com as Listas de Controlo de Acesso (ACLs).
4. Caso o conteúdo não esteja armazenado no **Módulo de Cache**, o componente **Forwarder** encaminha a requisição ao servidor de destino e devolve a resposta mascarada ao utilizador.

### 2.2. Vantagens e Desvantagens do Squid Proxy
* **Vantagens:** Otimização de largura de banda via cache de conteúdos; controlo granular através de ACLs; ocultação da topologia de rede e dos IPs internos; suporte a fluxos de auditoria e integração com provedores de identidade corporativos (LDAP/AD).
* **Desvantagens:** Complexidade na gestão manual de regras complexas; limitações de cache face ao tráfego cifrado moderno (HTTPS) que exige técnicas invasivas como *SSL Bump*; e o risco operacional de se tornar um Ponto Único de Falha (*Single Point of Failure*) na rede.

---

## 3. Especificações e Requisitos da Infraestrutura
O ambiente foi totalmente virtualizado obedecendo aos seguintes perfis de recursos:
# Sistemas Operativos Suportado:
Debian, Ubuntu Server, RHEL, Rocky Linux, AlmaLinux, CentOS Stream, FreeBSD, OpenBSD, NetBSD, macOS (ambientes POSIX).
# Recursos de Hardware :
### 1. Pequeno Porte
* **Utilizadores Concorrentes:** Até 100
* **CPU:** 2 Cores
* **RAM Mínima:** 4 GB
* **Armazenamento (Cache + Logs):** 50 GB a 100 GB SSD
---
### 2. Médio Porte
* **Utilizadores Concorrentes:** 100 a 1.000
* **CPU:** 4 – 8 Cores
* **RAM Mínima:** 8 GB – 16 GB
* **Armazenamento (Cache + Logs):** 250 GB – 500 GB NVMe
---
### 3. Grande Porte / Enterprise
* **Utilizadores Concorrentes:** 1.000+ ou SSL-Bump intensivo
* **CPU:** 8+ Cores (SMP workers)
* **RAM Mínima:** 32 GB – 64 GB+
* **Armazenamento (Cache + Logs):** 1 TB+ NVMe (RAID 10)
---
### Tabela Resumo

| Perfil de Rede | Utilizadores Concorrentes | CPU | RAM Mínima | Armazenamento (Cache + Logs) |
| :--- | :--- | :--- | :--- | :--- |
| **Pequeno Porte** | Até 100 | 2 Cores | 4 GB | 50 GB a 100 GB SSD |
| **Médio Porte** | 100 a 1.000 | 4 – 8 Cores | 8 GB – 16 GB | 250 GB – 500 GB NVMe |
| **Grande Porte / Enterprise** | 1.000+ ou SSL-Bump intensivo | 8+ Cores (SMP workers) | 32 GB – 64 GB+ | 1 TB+ NVMe (RAID 10) |
* **Conectividade:** Interface de rede com IP fixo configurada em ambiente NAT.

---

## 4. Considerações Finais e Aplicabilidade
O projeto cumpriu os objetivos estabelecidos ao entregar uma solução funcional de gateway perimetral capaz de suportar as rotinas operacionais de gestão, filtragem estruturada e auditoria de tráfego de redes. 

A arquitetura desenvolvida exibe uma elevada versatilidade:
* **Ambientes Domésticos / Servidores Caseiros:** Apresenta-se como uma excelente ferramenta de controlo parental, otimização de largura de banda e isolamento de privacidade laboratorial para entusiastas de TI.
* **Ambientes Empresariais e Organizações:** É perfeitamente viável e escalável para cenários corporativos, necessitando apenas de configurações avançadas e suplementares de segurança (tais como inspeção profunda de pacotes HTTPS via *SSL Bump* e balanceamento de carga para alta disponibilidade) para fazer face a ambientes de alta criticidade.

A integração final da telemetria com a ferramenta Wazuh elevou a maturidade de cibersegurança da infraestrutura, fornecendo visibilidade holística e dotando a administração de capacidades ágeis de deteção, triagem e resposta a incidentes de segurança.

---

## Referências Bibliográficas
* Fortinet. (s.d.). *Cyber Glossary: Proxy Server*. Obtido de fortinet.com.
* Máquina, C. (2026). *Repositório Pessoal do Desenvolvedor*. Obtido de github.com/cleisonmq.
* Máquina, C. (05 de 2026). *CMEASY-V1.0-CLEISON-MAQUINA-2026*. Obtido de github.com/cleisonmq.
* Microsoft. (s.d.). *Usar um servidor proxy no Windows*. Obtido de microsoft.com.
* Squid-Cache.org. (s.d.). *Squid Web Proxy Cache*. Obtido de squid-cache.org.
* Wikipedia. (2016). *Servidor Proxy*. Obtido de wikipedia.org.