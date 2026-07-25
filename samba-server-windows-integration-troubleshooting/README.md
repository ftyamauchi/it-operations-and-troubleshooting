samba-server-windows-integration-troubleshooting
Projeto desenvolvido como laboratório prático de suporte técnico para simular a implementação de um servidor de arquivos Linux (Samba) integrado a estações Windows e a resolução de incidentes de rede e autenticação.

O projeto demonstra como configurar o compartilhamento de arquivos seguro, restringir acessos anônimos e solucionar falhas comuns de helpdesk, como travamentos de cache e alteração de IP via DHCP.

Objetivos
Provisionar um servidor de arquivos Samba em ambiente Ubuntu Server.

Configurar permissões de leitura e escrita em diretórios compartilhados.

Implementar autenticação obrigatória por usuário, bloqueando acessos anônimos.

Realizar o troubleshooting de incidentes reais de conectividade no ecossistema Windows.

Tecnologias Utilizadas
Ubuntu Server 24.04 LTS

Oracle VirtualBox

Windows 10 / Windows 11

Samba (Protocolo SMB/CIFS)

UFW (Uncomplicated Firewall)

Estrutura do Projeto
samba-server-windows-integration-troubleshooting/
│
├── /samba/
│   └── publico/           # Diretório compartilhado na rede
│
├── /etc/samba/
│   └── smb.conf           # Arquivo de configuração do serviço
│
└── README.md

Preparação do Ambiente
Sistema Operacional do Servidor
O ambiente foi desenvolvido utilizando:

Ubuntu Server 24.04 LTS (Instalado em Máquina Virtual)

Na configuração da placa de rede da máquina virtual no VirtualBox, altere o modo de conexão para:

Placa em modo Bridge

Isso garante que o servidor e o computador físico fiquem na mesma rede local, recebendo IPs da mesma faixa.

Configuração do Firewall
No terminal do Ubuntu Server, libere as portas necessárias para o tráfego do protocolo SMB (139 e 445):

sudo ufw allow samba

Verifique o status do firewall para confirmar a liberação:

sudo ufw status

Configuração do Samba
Crie o diretório que será compartilhado:

sudo mkdir -p /samba/publico

Ajuste as permissões do diretório no sistema de arquivos:

sudo chmod -R 0777 /samba/publico

Abra o arquivo de configuração principal:

sudo nano /etc/samba/smb.conf

Adicione o seguinte bloco de configuração ao final do arquivo para criar o compartilhamento e bloquear acessos anônimos:

[publico]
comment = Compartilhamento Publico Homelab
path = /samba/publico
browseable = yes
read only = no
guest ok = no
valid users = suporte
force create mode = 0777
force directory mode = 0777

Salve e feche o arquivo.

Criação de Usuário e Credenciais
Crie a senha de rede para o usuário definido na configuração:

sudo smbpasswd -a suporte

Digite e confirme a senha.

Reinicie o serviço do Samba para aplicar as novas configurações:

sudo systemctl restart smbd

Verifique se o serviço subiu corretamente:

sudo systemctl status smbd

Troubleshooting de Incidentes no Windows
Durante os testes de acesso a partir da estação Windows, documentou-se a resolução de três problemas comuns de suporte técnico:

1. Bloqueio de Acesso Anônimo (Guest)
Sintoma: O Windows rejeitava a conexão de forma imediata ao tentar acessar a pasta de rede.

Causa: Versões modernas do Windows bloqueiam conexões anônimas sem autenticação por padrão por motivos de segurança.

Solução: O arquivo smb.conf foi alterado para guest ok = no, exigindo credenciais válidas.

2. Alteração de IP via DHCP
Sintoma: O acesso via SSH e o mapeamento de arquivos pararam de responder com erro de conexão recusada.

Causa: O servidor foi reiniciado e o roteador alterou o IP dinâmico da máquina virtual.

Solução: Identificação do novo IP através do terminal do Linux com o comando ip a e atualização do apontamento na estação cliente.

3. Timeout e Conexão Travada ("Trabalhando nisso...")
Sintoma: O Explorador de Arquivos do Windows travava tentando carregar a pasta infinitamente.

Causa: O Windows armazenou em cache as credenciais e o IP antigo da máquina, gerando conflito de sessão.

Solução: Abertura do Prompt de Comando (CMD) do Windows físico para limpar conexões residuais e forçar o mapeamento:

net use * /delete /y

Em seguida, o mapeamento foi forçado informando o novo IP e as credenciais válidas:

net use \IP_DO_SERVIDOR\publico /user:suporte SENHA_DO_USUARIO

Validação do Acesso
No Windows, abra a janela Executar (Windows + R) e digite o caminho do servidor:

\IP_DO_SERVIDOR

A pasta publico abrirá sem travamentos e permitirá a criação de arquivos normalmente.

Conceitos Demonstrados
Administração de servidores Linux.

Compartilhamento de arquivos em rede híbrida (Linux e Windows).

Configuração de segurança de rede local com UFW.

Gerenciamento de credenciais locais do Samba.

Resolução de incidentes em sistemas operacionais clientes via CMD do Windows.

Autor
Fabio Yamauchi
