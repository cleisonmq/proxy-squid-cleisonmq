# Proxy Squid

Repositório com a documentação de um projeto de implementação de um **gateway de segurança perimetral** usando o **Squid Proxy**, integrado com o **SIEM Wazuh**, desenvolvido por Cleison Alves José Máquina (Técnico de Cibersegurança, 2026).

## Sobre o projeto

O projeto consiste no desenho, instalação e validação de um servidor **Forward Proxy** baseado no Squid (open-source), montado sobre **Ubuntu Server 22.04 LTS**. Todo o processo de instalação e configuração foi automatizado com a ferramenta interna **CMEasy v1.0**, reduzindo o tempo de implementação em cerca de 60%.

O Squid atua como intermediário entre a rede interna (LAN) e a Internet, permitindo:
- Controlo de acessos através de ACLs (listas de controlo de acesso)
- Cache de conteúdos para otimizar a largura de banda
- Ocultação da topologia de rede e dos IPs internos
- Bloqueio de domínios maliciosos e conteúdos não autorizados
- Integração com sistemas de auditoria/monitorização (Wazuh)

## Aplicabilidade

- **Uso doméstico:** controlo parental, otimização de banda e privacidade
- **Uso empresarial:** filtragem de tráfego, auditoria e conformidade, escalável para ambientes de alta criticidade (com SSL Bump e balanceamento de carga)

## Referências

Documentação baseada em fontes como Squid-Cache.org, Fortinet, Microsoft e Wikipedia (ver detalhes em `proxy-squid-resumo.md`).