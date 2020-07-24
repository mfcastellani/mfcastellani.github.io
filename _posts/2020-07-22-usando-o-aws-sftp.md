---
layout: post
author: marcelo castellani
title: Usando o SFTP na AWS
tags: Infraestrutura
description: Um artigo explicando como configurar o serviço de SFTP da AWS usando o S3 como backend.
---
‎¡Hola!

Este artigo foi publicado anteriormente no meu antigo blog EvilDead, sendo assim aproveite e curta esse som da excelente banda Helmet.

<p></p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/lEnMb4m9lu0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p></p>

### O que caralhas é SFTP e por que eu preciso disso?

Criar um serviço de SFTP com o AWS SFTP é muito simples e permite que voce tenha em pouco tempo no ar um serviço seguro para recepção de arquivos em sua empresa. O SFTP, de acordo com a Wikipédia, é:

> In computing, the SSH File Transfer Protocol (also Secure File Transfer
> Protocol, or SFTP) is a network protocol that provides file access, file
> transfer, and file management over any reliable data stream. It was
> designed by the Internet Engineering Task Force (IETF) as an extension
> of the Secure Shell protocol (SSH) version 2.0 to provide secure file
> transfer capabilities. The IETF Internet Draft states that, even though
> this protocol is described in the context of the SSH-2 protocol, it could
> be used in a number of different applications, such as secure file
> transfer over Transport Layer Security (TLS) and transfer of management
> information in VPN applications.

O serviço de SFTP da AWS foi lançado em 2018, e não é um de seus serviços mais conhecidos. Mas ele é bem simples de usar.

Para criar um SFTP na AWS você precisa de uma conta na AWS e conhecimento do que é um SFTP e dos serviços S3 e IAM. O S3 será usado como repositório de seu SFTP e o IAM será usado para criar as policies e roles para acesso.

### Criando um servidor SFTP

Criar um SFTP é bem simples. Na barra de serviços da AWS digite SFTP e quando localizar o serviço clique sobre o mesmo. Na página a seguir clique em New Server.

![](/assets/img/sftp/01.jpg)

Voce pode criar um SFTP público ou acessível apenas dentro da VPC. Para o gerenciamento de usuários recomendo o Service Managed, que indica que o serviço de SFTP irá manter as contas para voce.

Já para o hostname voce tem várias opções, sendo a recomendada para um SFTP público utilizar um endereço dentro do seu dominio. O serviço de SFTP é bem maroto nisso e já permite que voce crie direto no Route 53.

![](/assets/img/sftp/02.jpg)

### Pré requisitos para usuário

Após a criação do servidor voce deverá adicionar usuários para acesso. Para isso tenha um bucket S3 pronto com as opções padrão. Deixe o Block all public access ativado.

Também será necessário ter uma role do IAM para uso. Crie uma policy com permissão de acesso ao S3 como a seguir

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowListingOfUserFolder",
      "Action": [
        "s3:ListBucket",
        "s3:GetBucketLocation"
      ],
      "Effect": "Allow",
      "Resource": [
        "arn:aws:s3:::bucket_name"
      ]
    },
    {
      "Sid": "HomeDirObjectAccess",
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObjectVersion",
        "s3:DeleteObject",
        "s3:GetObjectVersion"
      ],
      "Resource": "arn:aws:s3:::bucket_name/*"
    }
  ]
}
```

Mude o nome bucket_name na role para o bucket que voce irá usar como base para o SFTP.

Crie agora uma role. Selecione AWS Service:

![](/assets/img/sftp/04.jpg)

E depois o serviço transfer.

![](/assets/img/sftp/05.jpg)

### Adicionando um usuário

Agora que já temos o bucket e a role criadas podemos adicionar um usuário. Na tela de gerenciamento do serviço SFTP dê um nome ao usuário e selecione a role que criamos. em policy deixe none pois será herdada da role e em home directory selecione o bucket criado.

Recomendo que crie uma pasta dentro do bucket exclusiva para este usuário, informe essa pasta na caixa de texto optional folder e marque a opção restricted, de forma que o usuário tenha acesso apenas a esta pasta.

![](/assets/img/sftp/03.jpg)

Será necessário também adicionar a chave pública deste usuário para que ele realize o acesso. Com essas informações clique no botão add e o usuário será criado.

Agora é só enviar os arquivos que deseja armazenar no seu sftp.

```
marcelo@evildead:~$ sftp xablau@sftp.evildead.com.br
Warning: Permanently added the RSA host key for IP address '00.00.00.00' to the list of known hosts.
Connected to sftp.evildead.com.br.
sftp> put testfile.txt
Uploading testfile.txt to /testfile.txt
testfile.txt 100% 0 0.0KB/s 00:00
sftp> ls
testfile.txt
sftp> exit
```
