---
layout: post
author: marcelo castellani
title: Criando uma shared library em C com o GCC
tags: C
description: Neste artigo mostro como criar e compilar uma biblioteca compartilhada em C, usando o gcc, e como a utilizar em outro projeto.
---
‎¡Hola!

Vamos falar de C hoje rapaziada linda. E pra acompanhar este post deixo uma música de minha banda, a Vingança Vil.

<p></p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/z2ZzheCoyyM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p></p>

### Bibliotecas, ou livrarias

As bibliotecas, ou como diz um amigo meu, livrarias, são coletâneas de código existentes, com funções prontas para seu uso. São um dos conceitos base do reaproveitamento de código e, assim como bactérias em seu intestino e ácaros no carpete do escritório de sua empresa, elas estão ai em seu computador neste exato momento.

Bibliotecas são peças chave de software. Como disse, elas lhe entregam funções prontas para seu uso, agrupadas normalmente dentro de um contexto. Voce pode usar, por exemplo, uma biblioteca para acesso a um banco de dados. Ela lhe dará métodos para se conectar, para ler dados, gerenciar um pool de conexões entre outros. Ou uma biblioteca para desenhar interfaces gráficas, como o Gtk por exemplo, que lhe dará botões, caixas de texto e outros elementos prontos para o uso em seus projetos.

Neste artigo vou lhe mostrar como escrever uma biblioteca simples em C, como a compilar e como a utilizar em outro projeto C.

### Criando uma biblioteca

Nossa biblioteca será extremamente simples. Ela terá um método chamado `meow`, que imprimirá na tela o lindo gatinho a seguir:

```
  /\_/\  (  meaoooowwww!!!!
 ( ^.^ ) _)
   \'/  (
 ( | | )
(__d b__)
```

### O Código de nossa biblioteca

O objetivo deste projeto não é explicar o básico de C, então suponho que você saiba o que é um arquivo .h, um arquivo .c e etc... Vamos criar então um arquivo chamado `catlib.h` com o conteúdo a seguir:

```
#ifndef catlib_h__
#define catlib_h__

extern void meow(void);

#endif  //
```

Este arquivo define a interface de nossa biblioteca. Vamos agora definir o seu código, no arquivo `catlib.c`:

```
#include <stdio.h>

void meow(void)
{
    printf("  /\\_/\\  (  meaoooowwww!!!! \n");
    printf(" ( ^.^ ) _)\n");
    printf("   \\'/  ( \n");
    printf(" ( | | ) \n");
    printf("(__d b__) \n\n");
}
```

Agora precisamos compilar nosso projeto. Se voce não é familiar com o termo compilar é transformar o código que escrevemos em um código executável que o computador consiga entender. O primeiro passo é transformar o arquivo .c em um arquivo objeto, ou .o. Para isso vamos pedir para que o `gcc` nos informe de qualquer erro ou alerta de nosso código com os parametros `-Wall -Werror`.

Também compilaremos a biblioteca como um código de posição independente, com a flag `-fpic`. Isso quer dizer que esse código deve funcionar não importa em que posição de memória ele seja carregado. Isso é necessário pois uma biblioteca compartilhada pode ser usada por diversos programas e ela não pode adicionar seus dados em endereços fixo na memória visto que a localização de nossa bibliotecazinha na memória pode variar de programa para programa.

Para compilar usamos o comando a seguir:

```
$ gcc -c -Wall -Werror -fpic catlib.c
```

Isso irá gerar um arquivo .o:

```
$ ls
catlib.c catlib.h catlib.o
```

Agora precisamos transformar esse arquivo .o em uma biblioteca compartilhada. Novamente vamos usar o gcc, agora com as opções `-o` para informar o nome de nosso arquivo de saída, e `-shared` para dizer que desejamos criar uma biblioteca compartilhada. Um ponto importante, no Linux as bibliotecas são nomeadas libalgumacoisa.so, ou seja, vamos mudar o nome de `catlib` para `libcat` para respeitarmos esse padrão. O comando para fazer tudo isso é:

```
$ gcc -shared -o libcat.so catlib.o
```

E o resultado é que agora temos nossa biblioteca:

```
$ ls
catlib.c  catlib.h  catlib.o  libcat.so
```

Vamos agora copiar nossa bilioteca e nosso arquivo .h para as pastas padrões do sistema, ou seja, `/usr/lib` e `/usr/include`:

```
$ sudo cp libcat.so /usr/lib/
$ sudo cp catlib.h /usr/include/
```

Nossa biblioteca está pronta para ser usada.

### Usando a biblioteca

Vamos criar um código simples, que fará referência a nosso arquivo .h e chama o método meow:

```
#include <catlib.h>

int main(void)
{
    meow();
    return 0;
}
```

Repare que referenciamos o arquivo .h como `<catlib.h>`, o que quer dizer que o compilador irá o procurar na pasta padrão do sistema. Para compilar esse código, que eu chamei de `example.c` usamos a linha a seguir:

```
$ gcc -Wall -o example example.c -lcat
```

Novamente dizemos para nos retornar qualquer problema que ocorra com o `-Wall`, dizemos que o nosso arquivo compilado deverá se chamar `example` com o `-o` e dizemos para ele usar a biblioteca `cat` através da flag `-lcat`. Neste ponto voce poderá dizer "mas nossa lib não se chamava libcat.so????", sim, ela se chama `libcat.so`, mas o gcc sabe que toda lib tem seu nome iniciado por `lib`, então voce diz apenas o que importa e ele se vira marotamente. Isso se a biblioteca estiver na pasta padrão do sistema.

Vamos executar nosso programinha?

```
$ ./example
  /\_/\  (  meaoooowwww!!!!
 ( ^.^ ) _)
   \'/  (
 ( | | )
(__d b__)

```

Veja só que belezinha! Ai está o nosso gatinho e seu miado.

### Ldconfig e permissões de biblioteca

Simplesmente copiar a biblioteca para `/usr/lib` possibilita que nós a usemos, porém o ideal é dizer ao cache de bibliotecas do sistema que ela existe. Para isso usamos o `ldconfig`. Também é interessante tirarmos o direto de escrita nesta biblioteca de outros usuários, de forma que ela não seja substituida ou sobreescrita por outros.

Primeiro acertamos as permissões:

```
$ sudo chmod 0755 /usr/lib/libcat.so
```

Depois rodamos o `ldconfig` para ele atualizar o cache:

```
$ sudo ldconfig
```

Agora vamos verificar se nossa biblioteca está no cache do sistema:

```
$ sudo ldconfig -p | grep libcat.so
        libcat.so (libc6,x86-64) => /usr/lib/libcat.so
```

E lá está ela :)

### Verificando de onde nosso executável está lendo a biblioteca

Outro comando bem útil quando trabalhamos com bibliotecas compartilhadas é o `ldd`. Ele possibilita verificarmos quais bibliotecas nosso binário faz referencia e qual o path da biblioteca referenciada. Veja:

```
$ ldd example
        linux-vdso.so.1 (0x00007fff355c9000)
        libcat.so => /usr/lib/libcat.so (0x00007fa7962b0000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fa7960be000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fa7962dc000)
```

E lá está nossa biblioteca. Maneiro, não?
