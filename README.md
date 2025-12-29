# 🍺  Brewery Stock Manager

Gerenciador de estoque de cervejarias com foco em **alta disponibilidade**, **escalabilidade** e **segurança** na AWS.
</p>
<p>
  <a href="https://github.com/study-group-anapedra/brewery-stock-manager">
    🔗 Repositori do Projeto Brewery Stock Manager
  </a>
</p>


---

## 🏗️ Arquitetura da Aplicação (AWS)

A arquitetura do **Brewery Stock Manager** segue o padrão de **Três Camadas (3-Tier Architecture)**, distribuída em múltiplas **Availability Zones (AZs)** para garantir tolerância a falhas.

### 📌 Visão Geral da Arquitetura

O fluxo de acesso à aplicação ocorre da seguinte forma:

1. O usuário acessa a aplicação via navegador (Web Client).
2. O domínio é resolvido pelo **Amazon Route 53**.
3. As requisições passam pelo **AWS WAF** (proteção na borda).
4. O conteúdo é acelerado pelo **Amazon CloudFront (CDN)**.
5. O tráfego chega ao **Application Load Balancer (ALB)** público.
6. O ALB distribui o tráfego para **instâncias EC2** em subnets privadas (Auto Scaling Groups).
7. A aplicação consome o banco de dados **Amazon RDS (PostgreSQL)** em **Multi-AZ**.
8. **Amazon ElastiCache (Redis)** e **Amazon EFS** garantem performance e armazenamento compartilhado.
9. O acesso externo das subnets privadas ocorre via **NAT Gateways**.

</p>
<p>
  <a href="https://github.com/study-group-anapedra/brewery-stock-manager-infra/blob/main/doc/diagrama-aws-arquitetura.png">
    🔗 Diagrama do Fluxo da Arquitetura AWS
  </a>
</p>

</p>
<p>
  <a href="https://github.com/study-group-anapedra/brewery-stock-manager-infra/blob/main/doc/AWS_CloudFormation_Ferramenta.pdf">
    🔗 Sobre AWS CloudFormation
  </a>
</p>

---

## Dificuldades e Aprendizados

1. **Redes e Conectividade:**  
   Ausência de tabelas de rotas e associações explícitas causava rollbacks, pois as instâncias não baixavam dependências.  
   **Solução:** Configuração correta de **Internet Gateway** e **NAT Gateway**.

2. **Desenho de Arquitetura:**  
   Inicialmente tive dificuldade em criar diagramas completos. Resolvi isso replicando um modelo AWS que atendesse às minhas necessidades e redesenhando conforme aprendizado. Ainda não é perfeito, mas suficiente para começar.

3. **Infraestrutura como Código (IaC):**  
   Organização dos templates YAML, erros de sintaxe e conflitos com extensões foram superados com pesquisas e apoio de IAs, garantindo práticas de mercado.

4. **Deploy e Automatização:**  
    Durante os primeiros testes de deploy, enfrentei muitos erros pelo GitHub Actions, sem saber se o problema vinha do workflow, do código ou do CloudFormation.  
    **Soluções testadas:**

   - Deploy pelo GitHub Actions → diversos rollbacks e falhas misteriosas.
   - Deploy direto pelo CloudFormation → maior controle, mas ainda dependia de ajustes manuais.
   - Deploy local pelo VS Code com extensões do CloudFormation → finalmente consegui provisionar todas as stacks corretamente.

   **Observação:** Durante a criação do RDS, percebi que a AWS bloqueava a criação da instância PostgreSQL versão 15, embora estivesse disponível entre 15 e 15.7. Alterando para 15.7, a stack foi provisionada com sucesso.


   ## 💰 Estimativa de Custos e Relato de Experiência

Esta seção documenta a viabilidade financeira e o processo de tomada de decisão para provisionar esta arquitetura real para fins de aprendizado e portfólio.

### 📊 Projeção de Gastos (Fase de Provisão)
A estratégia consiste em provisionar a infraestrutura completa para validar a arquitetura, documentar com vídeos/fotos e, **imediatamente após a validação, destruir as stacks** para evitar cobranças recorrentes.

| Item | Estimativa Base | Margem de Erro | Total Estimado |
| :--- | :--- | :--- | :--- |
| **Provisão AWS** (EC2, RDS, NAT Gateway, etc) | R$ 20,00 | R$ 10,00 | R$ 30,00 |
| **Domínio** (Registro Anual Route 53) | R$ 80,00 | R$ 0,00 | R$ 80,00 |
| **TOTAL GERAL** | | | **R$ 110,00** |

> [!IMPORTANT]
> **Tomada de Decisão:** Para manter o controle rigoroso, configurei alertas de orçamento (Budgets) que me notificam via e-mail assim que o gasto atinge patamares críticos.


</p>
<p>
  <a href="https://github.com/study-group-anapedra/brewery-stock-manager-infra/blob/main/doc/budget_notification.png">
    🔗 Registro dos alertas configurados para monitoramento de custos em tempo real
  </a>
</p>

### 📝 Relato de Avanços e Superação
O processo de construção desta infraestrutura foi marcado por um ciclo de **Tentativa → Erro → Análise → Resolução**:

* **Dificuldades Encontradas:** Erros de sintaxe em Nested Stacks e a propagação lenta do CloudFront que travava o deploy do DNS.
* **Resolução:** A decisão técnica de **desacoplar as stacks** permitiu um deploy previsível. O uso de vídeos capturados serviu não apenas para documentação, mas para revisar onde os processos falhavam.
* **Superação:** Sair do provisionamento manual ("na unha") para uma automação onde posso destruir e recriar todo o ambiente em minutos, garantindo que o aprendizado seja prático e financeiramente sustentável.
  

---

  <a href="https://github.com/study-group-anapedra/brewery-stock-manager-infra/blob/main/doc/error-deploy-local-bash.png">
    🔗 Erro deply local
  </a>
</p>

<p>
  <a href="https://github.com/study-group-anapedra/brewery-stock-manager-infra/blob/main/doc/local-deploy-success-bash.png">
    🔗 Sucesso deply local
  </a>
</p>


5. **Familiarização com CloudFormation e IaC:**  
   Após o sucesso do deploy local, comecei a focar novamente na automação via GitHub Actions. Agora já estava mais familiarizada com a lógica de Nested Stacks, parâmetros, outputs e a forma de provisionar infraestrutura com código.  
   **Aprendizados:**

   - Erros são inevitáveis, mas cada falha reforça o entendimento da arquitetura e serviços AWS.
   - Separar stacks críticas (CloudFront e Route53) evita rollbacks e instabilidades.
   - Alinhar domínio, certificado e DNS é essencial para que a automação funcione corretamente.

6. **Desafios com CloudFront + Route53:**  
   Inicialmente, tentei criar o CloudFront e o registro DNS na mesma stack, causando falhas devido à propagação global do CloudFront.  
   **Solução:**

   - Desacoplar a stack de DNS da stack de CloudFront.
   - Criar o CloudFront primeiro, aguardar propagação global e depois criar o registro DNS em uma stack separada.
   - Evita rollbacks em cascata e garante deploy previsível.

  

<p>
  <a href="https://drive.google.com/file/d/1IlaBm_M72LY16YJ62Q7Dca7yxch6scxH/view?usp=sharing">
    🔗 Sucesso no Deploy via GitHub Actions após desacoplamento do CloudFront e Route53
  </a>
</p>

7. **Gerenciamento de erros e aprendizado contínuo:**  
   Cada erro, seja de sintaxe YAML, parâmetros, versão de serviço ou acoplamento de stacks, se tornou aprendizado:
   - Debug detalhado no VS Code.
   - Pesquisas sobre boas práticas de mercado.
   - Apoio das IAs para validar soluções.
   - Criação de pipelines estruturadas, limpas e fáceis de manter.

---

## ⚙️ Preparação do Ambiente AWS

### 1️⃣ Criar Conta e Usuário Administrador

1. Criar uma **conta nova na AWS**.
2. Criar um **usuário Admin IAM**.
3. Criar um **grupo de administradores** e adicionar o usuário nele.
4. Conceder **permissões administrativas completas** ao grupo (`AdministratorAccess`).

### 2️⃣ Configurar Segurança

- Ativar **MFA** para a conta root e o usuário admin.
- Garantir que todos os acessos críticos estejam protegidos.

### 3️⃣ Configurar Alertas de Custos

- Acessar **Billing and Cost Management** no console AWS.
- Criar **5 alertas de orçamento** (Budget Alerts), ex: `Alerta-R10`, `Alerta-R20`, `Alerta-R5`, `Alerta-R15`, `Alerta-Zero-Gasto`.

### 4️⃣ Configurar Domínio e DNS

- Criar o **domínio na Route 53**.
- Configurar os **registros DNS** conforme necessário para aplicação e CloudFront.

---

## 📚 Insights de Aprendizado

- Experiência prática com IaC (CloudFormation) e automação via GitHub Actions.
- Entendimento profundo de provisionamento de recursos complexos na AWS.
- Lições sobre **rollback**, **propagação de DNS**, **CloudFront**, e **Multi-AZ RDS**.
- Evolução do trabalho manual no console para deploys automatizados confiáveis.
- Aprendi que separação de workflows críticos (ex: CloudFront / Route53) é essencial para deploys previsíveis.
- Cada erro foi transformado em aprendizado, consolidando conhecimento de infraestrutura complexa.

---

## ⚙️ Vantagens do IaC (CloudFormation) sobre Provisionamento Manual

Durante o aprendizado, percebi que **provisionar recursos diretamente na AWS “na unha”** é limitado e propenso a erros, especialmente quando precisamos criar, destruir e recriar a infraestrutura diversas vezes.  

Com **CloudFormation** e Infraestrutura como Código (IaC), conseguimos:  

- **Automatizar todo o processo:** Criação, atualização e destruição de stacks com um único comando ou workflow.  
- **Repetibilidade:** Podemos recriar a mesma infraestrutura quantas vezes forem necessárias, sem inconsistências.  
- **Documentação automática:** O código serve como documentação viva da arquitetura e das dependências.  
- **Controle de versões:** Podemos versionar templates YAML/JSON no GitHub, facilitando auditoria e rollback.  
- **Segurança e boas práticas:** Permite definir políticas de segurança, grupos de IAM, subnets e recursos críticos de forma padronizada, evitando esquecimentos ou erros manuais.  

Durante o curso e os testes, tive que **criar e destruir stacks diversas vezes** para aprender como lidar com rollback, dependências entre recursos, propagação global de serviços como CloudFront, e configuração de RDS Multi-AZ. Essa prática me permitiu internalizar conceitos e entender **quando a AWS precisa de intervenção manual** e quando o IaC resolve tudo de forma previsível, o que me estimula a avançar no apredizado.  

<p>
  <a href="https://drive.google.com/file/d/1G0nQ32OuQPoWKUMldEonb2TvTJezBKG3/view?usp=sharing">
    🔗 Destruindo stacks
  </a>
</p>



## 🧑‍💻 Autora

**Ana Santana**  
📧 **Email:** anapedra.mil@gmail.com  
💼 **LinkedIn:** [anasantana](https://www.linkedin.com/in/anasantana)
