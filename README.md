# ProfitTrailer-HTTPS
Instalação e configuração do HTTPS/SSL no ProfitTrailer

## Requesitos:

- 1 - O seu ambiente deve esta em linux (Sorry!) 
- 2 - Acesso ao console/terminal da sua instancia
- 3 - Um nome de dominio para a sua Instancia ex: meuprofittraler.duckdns.org
- 4 - Vai precisar alterar o application.properties, deve fazer backup e depois edita-lo com o VIM ou NANO etc..

### Passos:

- Passo 01 

> Nota: Se você já possui em dominio para a sua VPS pule para o **Passo 02**

Voce pode acessar o site duckdns.org, onde você consegue criar subdominios e aponta-los para sua instancia

1 - logue no site duckdns.org, pode ser com sua conta do gmail

2 - coloque o nome do seu subdominio e clique em **add domain**

> Nota: neste exemplo. eu já criei o subdominio  **meuprofittraler.duckdns.org**, desta forma, o mesmo ja esta uso, voce deve criar com outro nome.

print-1-01

Observe que após adicionar o subdominio, o mesmo coloca o IP do seu provedor de saida, voce deve alterar este IP para o IP da sua Instancia/VPS,
após colocar o ip de sua instancia nao se esqueça de clicar na opcao **update ip**

> Nota: leva um certo e longo  tempo (Até 24hs) para a publicação do subdominio apontar para a sua instancia, voce pode testar no seu navegador
> ex: http://meuprofittraler.duckdns.org:8081

Pronto, agora o subdominio meuprofittraler.duckdns.org já esta apontado para a sua instancia, vamos ao segundo passo.

- Passo 02 

voce deve acessar a sua instancia via SSH, ou seja, se estiver do windows pode utilizar o software putty.exe

> Nota: eu utilizo o [MobaXterm](https://mobaxterm.mobatek.net/download.html)  no Windows

Ex: conexao com instancia com o MobaXterm

```bash
   ssh MEU_USER@MEUIP
```

- Passo 03 

logado no seu servidor, sugiro que vire gente grande, ou seja root, ou voce pode digitar o comando **sudo** antes de cada comando.

Vamos instalar o git, com o comando abaixo: OBS preste atenção em qual S.O voce esta usando, acredito que seja o ubuntu :)  

Comando no CentOS:

```bash
   yum install -y git
```

Comando no Ubuntu/Debian

```bash
   sudo apt-get  update && apt-get install  git -y 
```
Agora vamos baixar o projeto **certbot**, siga os comandos abaixo:

```bash
git clone https://github.com/certbot/certbot
```

```bash
cd certbot
```

```bash
./certbot-auto certonly -a standalone \
     -d meuprofittraler.duckdns.org -d meuprofittraler.duckdns.org
```

> NOTA: observe no comando acima o subdominio: meuprofittraler.duckdns.org, você deve colocar o seu!

após execultar o comando, ele deve instalar novos pacotes, então vai pergunta se você quer continuar, reponda sim com a letra ( Y ) do seu teclado.

print-2-01

Adicone um email valido, depois aceite os Termos do contrato com a letra ( A ) do seu teclado, e por fim aperte ( Y )

print-2-02

Agora vamos para o diretorio: /etc/letsencrypt/live/meuprofittraler.duckdns.org

```bash
cd /etc/letsencrypt/live/meuprofittraler.duckdns.org/
```
> NOTA: lembre-se alterar o comando acima para o seu subdominio.

vamos execultar o comando abaixo:


```bash
openssl pkcs12 -export -in fullchain.pem -inkey privkey.pem -out keystore.p12 -name tomcat -CAfile chain.pem -caname root
```

> OBS: Digite uma senha e a repita.
 

PASS 03 

Vamos configurar o arquivo application.properties da sua aplicação, voce deve edita-lo com o nano ou vim, ainda se preferir
baixe em seu Desktop edite e mande ao servidor.

OBS: FAÇA BACKUP DO SEU ARQUIVO, ANTES DE INICIAR ESTE PASSO!

> Neste passo, vou assumir que a senha que você configurou no comando acima foi: **PRECISOIRDORMIR**

Voce deve adiconar as seguintes linhas em seu arquivo:
```bash
server.ssl.key-store:/etc/letsencrypt/live/meuprofittraler.duckdns.org/keystore.p12
server.ssl.key-store-password: PRECISOIRDORMIR
server.ssl.key-password: PRECISOIRDORMIR
server.ssl.keyStoreType: PKCS12
server.ssl.keyAlias: tomcat
```

ou seja, o seu arquivo deve ficar mais ou menos assim 

```json

################## application.properties #########################
################### VERSION CONTROL ###############################
###################################################################
### Read the wiki for more explanations about the properties    ###
### https://wiki.profittrailer.io                               ###
###################################################################
###################################################################

server.port = 8081

server.ssl.key-store:/etc/letsencrypt/live/meuprofittraler.duckdns.org/keystore.p12
server.ssl.key-store-password: PRECISOIRDORMIR
server.ssl.key-password: PRECISOIRDORMIR
server.ssl.keyStoreType: PKCS12
server.ssl.keyAlias: tomcat


trading.exchange = BINANCE

#Put here your activated API key.
#This API key is also use to read YOUR balances and trade history
default_apiKey = SUAAPI
default_apiSecret = SUAAPI

#Put here a second api key that will be used to do all the buying and selling.
#This api key does NOT need to be activated
trading_apiKey = SUAAPI
trading_apiSecret = SUAAPI

server.timeZoneOffset = -02:00
server.password = SUASENHA

# how many days of log history to show?
trading.logHistory = 30
server.sitename = BINANCE_BTC
server.enableConfig = true
#OPTIONAL
#telegram.botToken =
#telegram.chatId =
```

Pronto, renicie a aplicao e acesse com o site colocando https://seusubdomio.duckdns.org:8081 

nao se esqueca do https!!!

se der ruim, apenas volte o arquivo application.properties para o original 

Referencias oficiais:

[ProfitTrailer](https://wiki.profittrailer.io/doku.php/pt:setup_ssl)











