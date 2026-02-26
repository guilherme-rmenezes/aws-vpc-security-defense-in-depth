# AWS VPC Security & Defense in Depth Lab

Este projeto demonstra como controlar o acesso a instâncias Amazon EC2 utilizando camadas de segurança em uma VPC, configurando Grupos de Segurança e Network ACLs.

## Objetivo

Entender como as permissões e bloqueios de tráfego são aplicados na AWS usando:
* **Security Groups** (stateful/nível de instância).
* **Network ACLs** (stateless/nível de sub-rede).
* **Princípio de menor privilégio** para tráfego HTTP e SSH.
* **Acesso seguro** via Systems Manager (SSM) e Bastion Host.

## Ambiente

O laboratório consiste em:
* **1 LabVPC** (CIDR 10.0.0.0/16).
* **2 Sub-redes Públicas** (hospedando ProxyServer1 e ProxyServer2).
* **1 Sub-rede Privada** (hospedando AppServer).
* **Gateways**: Internet Gateway para acesso externo e NAT Gateway para saída da rede privada.

## Arquitetura e fluxo de permissões

### Arquitetura Inicial do ambiente

O diagrama abaixo mostra a rede configurada com os servidores proxy servindo de ponte para o servidor de aplicação.

![Arquitetura Inicial](img/start-arch.png)

### Restrição de acesso ao AppServer

Nesta etapa, o acesso ao **AppServerSG** foi restringido para aceitar conexões apenas quando originadas de instâncias associadas ao **ProxySG**, eliminando a exposição para IPs públicos.

### Controle via Network ACL

Implementação de regras de **Deny** e **Allow** na ACL de rede para validar a precedência de regras numeradas (ex: regra 98 permitindo tráfego antes da regra 99 bloqueá-lo).

## Gerenciamento e Conectividade Segura

### Acesso via Bastion Host (SSH Agent Forwarding)

Configuração de um servidor de salto para acessar a instância privada na porta 22 sem a necessidade de armazenar chaves privadas (`.pem`) no host intermediário.

![SSH Flow](img/ssh-diagram.png)

### Acesso via AWS Systems Manager (Session Manager)

Utilização do **SSM Session Manager** para gerenciar a instância privada via shell diretamente pelo console AWS, sem a necessidade de abrir portas de entrada (SSH/22) ou manter Bastion Hosts ativos.

![SSM Access](img/ssm.png)

## Evidências de Configuração

### Tabelas de Roteamento e Sub-redes
Visualização das rotas configuradas para o Gateway NAT e Gateway de Internet.
![Subnets](img/subnets-view.png)
![Public Route](img/public-route.png)
![Private Route](img/private-route.png)

### Testes de Bloqueio (NACL)
Validação do tráfego sendo permitido ou bloqueado conforme a precedência das regras.
![Acesso Permitido](img/not-blocked.png)
![Acesso Bloqueado](img/blocked.png)
