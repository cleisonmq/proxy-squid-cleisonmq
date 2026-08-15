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
* **Sistema Operativo:** Ubuntu Server 22.04 LTS (Kernel Linux 5.15).
* **Recursos de Hardware da VM:** 1 Núcleo de CPU, 2 GB de Memória RAM e 25 GB de Armazenamento em Disco (ext4).
* **Conectividade:** Interface de rede com IP fixo configurada em ambiente NAT.

---

## 4. Engenharia de Sistemas, Instalação e Hardening
A implementação seguiu preceitos rigorosos de *Defense-in-Depth* aplicados via ferramenta CMEasy:

1. **Hardening do OpenSSH:** A porta padrão de administração remota (TCP 22) foi alterada para a porta alternativa **TCP 2222**. Desativou-se o login direto do utilizador `root` e a autenticação por palavra-passe, impondo exclusivamente o uso de chaves criptográficas assimétricas (par de chaves via `ssh-keygen` / `ed25519`).
2. **Configuração de Firewall Host-Based (UFW):** Estabeleceu-se uma política restritiva de *Deny by Default*, liberando estritamente os portos necessários para a administração e operação (TCP 2222 para SSH e TCP 3128 para o tráfego do Squid).
3. **Instalação Automatizada:** O script do CMEasy efetuou a atualização de pacotes (`apt-get full-upgrade`), instalou os binários do `squid` e dependências como o `apache2-utils` (para geração de hashes criptográficos de senhas). Criou-se também uma rotina de backup automático da configuração padrão de fábrica do Squid.

---

## 5. Implementação de Controlos e Políticas de Segurança
As diretrizes aplicadas alinham-se com padrões internacionais de conformidade e governança, tais como a **Diretiva NIS2** e a norma **ISO/IEC 27001**.

### 5.1. Mecanismo de Filtragem e Bloqueio de Websites
Foi desenvolvida uma política de restrição de conteúdos gerida por ACLs. Através do módulo dedicado da ferramenta CMEasy, domínios considerados não conformes ou maliciosos foram injetados num ficheiro de regras do Squid (ex: criação de listas de *sites banidos*). O sistema adota uma postura de segurança *deny by default*, na qual apenas os tráfegos explicitamente validados são permitidos.

### 5.2. Controlo de Identidades (Acesso por Autenticação)
Para suportar o **Princípio do Menor Privilégio**, foi ativado o módulo de autenticação básica do Squid. Os utilizadores autorizados foram provisionados através do utilitário `htpasswd`. Para mitigar riscos de leitura local e vazamento de hashes, o ficheiro de credenciais foi protegido com permissões restritas no Linux (`chmod 640`, propriedade exclusiva do utilizador do processo do proxy).

---

## 6. Centralização de Telemetria e Integração SIEM (Wazuh)
Com o intuito de eliminar pontos cegos na rede e garantir a auditoria contínua, o ecossistema do proxy foi acoplado a uma arquitetura de monitorização centralizada baseada no **SIEM Wazuh**.

1. **Instalação do Agente:** O agente leve do Wazuh foi provisionado e registado diretamente no servidor do Squid Proxy.
2. **Encaminhamento de Logs:** Utilizando o utilitário CMEasy, o ficheiro de configuração do agente (`ossec.conf`) foi modificado para recolher ativamente as saídas estruturadas do Squid, focando especificamente no `access.log` (registos de pedidos de navegação) e no `cache.log` (eventos de sistema).
3. **Mecanismo de Alerta:** O SIEM foi parametrizado com regras de correlação de eventos. Qualquer desvio tático ou violação grave das políticas de segurança despoleta alertas imediatos em tempo real na consola do SOC, complementado pelo envio automatizado de e-mails de alerta para os administradores de sistemas.

---

## 7. Fase de Testes e Validação Operacional
Os testes de conformidade foram conduzidos recorrendo a clientes em ambientes híbridos (máquinas de teste com sistemas operativos **Windows** e **Linux**) configurados para encaminhar o tráfego web através do IP do proxy na porta 3128.

* **Validação do Filtro de Conteúdo:** As tentativas de acesso a domínios bloqueados foram travadas na totalidade pelo Squid, que devolveu códigos de erro de acesso proibido (HTTP 403) aos browsers dos clientes.
* **Validação da Autenticação:** A navegação externa foi condicionada com sucesso à inserção de credenciais válidas na janela de autenticação do utilizador.
* **Validação de Resposta a Incidentes (SIEM):** Ataques simulados baseados em tentativas repetidas de autenticação fraudulenta geraram de imediato alertas críticos na plataforma Wazuh e notificações por correio eletrónico, atestando a eficácia técnica do sistema de monitorização.

---

## 8. Considerações Finais e Aplicabilidade
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