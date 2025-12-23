## 🏗️ Arquitetura da Aplicação (AWS)

A arquitetura do **Beer Brewery Stock Manager** segue o padrão de **Três Camadas (3-Tier Architecture)**, focada em alta disponibilidade, escalabilidade e segurança. A aplicação é distribuída em múltiplas **Availability Zones (AZs)** para garantir tolerância a falhas.

### 📌 Visão Geral da Arquitetura

O fluxo de acesso à aplicação ocorre da seguinte forma:

* O usuário acessa a aplicação via navegador (Web Client).
* O domínio é resolvido pelo **Amazon Route 53**.
* As requisições passam pelo **AWS WAF** (proteção na borda).
* O conteúdo é acelerado pelo **Amazon CloudFront (CDN)**.
* O tráfego chega ao **Application Load Balancer (ALB)** público.
* O ALB distribui o tráfego para **instâncias EC2** em subnets privadas (Auto Scaling Groups).
* A aplicação consome o banco de dados **Amazon RDS (PostgreSQL)** em **Multi-AZ**.
* **Amazon ElastiCache (Redis)** e **Amazon EFS** garantem performance e armazenamento compartilhado.
* O acesso externo das subnets privadas ocorre via **NAT Gateways**.

### 🛡️ Benefícios Garantidos

* **Alta Disponibilidade:** Balanceamento de carga em múltiplas zonas.
* **Elasticidade:** Escalabilidade horizontal automática.
* **Segurança:** Isolamento de rede em subnets públicas, privadas e de dados.
* **Proteção:** Camada de borda contra ataques maliciosos.

### 🗺️ Visualização do Fluxo
👇
<p>
  <a href="https://github.com/study-group-anapedra/brewery-stock-manager-infra/blob/main/doc/adicionar-diagrama-aws-arquitetura.png">
    🔗 Clique aqui para abrir o Diagrama de Arquitetura AWS
  </a>
</p>


### ⚠️ Dificuldades e Aprendizados

* **Redes e Conectividade:** Identifiquei que a ausência de tabelas de rotas e associações explícitas causava rollbacks, pois as instâncias não baixavam dependências. A solução foi configurar corretamente o **Internet Gateway** e o **NAT Gateway**.
* **Ciclo de Vida do CloudFormation:** Aprendi que tentar um novo deploy enquanto stacks anteriores estão em `DELETE_IN_PROGRESS` gera conflitos. É essencial aguardar o estado `DELETE_COMPLETE` para garantir um ambiente limpo.

---

## 🧑‍💻 Autora

**Ana Santana**
📧 **Email:** anapedra.mil@gmail.com
💼 **LinkedIn:** [anasantana](https://www.linkedin.com/in/anasantana)