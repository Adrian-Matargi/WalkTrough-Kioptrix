# Kioptrix Walkthrough

## 1. Informações da Máquina
- Plataforma: VulnHub
- Nome: Kioptrix
- Objetivo: Obter acesso root

## 2. Network Discovery

Para identificar a máquina vulnerável na rede local foi realizado um scan utilizando o Nmap:

nmap -sV 192.168.56.100/24

Durante o scan foi identificado o host ativo:

Target IP: 192.168.56.103

Este IP foi identificado como a máquina Kioptrix na rede local.

## 3. Port Scanning

Foi realizado um scan detalhado utilizando Nmap para identificar serviços e versões em execução na máquina alvo.

Comando utilizado:

nmap -sC -sV -T4 192.168.56.103

Resultado:

22/tcp   open  ssh      OpenSSH 2.9p2
80/tcp   open  http     Apache httpd 1.3.20
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
443/tcp  open  https    Apache httpd 1.3.20 (mod_ssl)
32768/tcp open status

## 4. Web Enumeration

Ao acessar o serviço HTTP na porta 80 foi possível visualizar a página padrão do Apache.

http://192.168.56.103

A página exibida foi a "Apache Test Page", indicando que o servidor está utilizando Apache em um sistema Red Hat Linux.
A página não apresenta funcionalidades interativas ou campos de entrada, indicando que a exploração provavelmente ocorrerá através de vulnerabilidades conhecidas no servidor web ou em módulos do Apache.

## 5. Exploit Research

Foi utilizada a ferramenta Searchsploit para identificar exploits públicos para os serviços encontrados.

searchsploit mod_ssl

Foi identificado o exploit:

Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck' Remote Buffer Overflow

Este exploit corresponde à versão do Apache e do mod_ssl identificadas no alvo.

## 6. Exploit Preparation

O exploit identificado no Searchsploit foi copiado para o diretório local utilizando:

searchsploit -m 764.c

Após copiar o código fonte do exploit, foi realizada a compilação utilizando o GCC:

gcc 764.c -o openfuck -lcrypto

A compilação gerou o executável "openfuck", que será utilizado para explorar a vulnerabilidade no servidor.

## Tentativa de exploração com OpenFuck

Durante a análise das vulnerabilidades foi identificado um exploit público para o serviço mod_ssl presente no servidor Apache:

Apache mod_ssl < 2.8.7 OpenSSL - 'OpenFuck' Remote Buffer Overflow

Foi realizada a tentativa de compilação do exploit utilizando o GCC:

gcc 764.c -o openfuck -lcrypto -lssl

Entretanto, devido à incompatibilidade entre o código antigo do exploit e as bibliotecas modernas do OpenSSL presentes no Kali Linux, ocorreram diversos erros de compilação relacionados a funções criptográficas obsoletas, como RC4 e MD5.

Diante dessas dificuldades, optou-se por continuar o processo de enumeração em busca de outros vetores de exploração disponíveis na máquina alvo.
