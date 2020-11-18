# Integrando o Red Hat Single Sign-On com o 3Scale

Demonstração de como integrar o 3scale com o Red Hat Single Sign-On (RH-SSO) e posteriormente expor APIS através do 3scale adicionando um camada de segurança.

O Red Hat Single Sign-On (RH-SSO) é uma solução de Sign-On integrada (SSO) que, quando usada em conjunto com 3scale, permite que você autentique seus desenvolvedores usando qualquer uma das opções de identity brokering e user federation.

## Pré-requisitos

Este workshop tem como pré requisito a instalação e configuração de uma instancia do Red Hat Single Sign-ON e do 3scale. As instruções podem ser encontradas no portal Red Hat (https://access.redhat.com/documentation/en-us/).

## Integrando console de administração do 3scale com o Red Hat Single Sign-On (RH-SSO):

0. [Configurando o Red Hat Single Sign-ON (RH-SSO)](#testdrive-step-0)
1. [Criando um realm](#testdrive-step-1)
2. [Adicionando um novo Client](#testdrive-step-2)
3. [Integrando 3scale Admin Console com RH-SSO](#testdrive-step-3)


## Expondo APIs através do 3scale e integrando com o Red Red Single Sign-ON (RH-SSO):

0. [Introdução.](#testdrive-step-4)
1. [Criando um Realm no RH-SSO.](#testdrive-step-5)
2. [Expondo sua API backend no 3scale.](#testdrive-step-6)
3. [Integrando sua API com o RH-SSO.](#testdrive-step-7)
4. [Configurando o Zync pod para certificados auto assinados.](#testdrive-step-8)
5. [Criando um Application Plan.](#testdrive-step-9)
6. [Criando um novo usuário para acesso a API.](#testdrive-step-10)
7. [Vinculando usuário a uma Application.](#testdrive-step-11)
8. [Testando Integração.](#testdrive-step-12)

### Configurando o Red Hat Single Sign-ON (RH-SSO). <a name="testdrive-step-0"></a>

Neste primeiro passo iremos configurar o Red Hat Single Sign-ON (RH-SSO) seguindo a [documentação do produto](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html-single/creating_the_developer_portal/index#enabling_and_disabling_authentication_via_red_hat_single_sign_on).

### Criando um realm. <a name="testdrive-step-1"></a>

Após realizar o login na console de administração do RH-SSO, posicione o mouse abaixo de **Master** (lado esquerdo acima) e clique em **Add realm**.
Informe o nome do realm **3scale-admin**

![](images/1.png)

Do lado esquerdo acima, estará o nome do realm criado conforme imagem abaixo:

![](images/2.png)


### Adicionando um novo Client. <a name="testdrive-step-2"></a>

Adicione um novo client no realm **3scale-admin** que criamos anteriormente.

Vá até a opção **Clients** e clique em **Create**.

![](images/3.png)

Informe o **Client ID**, no nosso caso será o **3scale** e clique em **Save**.

![](images/4.png)

Configure os parâmetros deste **Client** conforme os campos abaixo:

- **Client ID:** informe o nome desejado, no nosso caso será **3scale-rh-sso**.
- **Enabled:** mude para **ON**.
- **Consent Required:** mude para **OFF**.
- **Client Protocol:** selecione **openid-connect**.
- **Access Type:** selecione **confidential**.
- **Standard Flow Enabled:** mude para **ON**.
- **Root URL:** Informe a URL da console de administração do 3scale https://yourdomain.3scale.net.
- **Valid Redirect URLs:** Informe o endereço da console de administração do 3scale e adicione: **/*** por exemplo: https://yourdomain.3scale.net/*.

TODOS os demais parâmetros devem ser mudados para **OFF**.

![](images/5.png)

Vá até a aba **Credentials** e copie o client secret gerado conforme a imagem a seguir:

![](images/6.png)

Posteriormente vá até a aba **Mappers** e clique em **Add Builtin**

![](images/7.png)

Selecione **email verified** e clique em **Add selected**

![](images/8.png)

Certifique-se de ter configurado corretamente conforme imagem a seguir:

![](images/9.png)

### Configurando o 3scale Admin Console. <a name="testdrive-step-3"></a>

Neste primeiro passo iremos integrar a console de administração do 3 scale com o nosso client criado no Red Hat Single Sign-On (RH-SSO).

Após logar na console de administração do 3scale, vá até **Account Settings (*) - Users - SSO Integrations **

![](images/10.png)

Clique em **New SSO Integration** e preencha os campos conforme abaixo:

- **Client:** Nome do client no Red Hat Single Sign-On
- **Client Secret:** Client secret 
- **Realm:** Nome do Realm e URL de acesso para o Red Hat Single Sign-ON

Posteriormente clique em **Create Authentication Provider**

![](images/11.png)

Agora iremos testar nossa integração. Clique em **Test authentication flow now**

![](images/12.png)

Faça o login com um usuário do RH-SSO. 

![](images/13.png)

** Caso você não tenha criado um usuário anteriormente, siga os passos descritos em [**4.2. Creating New Users**](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.9/html-single/creating_the_developer_portal/index#enabling_and_disabling_authentication_via_red_hat_single_sign_on)

** Caso receba a mensagem de erro: **Your account isn't active or hasn't been approved yet.**, ative a flag **Email Verified** para **ON** nas configurações do seu usuário no RH-SSO.

Após validar o fluxo de autenticação, clique em **Publish** e verifique se o **State** mudou para **Visible**

![](images/14.png)

Pronto, sua console de administração do 3scale está instegrada ao Red Hat Single Sign-On (RH-SSO). Basta clicar em **Authenticate through Red Hat Single Sign-On** que seu login será feito com base nos usuários do RH-SSO.

![](images/15.png)

Após logar com o usuário do RH-SSO, logue novamente na console de administração do 3scale e vá em **Account Settings (*) - Users - Listing**, clique em **Edit** e na seção **Administrative**, aplique a **Role - Admin (full access)** para seu usuário e depois clique em **Update User**, assim poderemos seguir para os próximos passos com todas as permissões aplicadas a este usuário.

![](images/16.png)


### Introdução. <a name="testdrive-step-4"></a>

Nos passos a seguir, iremos confgurar um novo realm no Red Hat Single Sign-On e posteriormente um novo client que serão utilizados pelas APIS que serão expostas no 3 scale. Seguiremos os passos [descritos na documentação](https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.7/html/administering_the_api_gateway/openid-connect).

### Criando um Realm no RH-SSO. <a name="testdrive-step-5"></a>

Crie um novo **realm** chamado **3scale-apis**

![](images/17.png)

Crie um novo client chamado **3scale-apis**

![](images/18.png)

Configure os parâmetros do novo client conforme abaixo:

- **Access Type**: confidential.
- **Standard Flow Enabled**:  OFF.
- **Direct Access Grants Enabled**:  OFF.
- **Service Accounts Enabled**: ON

![](images/19.png)

Após salvar as configurações do client, vá até a aba **Service Account Roles** 

![](images/20.png)

Em **Client Roles** digite **realm-management**

![](images/21.png)

Selecione **manage-clients** em **Available Roles** e clique em **Add Selected**

![](images/22.png)

Anote o **client-id** e o **cient-secret** que pode ser consultado na aba **Credentials** pois usaremos posteriormente

![](images/23.png)


### Expondo sua API backend no 3scale. <a name="testdrive-step-6"></a>

Neste exemplo iremos expor uma API backend que foi realizado o deploy no Openshift. O código desta api e as informações de deploy no Openshift podem ser encotradas em: https://github.com/raraujo-dev/spring-boot-rest-api

Após realizar o login no 3scale, clique na aba **BACKENDS** e clique em **NEW BACKEND**

![](images/24.png)

Preencha os campos de sua API que será exposta no 3scale, informe a URL de acesso em **Private Base URL**

![](images/25.png)

No menu lateral selecione **Methods and Metrics** e clique em **New method**

![](images/26.png)

Nossa API REST que será exposta possui retorna no path /currentDateTime a data e o horário do ambiente que a API está rodando.

Neste caso iremos adicionar o método que irá retornar esta informação, assim poderemos coletar métricas posteriormente deste método específico.

Preencha as informações do seu método e clique em **Create Method**

![](images/27.png)

Agora clique em **Add a mapping rule**

![](images/28.png)

Neste exemplo, irei preencher as informações do meu path **/currentDateTime** da minha API que está sendo exposta pelo 3scale.

Após preencher as informações da sua API clique em **Create Mapping Rule**

![](images/29.png)

No menu central, selecione **Dashboard** e posteriormente a aba **PRODUCTS**

![](images/30.png)

Clique em **NEW PRODUCT**, preencha as informações da sua API que será exposta como um produto cadastrado no 3scale e posteriormente clique em **Create Product**

![](images/31.png)

Após criar o produto, você será direcionado para a página de **Overview**

![](images/32.png)

No menu lateral clique em **Integration - Backends** e clique em **Add Backend**

![](images/33.png)

Selecione o backend cadastrado anteriormente e preencha o **Path** da sua API antes de clicar em **Add to Product**

![](images/34.png)

### Integrando sua API com o RH-SSO. <a name="testdrive-step-7"></a>

No menu lateral selecione **Settings**

![](images/35.png)

Em **AUTHENTICATION** mude para **OpenID Connect Use OpenID Connect for any OAuth 2.0 flow.**

![](images/36.png)

No campo **OpenID Connect Issuer** iremos preencher o location do RH-SSO seguindo o modelo https://<CLIENT_ID>:<CLIENT_SECRET>@<HOST>:<PORT>/auth/realms/<REALM_NAME>". 

No nosso caso iremos preencher os campos conforme as informações do client criado no RH-SSO.

- **CLIENT_ID**: 3scale-apis
- **CLIENT_SECRET**: 387200ee-6828-4d63-aa1a-2d07ad0034a3
- **HOST**: sso-xxx-rh-sso.apps.saopaulo-996b.open.redhat.com
- **PORT**: 443
- **REALM NAME**: 3scale-apis

A url final ficará da seguinte forma **https://3scale-apis:387200ee-6828-4d63-aa1a-2d07ad0034a3@sso-xxx-rh-sso.apps.saopaulo-996b.open.redhat.com/auth/realms/3scale-apis**

![](images/37.png)

Em **OIDC AUTHORIZATION FLOW** selecione:

- Authorization Code Flow
- Service Accounts Flow
- Direct Access Grant Flow

Em **CREDENTIALS LOCATION** selecione **As HTTP Headers**

![](images/38.png)


Role a página até o final e clique em **UPDATE PRODUCT**

Posteriormente, vá até a aba **Configuration** e clique em **Promoto to staging API Cast** para aplicar as novas configurações.

![](images/54.png)

### Configurando o Zync pod para certificados auto assinados. <a name="testdrive-step-8"></a>

Quando integramos o 3scale com o Rh-SSO é estabelecido uma conexão SSL entre Zync (3scale) e Red Hat Single Sign-On que será responsável por sincronizar os dados entre o 3scale e o RH-SSO. Quando utilizado um certificado auto assinado (self signed) é necessário realizar uma configuração adicional. Este procedimento pode ser encontrado na documentação em **[Configure Zync to use custom CA certificates]**(https://access.redhat.com/documentation/en-us/red_hat_3scale_api_management/2.7/html/administering_the_api_gateway/openid-connect#configure_zync_to_use_custom_ca_certificates)

Após realizar o login no cluster openshift, execute o comando abaixo no namespace onde está instalado o 3scale.

    oc get pods
    
![](images/39.png)

Observe que o pod responsável por está sincronização, no examplo acima, é o **zync-que-1-gcg5k**

Agora iremos executar o comando abaixo, substituindo o <zync-pod-id> por zync-que-1-gcg5k, este comando irá buscar o certificado do pod e posteriormente irá gravar no arquivo zync.pem.

    oc exec <zync-pod-id> cat /etc/pki/tls/cert.pem > zync.pem

Anexe o novo arquivo ao pod Zync como ConfigMap:

    oc create configmap zync-ca-bundle --from-file=./zync.pem
    
 ![](images/40.png)

    oc set volume dc/zync --add --name=zync-ca-bundle --mount-path /etc/pki/tls/zync/zync.pem --sub-path zync.pem --source='{"configMap":{"name":"zync-ca-bundle","items":[{"key":"zync.pem","path":"zync.pem"}]}}'
    
    
     oc patch dc/zync --type=json -p '[{"op": "add", "path": "/spec/template/spec/containers/0/volumeMounts/0/subPath", "value":"zync.pem"}]'

Após o deployment, verifique se o certificado está anexado e se o conteúdo está correto:

    oc exec <zync-pod-id> cat /etc/pki/tls/zync/zync.pem

Configure a variável de ambiente **SSL_CERT_FILE** no Zync para apontar para o novo pacote de certificado CA:

    oc set env dc/zync SSL_CERT_FILE=/etc/pki/tls/zync/zync.pem

 ![](images/41.png)
 
 Faça o rollout do último deploy para aplicar as novas configurações
 
     oc rollout latest dc/zync
     
  ![](images/42.png)

Execute o comando abaixo e veja que um novo rollout do zync pod foi realizado

    oc get pods
    
  ![](images/43.png)

Execute o comando #oc exec <zync-pod-id> cat /etc/pki/tls/zync/zync.pem  - para se certificar que o certificado foi anexado ao pod com sucesso.

    oc exec zync-4-9mzbr cat /etc/pki/tls/zync/zync.pem

  ![](images/44.png)
  
  ### Criando um Application Plan. <a name="testdrive-step-9"></a>

Application Plans estabelecem as regras (limites, preços, recursos) para usar sua API; cada aplicativo de desenvolvedor acessando sua API estará acessando-o dentro das restrições de um Application Plan.

No menu central selecione o produto que acabamos de criar **Get Current Date and Time**, posteriormente no menu lateral selecione **Applications - Applications Plans** e clique em **Create Application Plan**

  ![](images/45.png)

Preencha os campos do seu Application Plan e clique em **Create Application Plan**

  ![](images/46.png)

  ### Criando um novo usuário para acesso a API. <a name="testdrive-step-10"></a>

Agora no menu central selecione **Audience** e logo em seguida no menu lateral vá até **Accounts - Listing**e posteriormente clique em **Create**

  ![](images/47.png)

Preencha os dados do seu usuário e clique em **Create**

  ![](images/48.png)

Agora veja que seu usuário foi criado com sucesso.

  ![](images/49.png)

  ### Vinculando usuário a uma Application. <a name="testdrive-step-11"></a>

Logo após criar seu usuário que irá acessar sua API sendo exposta pelo 3scale, clique em **Applications** e posteriormente **Create Application**

  ![](images/50.png)

Após preencher os dados clique em **Create Application**

  ![](images/51.png)

Veja que neste momento as **API Credentials** para acesso a sua API exposta no 3scale foram criadas

  ![](images/52.png)
  
Vá até a console de administração do RH-SSO e veja se este client foi sincronizado com o RH-SSO conforme a imagem abaixo:
  
  ![](images/53.png)
  
  Em caso de problemas, consulte os logs do pod **zync-que-xxx** no projeto do 3scale e/ou verifique se as configurações foram aplicadas corretamente.

  ### Testando Integração. <a name="testdrive-step-12"></a>

Utilizando o username e password do usuário que criamos no 3scale, iremos realizar um **POST** no endereço do **token_endpoint** do **RH-SSO**. Devemos enviar como parâmetro:

- **username**: usuário criado no RH-SSO
- **password**: password do usuário criado no RH-SSO
- **grant_type**: password
- **client_secret**: o client secret da sua aplicaçao que pode ser consultado no 3scale
- **client_id**: client id da aplicação que pode ser consultado na console do 3scale

O **token_endpoint** do seu realm pode ser consultado na console do RH-SSO em **Endpoints** conforme a imagem a seguir:

  ![](images/55.png)
  
  ![](images/56.png)


Irei utilizar o postman para realizar este teste:

  ![](images/57.png)
  
  Será retornado o access token:
 
   ![](images/58.png)
   
 De posse do access token, agora iremos realizar a chamada para nossa aplicação.
 
 Vá até a console do 3scale e copie a URL de acesso da sua API exposta no 3scale, basta selecionar a API no menu central e posteriormente no menu lateral selecionar **Integration - Configuration**

   ![](images/59.png)

No postman, faça uma request informando o **endpoint** e a **Mapping Rule**da API exposta no 3scale.

No header passe como parâmetro a variável **Authorization** com o valor **Bearer token_coletado** conforme na imagem a seguir:

   ![](images/60.png)
   
Pronto, o agora temos o retorno da API exposta no 3scale

   ![](images/61.png)

   





