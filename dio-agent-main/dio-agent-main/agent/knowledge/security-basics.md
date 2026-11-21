---
name: security-basics
description: Base de conhecimento do GuardAI com fundamentos de cibersegurança (princípios, risco, criptografia, identidade, rede, aplicações, sistemas, nuvem, dados, detecção e resposta, frameworks) e um bloco enxuto de conceitos avançados. Consulte ao explicar conceitos, justificar recomendações ou calibrar o nível técnico da resposta.
version: 1.0.0
---

# Fundamentos de Segurança (GuardAI)

Referência de conhecimento para embasar as skills `vulnerability-analysis` e `network-analysis`. Foco em **defesa**. Os conceitos avançados ficam concentrados na seção 14, de propósito enxuta.

**Como usar:** consulte só a seção relevante, explique no nível do usuário (iniciante → direto ao ponto; experiente → termos técnicos), não despeje o arquivo inteiro e nunca invente números de versão, CVEs ou normas. Se algo puder ter mudado (edição do OWASP Top 10, versões de normas, algoritmos recomendados), confirme na fonte oficial.

---

## 1. Princípios fundamentais

| Princípio | Ideia central |
|-----------|---------------|
| **Confidencialidade** | Só quem deve acessa a informação (criptografia, controle de acesso). |
| **Integridade** | A informação não é alterada indevidamente (hash, assinatura, controle de mudança). |
| **Disponibilidade** | O sistema funciona quando necessário (redundância, backup, proteção contra DoS). |
| **Autenticidade / Não repúdio** | Saber **quem** fez algo e impedir a negação (assinatura digital, logs de auditoria). |
| **AAA** | **A**utenticação (quem é), **A**utorização (o que pode), **A**uditoria/contabilização (o que fez). |
| **Defesa em profundidade** | Várias camadas independentes; a falha de uma não derruba tudo. |
| **Menor privilégio** | Cada identidade recebe só o acesso mínimo e pelo tempo necessário. |
| **Negar por padrão** | Tudo é bloqueado a menos que explicitamente permitido (allowlist > blocklist). |
| **Separação de funções** | Ações críticas exigem mais de uma pessoa/papel. |
| **Falhar de forma segura** | Em erro, o sistema nega acesso em vez de liberar (fail closed). |
| **Mediação completa** | Todo acesso é verificado **a cada requisição**, não só no login. |
| **Economia de mecanismo** | Quanto mais simples, menos superfície para falhas. |
| **Segurança por design** | Segurança desde o projeto ("shift left"), não remendo no fim. |
| **Princípio de Kerckhoffs** | A segurança deve depender da **chave**, não do sigilo do algoritmo. "Segurança por obscuridade" não é controle. |
| **Zero Trust** | Nenhuma confiança implícita por estar "dentro da rede": verifique identidade, dispositivo e contexto sempre. |

---

## 2. Vocabulário de risco

| Termo | Significado |
|-------|-------------|
| **Ativo** | Qualquer coisa de valor: dados, sistemas, pessoas, reputação. |
| **Ameaça** | Algo/alguém que pode causar dano (atacante, malware, erro humano, desastre). |
| **Vulnerabilidade** | Fraqueza que uma ameaça pode explorar. |
| **Exploit** | Técnica/código que aproveita uma vulnerabilidade. |
| **Risco** | Probabilidade × impacto de uma ameaça explorar uma vulnerabilidade num ativo. |
| **Controle** | Medida que reduz risco: preventivo, detectivo, corretivo, dissuasivo, compensatório. |
| **Evento / Incidente** | Evento = ocorrência observável; incidente = evento que viola ou ameaça a segurança. |
| **Superfície de ataque** | Soma de pontos expostos a um atacante (portas, APIs, usuários, dependências). |
| **Vetor de ataque** | Caminho usado (e-mail, web, rede, física, cadeia de suprimentos). |
| **Risco inerente / residual** | Antes dos controles / o que resta depois. |
| **Zero-day** | Falha sem correção disponível ao público. |

**Tratamento de risco:** **mitigar** (reduzir), **transferir** (seguro, terceirização), **evitar** (eliminar a atividade) ou **aceitar** (com dono, justificativa e revisão).

**Identificadores e métricas úteis**

| Sigla | O que é |
|-------|---------|
| **CVE** | Identificador público de uma vulnerabilidade específica. |
| **CWE** | Catálogo de **classes** de fraqueza (ex.: CWE-89, injeção SQL). |
| **CVSS** | Pontua a **severidade técnica** (0–10); não mede risco no seu contexto. |
| **EPSS** | Probabilidade estimada de exploração nos próximos 30 dias. |
| **KEV (CISA)** | Catálogo de vulnerabilidades **comprovadamente exploradas**; forte sinal de prioridade. |
| **NVD** | Base nacional de vulnerabilidades (dados de CVE e CVSS). |

> CVSS alto sem exposição ≠ urgência; CVSS médio com exploração ativa e ativo exposto = urgência. Contexto vence o número.

---

## 3. Modelagem de ameaças

Perguntas-base: **o que estamos protegendo? do que/quem? o que pode dar errado? o que já fazemos? funcionou?**

- **Fronteiras de confiança:** onde o dado muda de nível de confiança (internet → app, app → banco, usuário → admin). Falhas se concentram nelas.
- **STRIDE** (por elemento do diagrama de fluxo de dados):

| Letra | Ameaça | Propriedade violada | Contramedida típica |
|-------|--------|--------------------|---------------------|
| **S**poofing | Falsificar identidade | Autenticidade | Autenticação forte, MFA, assinatura |
| **T**ampering | Alterar dados | Integridade | Hash/assinatura, controle de acesso, TLS |
| **R**epudiation | Negar autoria | Não repúdio | Logs imutáveis, assinatura |
| **I**nformation disclosure | Vazamento | Confidencialidade | Criptografia, autorização, minimização |
| **D**enial of service | Indisponibilizar | Disponibilidade | Rate limiting, redundância, quotas |
| **E**levation of privilege | Ganhar acesso indevido | Autorização | Menor privilégio, validação server-side |

- **MITRE ATT&CK:** matriz de táticas e técnicas reais de adversários (acesso inicial, execução, persistência, escalonamento, movimento lateral, exfiltração...). Útil para **mapear detecções** e lacunas de cobertura.
- **Cyber Kill Chain:** modelo linear (reconhecimento → armamento → entrega → exploração → instalação → C2 → ações no objetivo). Interromper **qualquer** etapa derruba o ataque.

---

## 4. Criptografia essencial

| Primitiva | Para quê | Recomendado (de forma geral) | Evitar |
|-----------|----------|------------------------------|--------|
| **Simétrica** | Cifrar dados (mesma chave) | AES-GCM, ChaCha20-Poly1305 (cifras **autenticadas**, AEAD) | DES/3DES, RC4, ECB, cifra sem autenticação |
| **Assimétrica** | Troca de chaves, assinatura | RSA ≥ 2048 (ideal 3072), ECDSA/ECDH (curvas P-256, X25519), Ed25519 | RSA < 2048, chaves reutilizadas sem controle |
| **Hash** | Integridade, impressão digital | SHA-256/384/512, SHA-3, BLAKE2/3 | MD5 e SHA-1 para segurança |
| **HMAC** | Integridade + autenticidade com segredo | HMAC-SHA-256 | `hash(segredo + dado)` ingênuo |
| **KDF para senhas** | Armazenar senhas | **Argon2id**, scrypt, bcrypt (com salt e custo ajustado) | SHA-256 "puro", MD5, hash sem salt |
| **Assinatura digital** | Autenticidade + não repúdio | Ed25519, ECDSA, RSA-PSS | Assinar com chave mal protegida |

**Pontos que mais importam**
- **Hash não é criptografia**: é unidirecional; **encoding (Base64) também não protege nada**.
- **Cifrar ≠ autenticar**: use sempre **AEAD** para detectar adulteração.
- **Nunca invente criptografia própria**; use bibliotecas consolidadas (libsodium, OpenSSL, bibliotecas padrão da linguagem).
- **Aleatoriedade:** use gerador criptograficamente seguro (CSPRNG: `secrets`, `/dev/urandom`), nunca `rand()`/`Math.random()` para tokens e chaves.
- **Gestão de chaves é o ponto fraco real:** geração, armazenamento (HSM/KMS/cofre), rotação, revogação e separação de funções.
- **Nonce/IV** não pode se repetir com a mesma chave em modos como GCM (repetição quebra a segurança).
- **Em trânsito:** TLS 1.2+ (preferir **1.3**). **Em repouso:** criptografia de disco/volume/banco/objeto com chaves gerenciadas. **Em uso:** (avançado) computação confidencial.
- **PKI/certificados:** cadeia de confiança (CA raiz → intermediária → folha), validade, revogação (CRL/OCSP), verificação de **hostname**. Autoassinado só em lab; nunca desabilite a validação (`-k`, `verify=False`) em produção.

---

## 5. Identidade e controle de acesso

**Autenticação (fatores):** algo que **sabe** (senha), que **tem** (token, chave FIDO2), que **é** (biometria). **MFA** combina fatores distintos.

| Tópico | Boas práticas |
|--------|---------------|
| **Senhas** | Priorize **comprimento** e unicidade; bloqueie senhas vazadas/comuns; sem rotação forçada periódica sem motivo (orientação do NIST SP 800-63B); gerenciador de senhas. |
| **MFA** | Preferir **FIDO2/WebAuthn/passkeys** (resistentes a phishing) a SMS; TOTP é melhor que SMS; proteja o processo de recuperação de conta. |
| **Sessões** | ID aleatório e longo, `HttpOnly`/`Secure`/`SameSite`, expiração, invalidar no logout e na troca de privilégio. |
| **Federação** | **OAuth 2.0** (autorização delegada), **OIDC** (identidade sobre OAuth), **SAML** (SSO corporativo). Valide `state`, `redirect_uri`, `audience`, assinatura e expiração dos tokens. |
| **Modelos de autorização** | **RBAC** (por papel), **ABAC** (por atributos/contexto), **ReBAC** (por relações); sempre **no servidor**, por objeto e por função. |
| **Privilégio** | Contas nomeadas (sem compartilhadas), contas de serviço dedicadas, **PAM**/just-in-time para acesso administrativo, revisão periódica de acessos. |
| **Segredos** | Fora do código e do repositório; cofre (Vault, KMS, Secrets Manager); rotação; segredo vazado = **comprometido**. |

**Falhas clássicas:** IDOR/BOLA (acessar objeto de outro usuário trocando um ID), escalonamento horizontal/vertical, autorização só no front-end, tokens sem expiração, credenciais padrão, *credential stuffing* sem rate limit.

---

## 6. Segurança de rede

**Camadas (resumo):** enlace/L2 (MAC, ARP, VLAN) → rede/L3 (IP, ICMP, rotas) → transporte/L4 (TCP/UDP, portas) → aplicação/L7 (HTTP, DNS, SMB...).

**Controles**
- **Segmentação** (VLAN, sub-redes, microsegmentação) para limitar movimento lateral; separe usuários, servidores, gerência, IoT e convidados.
- **Firewall:** allowlist de origem/destino/porta; regras específicas > `any`; revisão periódica de regras órfãs.
- **IDS/IPS e monitoramento:** detectam padrões e anomalias (NIDS/HIDS, NDR).
- **VPN / acesso remoto:** MFA, menor privilégio de rede, preferir acesso por aplicação (ZTNA) a "rede inteira".
- **DNS:** resolvers confiáveis, DNSSEC onde cabível, bloqueio de domínios maliciosos, atenção a tunelamento DNS.
- **Wi-Fi:** WPA3 (ou WPA2-AES com senha forte), 802.1X para corporativo, desativar WPS.
- **Proteção a DoS:** rate limiting, CDN/WAF, anycast, capacidade e planos de contingência.

**Portas e protocolos comuns (referência)**

| Porta | Serviço | Observação de segurança |
|-------|---------|------------------------|
| 20/21 | FTP | Texto claro; preferir SFTP/FTPS; checar login anônimo |
| 22 | SSH | Chaves em vez de senha, sem root direto, restringir origem |
| 23 | Telnet | Texto claro; substituir por SSH |
| 25/465/587 | SMTP | Autenticação, TLS, SPF/DKIM/DMARC, evitar open relay |
| 53 | DNS | Evitar recursão aberta; TCP/UDP |
| 80/443 | HTTP/HTTPS | Forçar HTTPS e HSTS |
| 110/143/993/995 | POP3/IMAP | Preferir variantes com TLS |
| 139/445 | NetBIOS/SMB | Nunca expor à internet; desabilitar SMBv1 |
| 161/162 | SNMP | Preferir v3; sem comunidade padrão (`public`/`private`) |
| 389/636 | LDAP/LDAPS | Preferir LDAPS/StartTLS; sem bind anônimo |
| 1433/3306/5432 | SQL Server/MySQL/PostgreSQL | Não expor à internet; TLS e contas mínimas |
| 3389 | RDP | Nunca direto na internet; VPN/MFA/NLA |
| 5900 | VNC | Sem criptografia nativa; tunelar |
| 6379/27017/9200 | Redis/MongoDB/Elasticsearch | Historicamente expostos sem autenticação; vincular a interfaces internas e exigir auth |
| 5985/5986 | WinRM | Restringir; preferir HTTPS |

> Porta aberta **não** é vulnerabilidade por si só; o que importa é **se era esperada, para quem está acessível, em qual versão e com qual autenticação**.

---

## 7. Segurança de aplicações

**OWASP Top 10** (categorias da edição 2021; confirme a edição mais recente antes de citar numeração): Controle de acesso quebrado · Falhas criptográficas · Injeção · Design inseguro · Configuração incorreta de segurança · Componentes vulneráveis e desatualizados · Falhas de identificação e autenticação · Falhas de integridade de software e dados · Falhas de log e monitoramento · SSRF. Para APIs, consulte também o **OWASP API Security Top 10**; para requisitos verificáveis, o **OWASP ASVS**.

**Regras de ouro**
1. **Nunca confie na entrada**: valide no servidor (allowlist, tipo, tamanho, formato).
2. **Separe dado de instrução**: consultas parametrizadas, APIs sem shell, templates com auto-escape.
3. **Codifique a saída** conforme o contexto (HTML, atributo, JS, URL, SQL).
4. **Autorize em toda requisição**, por objeto; negar por padrão.
5. **Erros genéricos ao usuário**, detalhes só no log (sem segredos/dados pessoais no log).
6. **Cabeçalhos e cookies seguros:** `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options`, cookies `HttpOnly`/`Secure`/`SameSite`.
7. **Dependências sob controle:** inventário, atualização, lockfile, verificação de alcançabilidade.
8. **Segredos fora do código** e fora do histórico do Git.

**Ciclo de desenvolvimento seguro (SSDLC):** requisitos de segurança → modelagem de ameaças → codificação segura → **SAST** (código), **SCA** (dependências), **DAST** (app em execução), **IAST** → revisão de código → testes de segurança → hardening de pipeline → monitoramento em produção.

---

## 8. Sistemas operacionais e hardening

- **Patching** regular com priorização por risco; inventário de ativos e versões.
- **Superfície mínima:** remover/desativar serviços, pacotes e portas desnecessários.
- **Contas:** sem credenciais padrão; sem login direto de root/Administrator; sudo com registro; contas de serviço sem shell interativo.
- **Permissões (Linux):** menor privilégio em arquivos (evitar `777`), cuidado com SUID/SGID e `sudoers` permissivo; `umask` adequada.
- **Controle de acesso obrigatório:** SELinux/AppArmor ativos (não desabilitar "para testar").
- **Criptografia de disco** (LUKS/BitLocker) e *secure boot* onde aplicável.
- **Logs e auditoria:** `auditd`/journald, envio centralizado, relógio sincronizado (NTP).
- **Baselines:** **CIS Benchmarks** e guias STIG; automação de conformidade (Lynis, OpenSCAP) para verificar desvios.
- **Proteção de endpoint:** EDR/antimalware, bloqueio de execução não confiável quando viável.

---

## 9. Nuvem, containers e infraestrutura como código

- **Responsabilidade compartilhada:** o provedor protege a infraestrutura; **você** protege identidades, configuração, dados e aplicações.
- **Principais riscos na nuvem:** buckets/blobs públicos, IAM com `*`, chaves de acesso de longa duração vazadas, grupos de segurança `0.0.0.0/0`, falta de logging (CloudTrail e equivalentes), metadados de instância acessíveis via SSRF (use **IMDSv2**).
- **Boas práticas:** MFA no root/admin, papéis temporários (em vez de chaves estáticas), criptografia por padrão, segmentação por conta/projeto, logging centralizado, monitoramento de configuração (CSPM).
- **Containers:** imagem mínima e fixada por versão/digest, **usuário não-root**, sem `--privileged`, sistema de arquivos somente leitura quando possível, sem segredos na imagem, scan de imagens, política de rede entre pods.
- **Kubernetes (essencial):** RBAC restrito, **Pod Security Standards**, NetworkPolicies, segredos em cofre/KMS, API server não exposto, admission controllers.
- **IaC (Terraform, CloudFormation...):** scan de IaC no pipeline (ex.: checkov, tfsec/trivy), revisão por pares, estado do Terraform protegido (contém segredos).

---

## 10. Proteção de dados e privacidade

- **Classificação:** pública, interna, confidencial, restrita; o controle acompanha a classe.
- **Minimização:** colete e retenha só o necessário; elimine ao fim da finalidade.
- **LGPD (Brasil):** dados pessoais e sensíveis, bases legais, direitos do titular, encarregado (DPO), registro de operações, **notificação de incidentes** à ANPD e aos titulares quando houver risco relevante. Para decisões jurídicas, envolva o jurídico/DPO.
- **Proteção técnica:** criptografia em trânsito e em repouso, controle de acesso por necessidade de saber, mascaramento/tokenização/pseudonimização, DLP.
- **Backup e recuperação:** regra **3-2-1** (3 cópias, 2 mídias, 1 fora do local), com cópia **imutável/offline** contra ransomware, e **teste periódico de restauração** (backup não testado não é backup). Defina **RPO** (perda tolerável) e **RTO** (tempo de recuperação).

---

## 11. Gestão de vulnerabilidades

Ciclo: **inventariar → varrer/identificar → priorizar → corrigir/mitigar → validar → reportar/medir**.

**Priorize por:** exposição (internet?) · exploração conhecida (KEV/EPSS) · criticidade do ativo e dos dados · facilidade de exploração · controles compensatórios · esforço de correção. Agrupe achados com a mesma causa raiz.

**Métricas úteis:** tempo médio de correção (MTTR) por severidade, cobertura de varredura, idade das vulnerabilidades críticas abertas, % de ativos com patch em dia.

**Falsos positivos:** confirme com evidência antes de descartar (código inalcançável, versão com backport, entrada já validada).

---

## 12. Detecção e resposta a incidentes

**Logging que importa:** autenticação (sucesso/falha), mudanças de privilégio, acesso a dados sensíveis, alterações de configuração, tráfego de rede relevante (DNS, proxy, firewall), eventos de endpoint. Centralize (SIEM), proteja contra adulteração, sincronize relógios e mantenha retenção compatível com a investigação.

**IoC e IoA:** *Indicador de Comprometimento* (hash, IP, domínio) vs. *Indicador de Ataque* (comportamento). Comportamento dura mais que IOC.

**Ciclo de resposta (modelo clássico NIST SP 800-61):**
1. **Preparação:** plano, contatos, ferramentas, treinamento, backups.
2. **Detecção e análise:** confirmar, escopo, severidade, linha do tempo.
3. **Contenção, erradicação e recuperação:** isolar (preservando evidências), remover a causa, restaurar com segurança, monitorar reincidência.
4. **Pós-incidente:** lições aprendidas, correção da causa raiz, ajuste de controles.

**Regras práticas durante um incidente:** preserve evidências; **não apague nem reinicie sem necessidade** (perde memória volátil); registre cada ação com horário; isole a rede em vez de desligar quando possível; revogue credenciais potencialmente expostas; comunique por canal seguro; avalie **obrigações legais de notificação**. **Ordem de volatilidade** na coleta: memória → conexões/processos → disco → logs remotos/backups.

---

## 13. Frameworks, normas e referências

| Referência | Uso |
|------------|-----|
| **NIST CSF 2.0** | Estrutura de gestão de riscos: Governar, Identificar, Proteger, Detectar, Responder, Recuperar |
| **ISO/IEC 27001 / 27002** | Sistema de gestão de segurança da informação e controles |
| **CIS Controls / CIS Benchmarks** | Controles priorizados e baselines de configuração |
| **OWASP (Top 10, ASVS, Cheat Sheets)** | Segurança de aplicações e APIs |
| **MITRE ATT&CK / CWE / CAPEC** | Táticas e técnicas / fraquezas / padrões de ataque |
| **PCI DSS** | Dados de cartão |
| **LGPD / GDPR** | Privacidade e proteção de dados pessoais |
| **NIST SP 800-63B** | Diretrizes de autenticação e senhas |

---

## 14. Conceitos avançados (seleção enxuta)

1. **Zero Trust (arquitetura):** decisão de acesso por requisição com base em identidade, postura do dispositivo, contexto e risco; segmentação fina, *least privilege* contínuo, verificação explícita e **assumir violação** ("assume breach"). Não é um produto, é um modelo.

2. **Assume breach, purple team e detection engineering:** em vez de só prevenir, parta de que o invasor já entrou. *Red team* simula, *blue team* defende, *purple team* integra os dois para **validar e melhorar detecções** mapeadas ao ATT&CK. Regras de detecção versionadas e testadas (ex.: formato Sigma) tratadas como código.

3. **Priorização baseada em exploração real:** combine **CVSS** (gravidade) + **EPSS** (probabilidade) + **KEV** (exploração confirmada) + contexto (exposição, valor do ativo) + **alcançabilidade** (o código vulnerável é realmente chamado?). Reduz ruído e foca no que de fato importa.

4. **Cadeia de suprimentos de software:** riscos em dependências, builds e artefatos (pacotes maliciosos, *typosquatting*, comprometimento de CI). Controles: **SBOM** (inventário de componentes), assinatura e verificação de artefatos, builds reprodutíveis/herméticos, fixação de versões, pipeline com privilégio mínimo, frameworks como **SLSA**.

5. **Forward secrecy e agilidade criptográfica:** *Perfect Forward Secrecy* (ECDHE no TLS) garante que o vazamento da chave de longo prazo não decifra tráfego passado. **Agilidade criptográfica** é projetar para trocar algoritmos sem reescrever tudo, importante diante da transição para **criptografia pós-quântica** ("colher agora, decifrar depois" ameaça dados de longa vida).

6. **Memory safety e classes de falha de baixo nível:** estouro de buffer, *use-after-free* e afins explicam grande parte das falhas críticas históricas em C/C++. Mitigações: linguagens com segurança de memória (Rust, Go, etc.), ASLR, DEP/NX, *stack canaries*, CFI, *sandboxing*. Mitigação ≠ correção: reduz exploração, não elimina a falha.

7. **Ataques de canal lateral e temporais:** vazamento por tempo, cache ou consumo (ex.: comparar segredos com `==` revela tamanho do prefixo correto). Defesas: comparação em **tempo constante**, isolamento, bibliotecas criptográficas maduras.

8. **Identidade como perímetro (Active Directory/IdP e nuvem):** em ambientes modernos, comprometer **identidades e tokens** vale mais que invadir hosts. Pontos de atenção em alto nível: delegação e privilégios excessivos, contas de serviço com segredos fracos ou estáticos, tokens/sessões roubados (use *token binding*/sessões curtas/MFA resistente a phishing), consentimento excessivo em OAuth, caminhos de escalonamento em grafos de permissão (revisão de relações de confiança).

9. **Segurança de "confused deputy" e SSRF em nuvem:** um serviço com privilégio é induzido a agir em nome do atacante (ex.: SSRF alcançando o serviço de metadados e obtendo credenciais temporárias). Defesas: IMDSv2, allowlist de egress, papéis mínimos, validação de destino, *conditional access*.

10. **Quantificação de risco (FAIR) e apetite de risco:** traduz risco em valores (frequência de perda × magnitude), apoiando decisões de investimento e aceitação de risco com base em dados, não intuição.

---

## 15. Diretrizes para o agente ao aplicar este conhecimento

- **Ancore recomendações em princípios:** ao sugerir uma correção, cite o princípio que a justifica (menor privilégio, defesa em profundidade, negar por padrão), para o usuário aprender e generalizar.
- **Contexto antes de rigor:** o mesmo controle pesa diferente em lab e produção; peça o contexto (ver `vulnerability-analysis`, Etapa 1).
- **Calibre o nível:** iniciante → analogia e passos curtos; experiente → termos técnicos, ATT&CK/CWE, trade-offs.
- **Seja honesto com a incerteza:** versões, normas e algoritmos recomendados evoluem; sinalize quando algo deve ser confirmado na fonte oficial.
- **Defesa primeiro:** explique mecanismos em alto nível para justificar a proteção; não forneça exploits funcionais nem instruções para atacar terceiros.
- **Encaminhe para a skill certa:** diagnóstico e verificação de rede → `network-analysis`; análise de risco, classificação e correção → `vulnerability-analysis`.