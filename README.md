# Workshop integrando o Red Hat Single Sign-On e 3Scale

Demonstração de como integrar o 3scale com o Red Hat Single Sign-On (RH-SSO) e posteriormente expor APIS através do 3scale adicionando um camada de segurança.

O Red Hat Single Sign-On (RH-SSO) é uma solução de Sign-On integrada (SSO) que, quando usada em conjunto com 3scale, permite que você autentique seus desenvolvedores usando qualquer uma das opções de identity brokering e user federation.

## Pré-requisitos

Este workshop tem como pré requisito a instalação e configuração de uma instancia do Red Hat Single Sign-ON e do 3scale. As instruções podem ser encontradas no portal Red Hat (https://access.redhat.com/documentation/en-us/).

## Laboratórios:

0. [Criando um banco de dados no Openshift](#testdrive-step-0)
1. [Criando a tabela no banco de dados](#testdrive-step-1)
2. [Criando uma Conexão com um banco de dados no Fuse Online ](#testdrive-step-2)
3. [Criando uma API Rest no Fuse Online utilizando API First Approach](#testdrive-step-3)
4. [Utilizando Conditional Flows](#testdrive-step-4)
4. [SoapToRest](https://developers.redhat.com/blog/2018/07/24/migrate-soap-to-rest-with-camel/)
5. [API First](https://developers.redhat.com/blog/2018/07/12/contract-first-api-design-with-apicurio-and-red-hat-fuse/)


### Criando um banco de dados no Openshift <a name="testdrive-step-0"></a>


