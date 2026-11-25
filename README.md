# Cloudflare WAF Hardening Ruleset & Blueprint de Segurança de Borda

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Cloudflare WAF](https://img.shields.io/badge/Cloudflare-WAF%20Hardened-F38020.svg?logo=cloudflare)](https://cloudflare.com)
[![Language](https://img.shields.io/badge/Language-Português%20(Portugal)-green.svg)](README.md)
[![Diagnostics Suite](https://img.shields.io/badge/TAVARDT-Diagnostics-5b3cf5.svg)](https://tools.tavardt.com/pt/análise-cabecalhos-seguranca/)

Regras de segurança perimetral e mitigação de ameaças na borda para Cloudflare, em estrita conformidade com o RGPD e as diretrizes do Centro Nacional de Cibersegurança (CNCS).

Desenvolvido e mantido pela equipa de engenharia de segurança perimetral da **TAVARDT Portugal**.

> [!IMPORTANT]
> **Arquitetura Universal & Agnóstica de Stack:** Este repositório **não é restrito ao WordPress**. Foi arquitetado para proteger **APIs REST, microsserviços modernos, aplicações web (Next.js, React, Vue) e plataformas de backend (Node.js, Python, Go, Laravel, Java)**. O bloqueio de pedidos a CMS legados atua como uma **camada de higiene perimetral ativa**, descartando o tráfego parasita de botnets na rede da Cloudflare (< 1ms) antes que sobrecarregue os seus servidores de origem.

***

### Matriz de Proteção Multi-Stack

| Camada de Defesa | Vetores Mitigados | Plataformas & Stacks Protegidas |
| :- | :- | :- |
| **Isolamento de Credenciais** | Bloqueio de `.env`, `.git`, `.sql`, `package.json`, `composer.json` | **APIs, Next.js, Node.js, Python, Go, Laravel, React** |
| **Mitigação de Reconhecimento** | Bloqueio de Nikto, SQLMap, Acunetix, Masscan, Gobuster, Nmap | **Qualquer plataforma web, microsserviço ou API pública** |
| **Higiene de Verbos HTTP** | Rejeição de métodos desnecessários (`TRACE`, `TRACK`, `DEBUG`) | **APIs RESTful e arquiteturas de microsserviços** |
| **Desafio em Painéis** | Desafio gerido em rotas críticas (`/admin`, `/wp-login.php`) | **Consolas de administração, portais ERP e back-offices** |
| **Higiene de Ruído Cego** | Descarte na borda de pedidos a `/xmlrpc.php` e trackbacks | **Redução imediata de processamento nos servidores de origem** |

***

## 1. Visão Geral da Arquitetura Perimetral

Este repositório fornece regras declarativas prontas para aplicação na camada de borda da Cloudflare, prevenindo incidentes e violações de integridade em plataformas e infraestruturas digitais:

* **Isolamento de Ficheiros Confidenciais:** Bloqueio perimetral de pedidos a ficheiros `.env`, repositórios `.git`, cópias de segurança `.sql` e manifestos de dependências (`composer.json`, `package.json`).
* **Mitigação de Varreduras Automatizadas:** Interceção imediata de ferramentas de pesquisa e exploração conhecidas (Nikto, SQLMap, Acunetix, Masscan, Nmap, Gobuster, DirBuster).
* **Blindagem de Plataformas CMS:** Bloqueio de pedidos abusivos direcionados a `/xmlrpc.php` e `/wp-trackback.php`.
* **Filtragem de Verbos HTTP Inseguros:** Rejeição no perímetro de métodos de depuração desnecessários (`TRACE`, `TRACK`, `DEBUG`).
* **Desafio Gerido em Rotas Críticas:** Proteção de interfaces administrativas (`/wp-login.php`, `/admin`) através de validações comportamentais sem fricção.

***

## 2. Diagnóstico Prévio de Segurança

Antes de proceder à implementação em ambiente produtivo, valide a conformidade dos cabeçalhos de segurança e a resposta de rede do seu domínio através da plataforma da TAVARDT:

* **Auditoria de Cabeçalhos e Certificados SSL:** Análise de Cabeçalhos de Segurança TAVARDT
* **Catálogo Completo de Ferramentas de Diagnóstico:** **TAVARDT Tools Portugal**

***

## 3. Guia de Implementação

### Opção A: Importação Direta via API Cloudflare v4

Utilize a especificação estruturada [`rules/ruleset.json`](rules/ruleset.json):

```bash
curl -X PUT "https://api.cloudflare.com/client/v4/zones/O_SEU_ZONE_ID/rulesets/phases/http_request_firewall_custom/entrypoint" \
     -H "Authorization: Bearer O_SEU_CLOUDFLARE_API_TOKEN" \
     -H "Content-Type: application/json" \
     -data @rules/ruleset.json
```

### Opção B: Provisionamento com Terraform

Utilize a definição declarativa contida no diretório [`terraform/main.tf`](terraform/main.tf):

```bash
cd terraform
terraform init
terraform plan -var="zone_id=O_SEU_ZONE_ID"
terraform apply -var="zone_id=O_SEU_ZONE_ID"
```

***

## 4. Consultoria Especializada & Resposta a Incidentes

Para auditorias de segurança avançadas, conformidade com normas europeias (RGPD / NIS 2), blindagem perimetral ou apoio técnico contínuo:

* **Portal Corporativo:** **TAVARDT**
* **Linha de Contacto Técnico:** [Apoio e Engenharia Especializada](https://ag.tavardt.com/pt/contactos/)

***

## Links Úteis

* **Portal Corporativo:** https://ag.tavardt.com/pt/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/pt/
* **Contacto Técnico:** https://ag.tavardt.com/pt/contacto/

***

### Licença

Distribuído sob a licença de código aberto MIT. Consulte o ficheiro [`LICENSE`](LICENSE) para mais detalhes.

***

© 2026 TAVARDT Portugal. Todos os direitos reservados. Soberania e Defesa de Infraestrutura.
