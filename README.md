# AWS-CloudFormation-2
Infraestrutura como Código (IaC) avançada usando AWS CloudFormation para simular ambiente de produção.

🧠 Objetivo

Automatizar a criação de:

Rede (VPC + Subnets)
Balanceamento de carga
Escalabilidade automática
Instâncias EC2 em ambiente privado
⚙️ Tecnologias AWS usadas
AWS CloudFormation
Amazon EC2
VPC
ALB (Application Load Balancer)
Auto Scaling Group
Security Groups
Internet Gateway
🏗️ Benefícios da arquitetura
Alta disponibilidade
Escalabilidade automática
Segurança em camadas
Separação público/privado
Infraestrutura reproduzível
🚀 Deploy do stack
aws cloudformation create-stack \
  --stack-name infra-avancada \
  --template-body file://template.yaml
📊 Resultado esperado
URL pública do Load Balancer
EC2s escalando automaticamente
Acesso HTTP funcional via ALB
🔐 Boas práticas aplicadas
Infraestrutura declarativa
Segurança com SGs restritivos
Subnets privadas para EC2
Automação completa
Reutilização de templates
