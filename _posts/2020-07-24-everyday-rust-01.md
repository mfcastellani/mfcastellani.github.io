---
layout: post
author: marcelo castellani
title: Instalando o compilador do Rust e outras ferramentas relacionadas, e, claro, um HELLO WORLD!
tags: Rust
description: Neste artigo apresento uma introdução as ferramentas necessárias para se utilizar o Rust, como o rustup, cargo e o crates.io.
---
‎¡Hola!

Bora falar de Rust, no sapatinho? A ideia é ter um capitulo a cada x tempos, onde x depende de minha disponibilidade e vontade. Mas o objetivo é ser um passo a passo construindo conhecimento. E pra embalar esse artigo vamos de Gods and Punks, do Monster Magnet.

<p></p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/347M0hcgjcw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Mas que caralhas é Rust?

Quando eu escrevi o meu livrinho de Rust publicado pela casa do código, e que voce [pode comprar aqui](https://www.casadocodigo.com.br/products/livro-rust) eu era apenas um jovem Marcelo empolgado com a linguagem e a estudando duas a tres horas por dia. Ainda não tinha nenhuma experiência profissional a fundo com ela, apenas umas clis marotas e nada mais. Ai as coisas mudaram.

Eu trabalhei com Rust por um ano e pouco num de meus últimos empregos. Era o responsável por um dos inúmeros serviços que a empresa tinha, a maioria feito em Rust, e me chamaram por que precisavam de devs pra linguagem. Eu embarquei de corpo e alma, mergulhei de cabeça e posso dizer que foi uma das melhores deciões que tomei em muito tempo.

Quando comecei a estudar Rust eu acreditava que a linguagem era uma boa opção ao C e C++ por sua maneira revolucionária de gerenciar memória. E quem falava isso não era eu, mas sim um dos caras que eu mais admiro em computação, o Andrei Alexandrescu, uma das mentes por detrás da programação genérica e da linguagem D (ainda vou falar de D aqui neste blog por que é uma baita linguagem que não teve a atenção necessária). Andrei entrou em minha vida quando li [essa pérola que ele escreveu](https://en.wikipedia.org/wiki/Modern_C%2B%2B_Design).

Foi [neste artigo de 2015](https://www.quora.com/Which-language-has-the-brightest-future-in-replacement-of-C-between-D-Go-and-Rust-And-Why) que Andrei me despertou a atenção ao Rust. E eu quando comecei a estudar a linguagem me impressionei com sua performance. Rust é uma *linguagem compilada*, com *tipagem estática* e um *gerenciamento de memória decente* que não faz uso de *coletor de lixo*. Foi desenhada para ser *rápida*, *estável* e principalmente *segura*, quando falamos de threads num universo cada vez de computação distribuida.

Tudo isso é lindo, até você começar a usar a linguagem e apanhar para caraleo do compilador. Principalmente pela forma como Rust gerencia memória. Mas isso é assunto pra outro artigo. O que quero falar aqui é que a beleza de Rust é simplesmente funcionar. Em todo o tempo que trabalhei com a linguagem eu não tive problemas em produção. O código que eu escrevia, ao funcionar uma vez, funcionava da mesma forma, consumindo o mesmo número de recursos para sempre. Sem as surpresas de estouro de memória do C, sem o aumento de consumo de memória que encontramos em Ruby ou Java. Sem ponteiros perdidos, sem dores de cabeça. Rust é focada em funcionar. E posso te garantir, funciona muito bem.

### Ok, mas e ai?

Dito isso vamos ao motivo da existencia dessa página aqui. Meu objetivo é trazer um pouco de Rust sempre que possível, idealmente uma vez a cada dois ou tres dias (até por que quero escrever sobre outras coisas também). E hoje vou começar bem do começo, falando de `rustup`, `cargo` e como criar um projetinho básico, como importar uma biblioteca *rustica* (se não sabe o que são bibliotecas [veja esse meu lindo artigo aqui](https://mfcastellani.github.io/2020/07/23/biblioteca-compartilhada-c-gcc.html)) e como compilar a parada. Bora lá?

### Instalando o básico

Tudo o que você precisa instalar pra criar, compilar e rodar um programa escrito em Rust em sua máquina é instalado com o [rustup](https://rustup.rs/). Para instalar voce precisa apenas rodar o comando a seguir:

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

E seguir as instruções na tela. Sem segredos. Para confirmar se o Rust está instalado verifique com:

```
$ rustc --version
rustc 1.45.0 (5c1f21c3b 2020-07-13)

$ cargo --version
cargo 1.45.0 (744bd1fbb 2020-06-15)
```

Ou com

```
$ rustup show
Default host: x86_64-unknown-linux-gnu
rustup home:  /home/marcelocastellani/.rustup

installed toolchains
--------------------

stable-x86_64-unknown-linux-gnu
nightly-x86_64-unknown-linux-gnu
1.30.0-x86_64-unknown-linux-gnu

active toolchain
----------------

stable-x86_64-unknown-linux-gnu (default)
rustc 1.45.0 (5c1f21c3b 2020-07-13)
```

### O que será instalado?

O rustup irá instalar para você o `rustc`, que é o compilador do Rust, o `cargo` que é o gerenciador de dependencias e projetos do Rust, e outras ferramentas que não vamos dar atenção agora.

Com essas ferramentas já conseguimos criar um projeto Rust do zero, com o comando a seguir:

```
$ cargo new er-01
     Created binary (application) `er-01` package
```

Esta linha irá criar a estrutura de arquivos e pastas abaixo:

```
$ tree er-01/
er-01/
├── Cargo.toml
└── src
    └── main.rs

1 directory, 2 files
```

O arquivo `Cargo.toml` é o arquivo de configuração de nosso projeto, e o arquivo `main.rs`, dentro da pasta `src` é o arquivo de código principal de nosso projeto até agora. Se você der uma olhada nestes arquivos o conteúdo deles será:

#### Cargo.toml

```
[package]
name = "er-01"
version = "0.1.0"
authors = ["Marcelo Castellani <marcelo@linux.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

Neste arquivo temos uma seção `package`, onde temos a definição do nome de nosso projeto, a versão, os autores e a edição do Rust que estamos usando. Mas o que seria essa tal de *edição*, ou *edition*? Parafraseando [este link aqui](https://doc.rust-lang.org/edition-guide/editions/index.html):

> Os novas versões de Rust são liberadas em um ciclo de seis semanas.
> Isso significa que os usuários recebem um fluxo constante de novos
> recursos e que isso ocorre muito mais rápido que as atualizações de
> outras linguagens, mas também significa que cada atualização é menor.
> Depois de um tempo, todas essas pequenas mudanças se somam mas, de um
> lançamento para o outro, pode ser difícil olhar para trás e dizer
> "Uau, entre o Rust 1.10 e o Rust 1.20, o Rust mudou muito!"

<p></p>

> A cada dois ou três anos, estaremos produzindo uma nova edição do Rust.
> Cada edição reúne os recursos que chegaram a um pacote claro, com
> documentação e ferramentas totalmente atualizadas. Novas edições são
> enviadas através do processo usual de lançamento.
>
> Isso serve a propósitos diferentes para pessoas diferentes:
>
> * Para usuários ativos do Rust, ele reúne alterações incrementais em
> um pacote fácil de entender.
>
> * Para não usuários, isso indica que alguns grandes avanços foram
> alcançados, o que pode fazer com que Rust valha outra olhada.
>
> * Para aqueles que desenvolvem o Rust, ele fornece um ponto de partida
> para o projeto como um todo.

Hoje temos duas grandes edições do Rust, a 2015 e a 2018. Dessa forma quando definimos que usamos a edição 2018 do Rust em nosso projeto dizemos que estamos usando a última edição. Provavelmente teremos uma nova edição em 2021.

#### main.rs

Os arquivos de código do Rust possuem a extensão .rs como padrão.

```
fn main() {
    println!("Hello, world!");
}
```

No caso o cargo gerou para nós o código de um hello world maroto. Para o executar basta:

```
$ cargo run
   Compiling er-01 v0.1.0 (/home/marcelocastellani/data/pessoal/er-01)
    Finished dev [unoptimized + debuginfo] target(s) in 0.76s
     Running `target/debug/er-01`
Hello, world!
```

O cargo compilou nosso código e gerou um binário em target, debug. Após isso ele executou esse binário pra gente.

```
$ tree .
.
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target
    └── debug
        ├── build
        ├── deps
        │   ├── er_01-bb5b7fbf52598e52
        │   └── er_01-bb5b7fbf52598e52.d
        ├── er-01
        ├── er-01.d
        ├── examples
        └── incremental
            └── er_01-2z955ctw73gu4
                ├── s-fplkouq321-g5i22w-28erqaprfssfi
                │   ├── 20rss042ainquxri.o
                │   ├── 2e5ylhx4w4fas7b0.o
                │   ├── 3o0zs5uguve3omue.o
                │   ├── 3xhn0psaouh34573.o
                │   ├── 4b96xegrs6e3qh9s.o
                │   ├── a064u4m7o7voukn.o
                │   ├── dep-graph.bin
                │   ├── query-cache.bin
                │   └── work-products.bin
                └── s-fplkouq321-g5i22w.lock
```

Uma coisa importante, quando voce passa a ter muitos projetos Rust em sua máquina é interessante se habituar a usar o comando `cargo clean`, para limpar todos esses arquivos de compilação incremental gerados pois dependendo do tamanho de seu projeto eles passam a ocupar bastante espaço.

### Adicionando uma biblioteca

Vamos agora adicionar uma biblioteca para imprimir a data e hora atual, ao invés da mensagem hello world. Para isso usaremos uma biblioteca disponível no repositório global de bibliotecas Rust, o [crates.io](https://crates.io/). A biblioteca, ou crate para ficar mais `rustacean` será a `chrono`, [disponível aqui](https://crates.io/crates/chrono).

Primeiro precisamos a adicionar em nosso `Cargo.toml` na seção de dependências:

```
[dependencies]
chrono = "0.4"
```

Agora basta executar o comando `cargo build` para que a biblioteca seja baixada e compilada localmente:

```
$ cargo build
    Updating crates.io index
  Downloaded num-integer v0.1.43
  Downloaded libc v0.2.73
  Downloaded num-traits v0.2.12
  Downloaded chrono v0.4.13
  Downloaded 4 crates (712.4 KB) in 0.98s
   Compiling autocfg v1.0.0
   Compiling libc v0.2.73
   Compiling num-traits v0.2.12
   Compiling num-integer v0.1.43
   Compiling time v0.1.43
   Compiling chrono v0.4.13
   Compiling er-01 v0.1.0 (/home/marcelocastellani/data/pessoal/er-01)
    Finished dev [unoptimized + debuginfo] target(s) in 25.41s
```

Perceba que adicionamos apenas uma dependência, mas foram baixadas diversas outras, que certamente são relacionadas entre si. Agora vamos modificar o código de nosso projeto para mudar a mensagem de saida.

```
use chrono::prelude::*;

fn main() {
    let local: DateTime<Local> = Local::now();

    println!("{}", local);
}
```

Pronto, agora basta mandar um `cargo run`

```
$ cargo run
   Compiling er-01 v0.1.0 (/home/marcelocastellani/data/pessoal/er-01)
    Finished dev [unoptimized + debuginfo] target(s) in 0.19s
     Running `target/debug/er-01`
2020-07-24 17:20:58.407261814 -03:00
```

### O que fizemos?

Dizemos para o compilador que vamos usar o `chrono::prelude::*` através da diretiva `use`. Depois criamos uma variável que recebe a data e hora atual e a imprimimos. Para definir uma variável usamos o `let` seguido do nome da variável. Podemos, opcionalmente, declarar o seu tipo com o sinal de : e o tipo, e atribuímos algum valor com o `=`.

A declaração do tipo é opcional, isso quer dizer o código abaixo funciona da mesma forma pois o compilador infere qual o tipo a partir do retorno da atribuição. Vale destacar que é recomendado declarar o tipo sempre que não for atribuir o tipo na definição e quando for uma variável mutável (veremos isso em outro artigo).

```
use chrono::prelude::*;

fn main() {
    let local = Local::now();

    println!("{}", local);
}
```

Veja:

```
$ cargo run
   Compiling er-01 v0.1.0 (/home/marcelocastellani/data/pessoal/er-01)
    Finished dev [unoptimized + debuginfo] target(s) in 0.18s
     Running `target/debug/er-01`
2020-07-24 17:24:59.674791755 -03:00
```

Legal não? Bom, por hoje fico por aqui. Vou mandar mais artigos sobre Rust nos próximos dias. Fica de olho aqui no blog.
