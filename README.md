## 🏗️ Arquitetura da Aplicação (AWS)

A arquitetura do **Beer Brewery Stock Manager** foi projetada seguindo o padrão de **Três Camadas (3-Tier Architecture)**, com foco em **alta disponibilidade**, **escalabilidade**, **segurança** e **boas práticas em nuvem**.

A aplicação está distribuída em **múltiplas Availability Zones**, garantindo tolerância a falhas e continuidade do serviço.

### 📌 Visão Geral da Arquitetura

O fluxo de acesso à aplicação ocorre da seguinte forma:

1. O usuário acessa a aplicação via navegador (Web Client)
2. O domínio é resolvido pelo **Amazon Route 53**
3. O conteúdo é distribuído pelo **Amazon CloudFront (CDN)**
4. As requisições passam por uma camada de proteção com **AWS WAF**
5. O tráfego é direcionado para um **Application Load Balancer (ALB)**
6. O ALB distribui as requisições entre **instâncias EC2**, organizadas em **Auto Scaling Groups**
7. A aplicação acessa o banco de dados **Amazon RDS (PostgreSQL)** em configuração **Multi-AZ**
8. Serviços auxiliares como **Amazon ElastiCache (Redis)** e **Amazon EFS** são utilizados para cache e armazenamento compartilhado
9. O acesso à internet a partir de subnets privadas é realizado por meio de **NAT Gateways**

Essa arquitetura garante:
- Balanceamento de carga automático
- Escalabilidade horizontal
- Isolamento de rede com subnets públicas e privadas

### ⚠️ Dificuldades e Aprendizados no Processo

- Durante o processo de deploy da infraestrutura, um dos rollbacks do CloudFormation ocorreu quando a VPC e as subnets foram criadas corretamente; porém não foram criadas tabelas de rotas nem associações explícitas; as subnets públicas não possuíam rota `0.0.0.0/0` para o Internet Gateway; e as subnets privadas não possuíam saída para a internet; com isso as instâncias EC2 não conseguiram baixar dependências nem responder aos health checks do Auto Scaling Group; resultando no rollback automático da stack; o problema foi resolvido com a criação de Route Table pública e privada; associação correta às subnets; configuração de rota para o Internet Gateway; e uso de NAT Gateway para garantir conectividade das subnets privadas; assegurando estabilidade e sucesso no deploy; passar por essa dificuldade foi muito importante pois permitiu internalizar melhor os conceitos; revisar a infraestrutura com mais cautela; e prevenir ou resolver situações similares no futuro.

- Durante o processo de testes de deploy foram acionadas novas execuções enquanto o CloudFormation ainda realizava rollback de stacks anteriores; como os recursos ainda não estavam totalmente deletados; o serviço entrou em ciclos de criação e remoção automática; gerando sucessivos rollbacks; a análise da aba **Resources** mostrou que alguns stacks ainda estavam em `DELETE_IN_PROGRESS`; o aprendizado foi entender que um novo deploy só deve ser iniciado quando todos os recursos estiverem em `DELETE_COMPLETE`; garantindo ambiente limpo; evitando conflitos de dependência; e assegurando previsibilidade no deploy da infraestrutura.

## 🧑‍💻 Autora

**Ana Santana**

📧 **Email:** anapedra.mil@gmail.com  
📱 **WhatsApp:** +55 31 99750-2148  
💼 **LinkedIn:** https://www.linkedin.com/in/anasantana