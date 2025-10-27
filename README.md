# simulando-ataque-bruteforce-medusa

## simulação de ataque brute force usando a ferramenta Medusa no Kali Linux em ambiente virtualizado. a máquina alvo é um Metasploitable 2. ambas máquinas virtualizadas usando o VirtualBox com configuração de rede host-only

no terminal da máquina Metasploitable, descobrimos o endereço IP usando o comando *ip addr*
<img width="729" height="402" alt="image" src="https://github.com/user-attachments/assets/eb3aab5d-dead-4bb2-873e-27761546e924" />

no Kali, usamos o comando *ping* com o IP do alvo para certificar que temos conexão
<img width="858" height="559" alt="image" src="https://github.com/user-attachments/assets/a39aa207-303b-4bed-9be7-af7773524cf1" />
### perfeito! tendo conexão com nossa máquina alvo, podemos buscar por vulnerabilidades.

usando a ferramenta nmap, encontramos diversas portas abertas
<img width="859" height="559" alt="image" src="https://github.com/user-attachments/assets/b371117c-41c5-44ce-be1c-50cd002931ad" />

## Estabelecendo conexão FTP com a máquina alvo

ao tentar uma conexão via FTP, usuário e senha são solicitados.
<img width="854" height="559" alt="image" src="https://github.com/user-attachments/assets/5207873e-a3b4-49b0-b796-2966d66c5a8c" />

sem saber essas credenciais, não é possível estabelecer a conexão com o alvo via FTP.
é aqui que iremos tentar nosso ataque de brute force. mas para isso, precisamos disponibilizar duas listas contendo nomes de usuário e senhas comuns para serem testadas pelo Medusa.

<img width="859" height="560" alt="image" src="https://github.com/user-attachments/assets/80ec6641-4f62-4b73-b730-f2902a520ad9" />

criamos dois arquivos: **users.txt** e **pass.txt**, o primeiro contendo uma lista de usuários e o segundo uma lista de senhas. esses termos serão usados pelo Medusa para tentar encontrar combinações de credenciais que permitam acesso à máquina alvo via FTP.

## Brute Force no FTP utilizando o Medusa

utilizando a ferramenta Medusa, especificamos:

**-h** o IP do alvo (host)

**-U** o arquivo contendo os nomes de usuário

**-P** o arquivo contendo as senhas

**-M** o módulo/protocolo que será atacado

**-t** quantidade de processos/tentativas realizadas simultaneamente
<img width="856" height="771" alt="image" src="https://github.com/user-attachments/assets/36c041d3-dd93-4769-a67c-e985f91777b8" />

### a mensagem de [SUCCESS] informa que houve uma tentativa bem sucedida. utilizando o usuário *msfadmin* e a senha *msfadmin*

utilizando as credenciais encontradas pelo Medusa, a conexão via FTP com a máquina alvo é um sucesso!
<img width="580" height="441" alt="image" src="https://github.com/user-attachments/assets/cdf65f43-0f9a-483c-9957-11738c98cf11" />

### a partir desse ponto, um atacante poderia fazer o download de arquivos, upload de scripts, alterar conteúdos, usar o host como ponto de entrada para alcançar outras máquinas da rede, procurar credenciais e mecanismos de persistência e, se tiver permissões de escrita em áreas críticas, corromper ou criptografar dados. isso apenas ilustra a necessidade de proteger os sistemas, seja através do uso de senhas fortes, autenticação por chave, limitação de tentativas , aplicação de bloqueio/alerta contra brute-force ou da migração para SFTP/FTPS.

## Brute Force em formulário de login de aplicação WEB

vamos agora realizar um Brute Force na aplicação web.

o site abaixo está hospedado em nossa máquina alvo, sendo acessado pelo Kali usando o Firefox

<img width="939" height="628" alt="image" src="https://github.com/user-attachments/assets/60a0f0e4-0541-44da-80d4-67b01ff93d65" />

 

executando uma tentativa de login com credenciais deliberadamente incorretas, podemos inspecionar a página e encontrar os parâmetros de requisição para login no site.

<img width="1278" height="832" alt="image" src="https://github.com/user-attachments/assets/adcbd1b5-a2d7-4809-9be8-da58a63beb9e" />

essa informação é valiosa pois será necessária para realizar o ataque com a ferramenta Hydra.

## Brute Force na aplicação web utilizando o Hydra

vamos usar as mesmas listas utilizadas no ataque anterior, os arquivos **users.txt** e **pass.txt**

**-L** arquivo contendo os usuários

**-P** o arquivo contendo as senhas

**IP do alvo**

**tipo de ataque, formulário HTTP via POST**

**página alvo, campos do formulário e mensagem que aparece quando o login falha**

**-t** quantidade de processos/tentativas realizadas simultaneamente

<img width="538" height="497" alt="image" src="https://github.com/user-attachments/assets/ca7a136f-fc76-4b3a-b33c-c414f0ab00db" />

o Hydra executa o ataque Brute Force e retorna as credenciais válidas: **login: admin senha: password**

usando essas credenciais, conseguimos realizar o login na página web do DVWA:

<img width="1274" height="855" alt="image" src="https://github.com/user-attachments/assets/af085b14-e044-4be7-be73-07c0c50d8f93" />

## Ataque de Enumeração de Usuários e Password Spraying contra Serviços SMB

utilizando a ferramenta enum4linux, iremos fazer um scan no alvo para enumerar informações sobre o servidor SMB da máquina alvo. essas informações serão salvas no arquivo enum4_output.txt

<img width="475" height="64" alt="image" src="https://github.com/user-attachments/assets/10b4bd82-d2ce-4e1f-a574-441fdfc6ac13" />

com esse comando, encontramos uma lista de usuários relacionados com o servidor SMB:

<img width="242" height="573" alt="image" src="https://github.com/user-attachments/assets/2fbd7516-5d32-4d26-8432-171854ab3a51" />

agora, criamos nossos arquivos contendo nomes de usuários e senhas para serem usados em nosso Brute Force, adicionando os usuários encontrados e algumas senhas comuns

<img width="764" height="129" alt="image" src="https://github.com/user-attachments/assets/4fe6ecda-d6a6-41a8-bb10-e1a245a56116" />

## Brute Force no SMB utilizando o Medusa

dessa vez, adicionamos também o parâmetro **-T 50**, que indica o número de hosts testados simultaneamente. nesse caso em específico, por se tratar de apenas um, não fará diferença, porém, numa situação de mundo real, isso tornaria o processo mais rápido.

<img width="730" height="83" alt="image" src="https://github.com/user-attachments/assets/725f72d7-d12b-4514-8d83-e3d3c0f18b6f" />

após a execução do comando, o Medusa nos retorna um resultado positivo:

<img width="1080" height="562" alt="image" src="https://github.com/user-attachments/assets/2d9d64e9-edcb-4082-b98d-e6b80249db2c" />

portanto, usando essas credenciais, conseguimos acesso ao servidor SMB

<img width="754" height="336" alt="image" src="https://github.com/user-attachments/assets/febf2bf5-4440-488e-8a7f-3b99c6773741" />

