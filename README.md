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

Arquitetura do cenário (produção simulada)
Componentes implementados:
VPC dedicada
2 Subnets públicas
2 Subnets privadas
Internet Gateway
Route Tables
Application Load Balancer (ALB)
Target Group
Auto Scaling Group (ASG)
Launch Template
EC2 em subnet privada
Security Groups segmentados
🏗️ Arquitetura lógica
Internet
   │
   ▼
ALB (Public Subnet)
   │
   ▼
Target Group
   │
   ▼
Auto Scaling Group (Private Subnet)
   │
   ▼
EC2 Instances
📜 template.yaml (CloudFormation avançado)
🔷 1. VPC e rede
AWSTemplateFormatVersion: '2010-09-09'
Description: Infraestrutura avançada com VPC, ALB e ASG

Resources:

  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
🔷 2. Subnets públicas e privadas
  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      AvailabilityZone: !Select [0, !GetAZs '']

  PrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.2.0/24
      AvailabilityZone: !Select [0, !GetAZs '']
🔷 3. Internet Gateway
  InternetGateway:
    Type: AWS::EC2::InternetGateway

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway
🔷 4. Route Table pública
  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC

  PublicRoute:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway
🔷 5. Security Groups
  ALBSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: ALB SG
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  InstanceSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: EC2 SG
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          SourceSecurityGroupId: !Ref ALBSecurityGroup
🔷 6. Launch Template
  LaunchTemplate:
    Type: AWS::EC2::LaunchTemplate
    Properties:
      LaunchTemplateData:
        ImageId: ami-0c02fb55956c7d316
        InstanceType: t2.micro
        SecurityGroupIds:
          - !Ref InstanceSecurityGroup
        UserData:
          Fn::Base64: !Sub |
            #!/bin/bash
            yum update -y
            yum install -y httpd
            systemctl start httpd
            echo "Infra AWS CloudFormation OK" > /var/www/html/index.html
🔷 7. Target Group
  TargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Port: 80
      Protocol: HTTP
      VpcId: !Ref VPC
      HealthCheckPath: /
🔷 8. Load Balancer
  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internet-facing
      Subnets:
        - !Ref PublicSubnet1
      SecurityGroups:
        - !Ref ALBSecurityGroup
🔷 9. Auto Scaling Group
  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      VPCZoneIdentifier:
        - !Ref PrivateSubnet1
      MinSize: 1
      MaxSize: 3
      DesiredCapacity: 2
      LaunchTemplate:
        LaunchTemplateId: !Ref LaunchTemplate
        Version: !GetAtt LaunchTemplate.LatestVersionNumber
      TargetGroupARNs:
        - !Ref TargetGroup
📤 Outputs
Outputs:
  LoadBalancerURL:
    Value: !GetAtt ApplicationLoadBalancer.DNSName
🔐 Boas práticas aplicadas
Infraestrutura declarativa
Segurança com SGs restritivos
Subnets privadas para EC2
Automação completa
Reutilização de templates
