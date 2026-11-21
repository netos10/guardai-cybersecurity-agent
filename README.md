#  GuardAI — Mentor Defensivo de Cibersegurança

O **GuardAI** é uma especialização do **DIO Agent** executado sobre o harness **Hermes**, projetado para atuar como um mentor técnico de cibersegurança defensiva. Em vez de entregar respostas soltas ou comandos sem contexto, o agente conduz uma investigação estruturada, entendendo o cenário, avaliando impacto, probabilidade e severidade.

---

## 🌐 Evolução Adicionada: `skills/network-analysis`

O GuardAI recebeu a skill **`skills/network-analysis`** para atuar como suporte prático em diagnóstico e reconhecimento de redes em ambientes de laboratório.

### Por que foi adicionada?
Durante estudos e testes em ambientes controlados (como VMs Ubuntu Server e Kali Linux), muitas vezes o estudante ou analista sabe o que deseja verificar na rede, mas precisa de apoio para traduzir esse objetivo na sintaxe correta das ferramentas de comando sem recorrer a automações ofensivas ou comandos copiados sem entendimento.

### Função da Skill
A `skills/network-analysis` funciona como um tradutor técnico e educativo: o usuário descreve em linguagem natural o objetivo de verificação de rede, e o agente:
1. **Valida o Escopo e Autorização:** Confirma se o alvo pertence a um ambiente próprio/de laboratório e solicita dados essenciais (IP, sub-rede ou interface de rede).
2. **Monta o Comando Adequado:** Constrói o comando exato utilizando utilitários padrão do Kali Linux e ambientes Linux.
3. **Explica Cada Parâmetro:** Detalha o papel de cada *flag* e argumento utilizado, promovendo aprendizado real.
4. **Conecta com a Análise de Risco:** Caso a saída do diagnóstico revele portas abertas indevidamente ou serviços expostos, o agente encaminha a investigação para a skill principal (`vulnerability-analysis`).

### Principais Habilidades Cobertas
* **Descoberta de Hosts e Portas:** Montagem de comandos para varredura de conectividade, identificação de portas abertas e detecção de serviços/versões em redes de laboratório (ex.: `nmap`, `ping`, `netcat`).
* **Diagnóstico de Conectividade e Rotas:** Auxílio na verificação de interfaces, tabelas de roteamento, resolução DNS e latência (ex.: `ip`, `ss`, `traceroute`, `dig`, `whois`).
* **Inspeção Básica de Tráfego e Requisições:** Apoio na captura e análise de pacotes ou cabeçalhos HTTP para entender o comportamento da comunicação em rede (ex.: `tcpdump`, `curl`).

---

##  Como Executar o GuardAI

1. Clone este repositório e acesse a pasta raiz do projeto no terminal:
   ```bash
   git clone https://github.com/netos10/GuardAI.git
