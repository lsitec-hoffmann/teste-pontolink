# APIS
- Controlador: https://1otjpmznaa.execute-api.sa-east-1.amazonaws.com/dev
- Servidor de áudio: https://ywyw9kjjoh.execute-api.sa-east-1.amazonaws.com/dev
- Aplicativos: https://f5s6qaw2yl.execute-api.sa-east-1.amazonaws.com/dev
- Aplicativos: https://f5s6qaw2yl.execute-api.sa-east-1.amazonaws.com/dev

# Controlador-AWS

## Requisitos

Para o acompanhamento deste tutorial é necessário:
- Uma conta válida na AWS (Amazon Web Services);
- Sistema operacional Linux;
- Download dos arquivos enviados na transferência do softwares;
- Python3;
- Servidor HTTP;
- Configuração do AWS-CLI: https://www.treinaweb.com.br/blog/como-instalar-e-configurar-o-aws-cli/

## Cognito

O primeiro serviço a ser configurado é o Cognito. Ele fará o gerenciamento das contas de usuário. Para iniciar, pesquise por Cognito (na tela de gerenciamento do console) e selecione-o. Na tela inicial do serviço Cognito, clique na opção Gerenciar grupos de usuários. Se você já tiver criado um grupo de usuário, selecione-o e pule para o passo 18. Caso ainda não possua um grupo, siga os passos abaixo.

1. Na tela de gerenciamento do Cognito selecione a opção Criar um grupo de usuários;
2. Informe o nome do grupo. Por exemplo, usuarios-controlador;
3. Clique em Configurações passo a passo;
4. Selecione a opção Endereço de e-mail ou número de telefone e o tópico Permitir endereços de e-mail;
5. Deixe marcada a opção Habilitar a não diferenciação de maiúsculas e minúsculas para entrada de nome de usuário;
6. Em atributos padrão selecione e-mail;
7. Clique em Adicionar atributo personalizado. Dê o nome de temporaria e clique em Próxima etapa;
8. Na opção sobre as senhas, deixe o tamanho padrão igual a 8 e desmarque apenas a opção Exigir caracteres especiais. Então, clique em Próxima etapa;
9. Na pergunta “Como um usuário poderá recuperar a conta dele?”, selecione Somente e-mail;
10. Na pergunta “Quais atributos você deseja verificar?”, deixe marcado apenas a opção de e-mail e clique em Próxima etapa;
11. Nesta página vá até a opção “Deseja personalizar suas mensagens de e-mail de verificação?” e em Tipo de verificação selecione Código. Clique me próxima etapa;
12. Na configuração de Tags, basta clicar em próxima etapa;
13. Na opção de lembrar dos dispositivos, clique em Não e em próxima etapa;
14. Na opção de Clientes de aplicativo clique em Adicionar um cliente de aplicativo. Informe um nome para este cliente (como cliente-usuarios). Informe todos os tempos relacionados a expiração para 1 dia. Desmarque a opção Gerar segredo do cliente e marque todas as opções de Configurações de fluxos de autenticação. Em Configuração de segurança deixe Habilitado e clique em Criar cliente de aplicativo e então em Próxima etapa;
15. No fluxo de trabalho com gatilhos, basta clicar em próxima etapa;
16. Revise as opções. Estando tudo correto, clique em Criar grupo;
17. No menu a esquerda selecione Nome do domínio. Forneça um nome de domínio e clique em Verificar disponibilidade. Caso esteja disponível, clique em Salvar alterações.
18. No menu a esquerda selecione a opção Configurações gerais. Selecione o **ID do grupo** e substitua esse valor na variável **userPoolId** no arquivo **utils/defs.py**;
19. No menu a esquerda selecione a opção Configurações do cliente de aplicativo. Selecione o ID e substitua esse valor na variável **clientId** no arquivo **utils/defs.py**;
20. No menu a esquerda selecione a opção Integração do aplicativo. Selecione o **Domínio** e substitua esse valor na variável **cognitoLink** no arquivo **utils/defs.py**;
21. Por fim, informe a URL do controlador na variável **CONTROLADOR_LINK** no arquivo **utils/defs.py**.

## S3

Pesquise e entre no serviço S3. Se já possuir um bucket criado, pule para o passo 5. Caso não, siga os procedimento abaixo.

1. Na página inicial, clique em Criar bucket;
2. Informe um nome para o bucket e selecione a região; 
3. Desmarque a opção de Bloquear todo o acesso público e clique em Criar bucket.
4. Você será redirecionado para a lista com todos os buckets. Clique em cima do que foi criado anteriormente. Clique em Criar pasta e informe o nome images e clique em Criar pasta.
5. No arquivo **utils/defs.py** substitua o nome do Bucket na variável **nameBucket**;
6. No arquivo **utils/defs.py** substitua a URL do Bucket na variável **urlBucket**. O padrão é **https://[nomeBucket].s3.[regiao].amazonaws.com**.

## EC2

Pesquise e entre no serviço EC2. Se já possuir uma instância criada, pule para o passo 2. Caso não, siga os procedimento abaixo.

1. No menu a esquerda clique em Painel EC2. Informe as configurações e crie sua instância (a depender de sua necessidade);
2. Clique na sua instância. Selecione a aba **Segurança** e clique na opção do **Grupos de segurança**;
3. Em Regras de entrada, clique em **Editar regras de entrada**. Selecione o tipo MYSQL/Aurora. Em Origem selecione Personalizado e informe o padrão 0.0.0.0/0;
4. Clique em **Adicionar regra**. Selecione o tipo MYSQL/Aurora. Em Origem selecione Personalizado e informe o padrão ::/0. Clique em **Salvar regras**;
5. No menu a esquerda selecione Instâncias e clique sobre sua instância. Acesse sua instância via SSH;
6. Execute o comando abaixo para configura o horário da instância como UTC-3:
```
sudo rm -f /etc/localtime
sudo ln -s /usr/share/zoneinfo/Brazil/East /etc/localtime
```
7. Instale o Mysql utilizando o seguinte comando (se já estiver instalado, pule este passo):
```
sudo apt install mysql-server
```
8. Execute o seguinte comando para abrir as configurações do Mysql:
```
sudo nano /etc/mysql/my.cnf
```
9. Substitua o conteúdo do arquivo por esse:
```
#!includedir /etc/mysql/conf.d/
#!includedir /etc/mysql/mysql.conf.d/
[mysqld]
socket=/var/lib/mysql/mysql.sock
bind-address=0.0.0.0
port = 3306
  
[client]
port=3306
socket=/var/lib/mysql/mysql.sock
```
10. Reinicie o serviço com o comando abaixo:
```
sudo service mysql restart
```
11. Execute o seguinte comando para entrar no Mysql. Quando necessário, forneça a senha:
```
mysql -h 127.0.0.1 -u root -p
```
12. Crie o usuário **admin** com os seguintes comandos (substitua a senha por uma segura):
```
CREATE USER 'admin'@'%' IDENTIFIED WITH mysql_native_password BY '[password]';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit;
```
- Execute este passo **APENAS** se já possuir o usuário admin criado:
```
ALTER USER 'admin'@'%' IDENTIFIED WITH mysql_native_password BY '[password]';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
exit;
```
13. Transfira o arquivo controlador.sql para o servidor. E logo após, insira o seguinte comando:
```
mysql -h 127.0.0.1 -u root -p < controlador.sql
```
14. Após isso, feche a conexão SSH e vá para o Painel EC2 no dashboard da AWS;
15. Selecione sua instância e copie a informação de **DNS IPv4 público** e cole na variável **bd_host** do arquivo **utils/defs.py**;
16. Informe também o **bd_password** que você criou anteriormente.

## IAM

Para configurar as permissões, execute o seguinte comando:
```
python3 iam.py
```

## Lambda

Para configurar as funções Lambda, execute o seguinte comando:
```
python3 lambda.py
```

Logo após, execute os passos abaixo: 

1. Entre novamente no serviço Cognito e selecione o pool dos usuários. 
2. Selecione, no menu a esquerda, a opção **Verificações e MFA**;
3. Na pergunta Quais atributos você deseja verificar?, selecione **E-mail** e clique em Salvar alterações;

## API Gateway

Para configurar o API Gateway, você precisa ter 3 APIS criadas com o seguintes nomes:
- lp-app;
- lp-operador;
- lpadmin.

Após criar as 3 APIS, execute:
```
python3 apigateway.py
```

aaa
a
aa
a
a
a

Após executar esses comandos, volte para o dashboard da AWS e execute os passos abaixo:

1. Selecione a api **lp-app**;
2. Clique em Ações e Implantar API. Selecione o Estágio da implantação como **dev** (caso não tenha a opção dev, selecione Novo Estágio e forneça o nome de dev);
3. Clique em Implante;
4. Clique em APIS;
5. Selecione a api **lp-operador**;
6. Clique em Ações e Implantar API. Selecione o Estágio da implantação como **dev** (caso não tenha a opção dev, selecione Novo Estágio e forneça o nome de dev);
7. Clique em Implante;
8. Clique em APIS;
9. Selecione a api **lp-admin**;
10. Clique em Ações e Implantar API. Selecione o Estágio da implantação como **dev** (caso não tenha a opção dev, selecione Novo Estágio e forneça o nome de dev);
11. Clique em Implante.

## Reconfigurando o Cognito

Execute o comando abaixo e forneça um e-mail e uma senha (esse passo deve ser utilizado apenas na primeira vez para criar o primeiro administrador do sistema):

```
python3 cria_usuario.py
```

Após isso, suba os códigos para o servidor HTTP, alterando o link da API do controlador para o link da API **lpadmin**.