<p align="center">
  <a href="https://ag.tavardt.com/pt/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# Cloudflare WAF Hardening Ruleset & Blueprint de Segurança de Borda

[![Cloudflare WAF](https://img.shields.io/badge/Cloudflare-WAF%20Hardened-F38020.svg?logo=cloudflare)](https://cloudflare.com)
[![Diagnostics Suite](https://img.shields.io/badge/TAVARDT-Diagnostics-5b3cf5.svg)](https://tools.tavardt.com/pt/análise-cabecalhos-seguranca/)
[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

***

Conjunto de regras customizadas para Cloudflare WAF, mitigação de bots e blueprint de segurança perimetral desenvolvido pela **TAVARDT**, em estrito alinhamento com as diretrizes do CNCS, o RGPD da União Europeia e ISO/IEC 27001.

> [!IMPORTANT]
> **Arquitetura Perimetral Universal e Agnóstica de Stack:** Este conjunto de regras não se restringe ao WordPress ou a plataformas monolíticas. Ele foi concebido para proteger APIs RESTful, microsserviços modernos, frontends SPA (Next.js, React, Vue) e backends (Node.js, Python, Go, Laravel, Java). O descarte de requisições de CMS legadas atua como uma camada ativa de higiene perimetral, bloqueando o ruído de botnets diretamente nos datacenters de borda da Cloudflare em menos de 1ms antes que o tráfego atinja os servidores de origem.

***

## Capacidades Centrais e Arquitetura Defensiva

* **Isolamento Perimetral de Credenciais**: Bloqueia de imediato na borda o acesso a arquivos `.env`, repositórios `.git`, despejos `.sql`, `config.json`, `package.json` e `composer.json` antes de chegarem à origem.
* **Supressão de Ferramentas de Reconhecimento**: Intercepta scanners automáticos de vulnerabilidades (Nikto, SQLMap, Acunetix, Masscan, Zgrab, Nmap, DirBuster, Gobuster) na borda da Cloudflare.
* **Higiene Perimetral Agnóstica de Stack**: Elimina vetores de abuso direcionados a CMS legados (`/xmlrpc.php`, trackbacks) em menos de 1ms, poupando CPU, memória e conexões TCP na origem.
* **Higienização de Métodos HTTP**: Rejeita métodos perigosos ou de depuração (`TRACE`, `TRACK`, `DEBUG`) diretamente na barreira perimetral do firewall.
* **Desafio Adaptativo para Rotas Administrativas**: Aplica Desafios Gerenciados da Cloudflare em portais de autenticação (`/admin`, `/wp-login.php`, `/administrator`), permitindo tráfego autorizado de ASN interno.

***

## Vetores de Risco e Impacto Operacional

Publicar aplicações e APIs na internet sem filtragem declarativa de borda expõe servidores de origem a varreduras automatizadas contínuas e esgotamento de recursos. O conjunto neutraliza cinco vetores críticos de risco no perímetro da CDN:

* **Exposição de Credenciais e Configurações (TAVARDT-WAF-01)**: Exploração de servidores mal configurados para captura de segredos em arquivos de ambiente, históricos Git e despejos de banco de dados.
* **Varreduras Automatizadas de Exploração (TAVARDT-WAF-02)**: Sondagens hostis de scanners que sobrecarregam a largura de banda, elevam a carga de CPU e tentam localizar falhas conhecidas.
* **Ataques de Amplificação e Força Bruta em CMS (TAVARDT-WAF-03 e TAVARDT-WAF-05)**: Redes zumbis tentando amplificação de negação de serviço via pingbacks XML-RPC ou ataques de força bruta em portais administrativos.
* **Abuso de Protocolo via Adulteração de Verbos (TAVARDT-WAF-04)**: Agentes maliciosos que utilizam métodos HTTP obsoletos para contornar caches, explorar cross-site tracing (XST) ou forçar depurações.
* **Esgotamento de Recursos e Indisponibilidade na Origem**: Tráfego malicioso não filtrado que ocupa threads de aplicação, gerando lentidão e indisponibilidade para usuários legítimos.

***

## Implementação e Automação de Borda

Aplique o conjunto de regras defensivas utilizando a API v4 declarativa da Cloudflare ou através de infraestrutura como código com Terraform:

### Opção A: Ingestão Direta via Cloudflare API v4

```bash
# Aplicar regras de firewall customizadas via Cloudflare API v4
curl -X PUT "https://api.cloudflare.com/client/v4/zones/YOUR_ZONE_ID/rulesets/phases/http_request_firewall_custom/entrypoint" \
  -H "Authorization: Bearer YOUR_CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d @rules/ruleset.json
```

### Opção B: Provisionamento Declarativo com Terraform

```bash
# Acessar o diretório do módulo Terraform
cd terraform

# Inicializar o provedor Cloudflare
terraform init

# Planejar a aplicação declarativa das regras
terraform plan -var="zone_id=YOUR_ZONE_ID"

# Aplicar a configuração de firewall perimetral
terraform apply -var="zone_id=YOUR_ZONE_ID"
```

***

## Perguntas Técnicas Frequentes

### Quais vetores de risco o conjunto de regras WAF da Cloudflare neutraliza na borda da CDN?

O conjunto bloqueia scanners automatizados de reconhecimento, tentativas de exposição de arquivos sensíveis e variáveis de ambiente, abuso de XML-RPC e métodos HTTP de depuração diretamente na borda da Cloudflare. Esse descarte perimetral interrompe tráfego malicioso em menos de 1 milissegundo, preservando CPU, memória e conexões de banco de dados do servidor de origem.

### Por que esta arquitetura de hardening é indicada para APIs REST e microsserviços modernos além de CMS?

Scanners automatizados de vulnerabilidades sondam rotas públicas de modo indiscriminado, independentemente de a aplicação rodar em Node.js, Go, Python ou Next.js. Descartar sondagens cegas de CMS e requisições a arquivos sensíveis no perímetro mantém a higiene da infraestrutura livre de ruído computacional.

### Como o conjunto impede o vazamento acidental de credenciais e despejos de bancos de dados?

A regra TAVARDT-WAF-01 executa bloqueio imediato HTTP 403 para requisições com caminhos contendo `.env`, `.git`, `.sql`, `composer.json` ou `package.json`. Esse controle determinístico na borda impede que falhas de roteamento na origem exponham chaves de API privadas e estruturas de dados sensíveis.

### Como funciona o desafio em rotas administrativas sem prejudicar serviços internos autorizados?

A regra TAVARDT-WAF-05 aciona Desafios Gerenciados da Cloudflare em acessos a caminhos sensíveis como `/wp-login.php`, `/admin` e `/administrator`. Ela inclui uma exceção explícita para o ASN 13335 da Cloudflare, assegurando que rotinas internas e sondas de monitoramento operem sem bloqueios indevidos.

### Quais são as diferenças operacionais entre implantar via API v4 da Cloudflare e utilizar Terraform?

A importação de `rules/ruleset.json` via API v4 proporciona uma implementação direta e sem dependências locais na fase `http_request_firewall_custom`. Já o provisionamento com o módulo `terraform/main.tf` viabiliza gerenciamento como código, detecção de desvios de configuração e padronização entre múltiplas zonas de produção.

***

## Links Úteis

* **Portal Corporativo:** https://ag.tavardt.com/pt/
* **Suíte de Diagnósticos:** https://tools.tavardt.com/pt/
* **Contacto Técnico:** https://ag.tavardt.com/pt/contacto/

***

### Licença
Distribuído sob a Licença MIT. Consulte [LICENSE](LICENSE) para obter detalhes completos.

***

© 2026 TAVARDT. Todos os direitos reservados.
