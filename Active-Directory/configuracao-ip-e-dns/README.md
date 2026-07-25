# Lab: Configuracao de IP Estatico e DNS Local para Active Directory

## Objetivo do Laboratorio
Este laboratorio documenta a preparacao de infraestrutura de rede necessaria para a promocao de um Controlador de Dominio (DC). O foco foi garantir que o servidor possua um endereco IP fixo e aponte para si mesmo como servidor DNS primario, evitando falhas de resolucao de nomes no Active Directory.

---

## Detalhes do Ambiente
* Sistema Operacional: Windows Server (ou Ubuntu Server, caso tenha feito no Linux)
* Funcao do Servidor: Futuro Controlador de Dominio (Active Directory Domain Services)
* Interface de Rede: Ethernet (adaptador principal)

---

## Passo a Passo Executado

### 1. Planejamento de Rede
Antes de alterar as configuracoes, foi definido o seguinte escopo de rede para o servidor:
* Endereco IP Fixo: 192.168.1.10 (substitua pelo IP que voce usou)
* Mascara de Sub-rede: 255.255.255.0
* Gateway Padrao: 192.168.1.1 (o IP do seu roteador/firewall)
* Servidor DNS Preferencial: 127.0.0.1 (Loopback) ou o proprio IP 192.168.1.10

### 2. Aplicacao das Configuracoes
Nota: Caso tenha feito no Windows Server, a configuracao foi realizada atraves do Painel de Controle (ncpa.cpl) nas propriedades do protocolo IPv4. Caso tenha sido no Ubuntu Server, a alteracao foi feita via arquivo do Netplan (/etc/netplan/).

Os parametros foram validados e salvos para garantir persistencia apos a reinicializacao do sistema.

### 3. Testes de Conectividade e Validacao
Para confirmar que o servidor continuava operando corretamente na rede e respondendo localmente, foram executados os seguintes comandos no terminal:

```bash
# Validar se as novas configuracoes de IP foram aplicadas
ipconfig /all   # (No Windows) ou 'ip a' (No Linux)

# Testar conectividade com o gateway padrao
ping 192.168.1.1

# Testar a resolucao de nomes local externa
ping google.com
