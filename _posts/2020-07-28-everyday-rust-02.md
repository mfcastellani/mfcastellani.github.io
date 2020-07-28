---
layout: post
author: marcelo castellani
title: Everyday Rust, dia 02
tags: Rust
description: Neste projeto dou continuidade ao que fizemos na artigo anterior focando mais em código e menos em ferramentas.
---
‎¡Hola!

Vamos de Rust, parte 2? Depois de termos criado um projetinho do zero [no último artigo](https://mfcastellani.github.io/2020/07/24/everyday-rust-01.html) vamos agora fazer algo um pouco mais complexo, que tal? E pra isso vamos de Black Rebel Motorcycle Club, uma das preferidas aqui de casa, com a sensacional Beat the devil's tatoo.

<p></p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/mYkhNWIdra0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Começando de onde paramos

No artigo anterior criamos um projeto que imprimia a data e hora atual na saída padrão. Hoje vamos dar continuidade a este projeto adicionando mais código e explicando linha a linha o que fizemos. Nosso objetivo no final deste artigo será receber a data de nascimento de um usuário e a converter para uma data. Este será um artigo em duas partes, e no próximo iremos calcular quantos dias a pessoa já viveu.

Importante, pra ninguém ficar me xingando no Twitter, este artigo visa a legibilidade e evita construções complexas do Rust, que poderiam eliminar o número de linhas. Também é um artigo para iniciantes onde tenho como base que a pessoa conheça programação mas está começando com Rust agora, então não usarei construtores que não foram apresentados ainda nesta sequência de artigos.

Se você não tem os arquivos do projeto anterior recomendo que [leia o último artigo](https://mfcastellani.github.io/2020/07/24/everyday-rust-01.html) pois vamos precisar deles aqui.

### O que fizemos?

Nosso projeto anterior faz uso do [crate chrono](https://crates.io/crates/chrono), que é uma versão com esteróides da biblioteca `time` padrão do Rust. Essa biblioteca `chrono` possibilita um uso mais simples de data e hora (um grande problema da maior parte das linguagens de progração que eu trabalhei até hoje). Para a usar declaramos a dependência em nosso arquivo `Cargo.toml` (onde estão as definições do projeto):

```
[dependencies]
chrono = "0.4"
```

E em nosso arquivo de código fonte nós dizemos ao compilador para usar o `prelude` dessa biblioteca através do `use`. Recomendo dar uma lida rápida [nesta documentação sobre preludes](https://doc.rust-lang.org/std/prelude/index.html) para mais detalhes.

```
use chrono::prelude::*;
```

Temos na sequência então um método, o `main`. Métodos em Rust são definidos com `fn`, o nome e a lista de parâmetros que ele recebe entre `()`. O código do método em sí inicia após a `{`.

```
fn main() {
```

Na sequência definimos uma variável que recebe a data e hora local do momento da execução através do método `now()`. Nossa variável não é mutável, e vamos ver logo menos mais sobre isso:

```
let local = Local::now();
```

E então imprimimos usando a macro `println!` o valor que atribuimos. Uma macro é uma declaração de um grupo de instruções que será expandido em tempo de compilação, ou seja, não existe um método `println!`, mas sim uma sequência de métodos que é agrupado dentro de `println!` e que serão expandidos na compilação. Fazendo aquele merchan, tem um artigo supimpa sobre macros [no meu livro](https://www.casadocodigo.com.br/products/livro-rust), mas me comprometo a falar sobre isso em mais detalhes aqui. A macro recebe uma string de formação, no caso `{}`, e o parametro que será impresso. Na prática o {} será substituído pelo valor dentro da variável local, no caso a data e hora atual.

```
println!("{}", local);
```

Tudo isso faz isso:

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.21s
     Running `target/debug/er-01`
2020-07-28 15:10:08.293360787 -03:00
```

### Lendo a data de nascimento do usuário

Vamos agora receber a data de nascimento do usuário. Para facilitar vamos receber primeiro o dia, depois o mês e depois o ano. Para isso vamos ter três variáveis mutáveis. Por padrão, em Rust, variáveis são imutáveis, ou seja, definiu um valor já era. Para dizer que a variável é mutável usamos a palavra reservada `mut`. Ao invés de declarar `let variavel` declaramos `let mut variavel`. Vamos também usar o método `read_line` da biblioteca `std::io`, para ler a entrada de dados. Nosso código dessa primeira parte ficará assim:

```
use chrono::prelude::*;
use std::io;

fn main() {
    // variáveis
    let mut day = String::new();
    let mut month = String::new();
    let mut year = String::new();
    let local = Local::now();

    // ler os dados a partir do prompt
    println!("Vamos ver quantos dias você já viveu?");

    println!("Digite o seu dia de nascimento (apenas o dia, de 1 a 31):");
    io::stdin().read_line(&mut day);

    println!("Digite o seu mês de nascimento (apenas o mês, de 1 a 12):");
    io::stdin().read_line(&mut month);

    println!("Digite o seu ano de nascimento (apenas o ano, com quatro digitos, por exemplo 1977):");
    io::stdin().read_line(&mut year);
}
```

Veja que nós passamos para o método `read_line` a variável definida anteriormente da seguinte forma: `read_line(&mut year)`. O `&mut` é uma referência de empréstimo, ou seja, nós pegamos uma variável que pertence a `main` (no caso `day`, `month` e `year`) e emprestamos para o método `read_line`. Se as variáveis fossem formas de bolo seria como se falassemos: caso amigo `read_line`, aqui está a minha forma de bolo `year`, use-a. Eventualmente nossa forma pode vir com um bolo dentro.

### Um montão de alertas (warnings)

Ao executar este código com o `cargo run` teremos alguns alertas do compilador.

```
$ cargo run
   Compiling er-01 v0.1.0 (/home/marcelocastellani/data/pessoal/er-01)
warning: unused `std::result::Result` that must be used
  --> src/main.rs:15:5
   |
15 |     io::stdin().read_line(&mut day);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: `#[warn(unused_must_use)]` on by default
   = note: this `Result` may be an `Err` variant, which should be handled

(...)
```

Isso por que o [método read_line](https://doc.rust-lang.org/std/io/struct.Stdin.html#method.read_line) retorna um `Result`. Este é um tipo baseado em um enumerador que normalmente é usado em operações que podem dar ruim, e ele retornar um sucesso ou um erro. Seu formato é:

```
enum Result<T, E> {
   Ok(T),
   Err(E),
}
```

Métodos em Rust normalmente retornar `Result` quando o erro é recuperável. Imagine, por exemplo, você tentar abrir um arquivo que não existe. Se o arquivo não existir sua aplicação pode criar o arquivo ou informar ao usuário que o arquivo não existe. Não precisa morrer. Dessa forma vamos usar o método `expect()` encadeado ao `read_line` para que, caso recebamos um erro, uma mensagem seja apresentada e a aplicação não quebre. Você pode ler mais sobre [Result neste link](https://doc.rust-lang.org/std/result/).

Nosso código ficará assim:

```
println!("Digite o seu dia de nascimento (apenas o dia, de 1 a 31):");
io::stdin().read_line(&mut day).expect("Dia invalido");

println!("Digite o seu mês de nascimento (apenas o mês, de 1 a 12):");
io::stdin().read_line(&mut month).expect("Mes invalido");

println!("Digite o seu ano de nascimento (apenas o ano, com quatro digitos, por exemplo 1977):");
io::stdin().read_line(&mut year).expect("Ano invalido");
```

### Convertendo strings em números

Quando lemos os dados de dia, mês e ano de nosso usuário nós armazenamos os mesmos numa variável do tipo `String`. Vamos precisar pegar o dado dentro dessa variável e converter para um valor numérico. Podemos fazer isso com o método `parse` de duas formas: uma através de um operador chamado `turbofish`, no caso esse cara aqui `::<>`, ou através da anotação de tipo como já vimos no artigo anterior. Em ambos os casos vamos ter um Result, então precisaremos o tratar de alguma forma.

A conversão deverá resultar num [dos tipos numéricos válidos](https://doc.rust-lang.org/reference/types/numeric.html) do Rust. No caso usaremos um inteiro de 16 bits, o suficiente para comportar o ano.

Um outro ponto importante, ao usarmos o `read_line` foi lido tudo o que foi digitado. Isso quer dizer que se digitamos o número 30 e pressionamos ENTER para o dia dentro de nossa variável `day` vamos ter `30\n`. E o barra ene vai dar problema quando tentarmos o converter para um número. Dessa forma precisamos antes da conversão remover o último caractere de nossas strings.

Isso pode ser feito com o método `pop()`, ou com o método `truncate(x)`, ambos de `String`. O primeiro remove o último caractere, o segundo mantém x caracteres. Por exemplo, para remover o barra ene da variável dia usamos:

```
day.pop();
```

Poderiamos fazer com truncate, dizendo para ele manter o tamanho menos um, que no caso é o barra ene maledeto no final da string:

```
day.truncate(day.len() - 1);
```

O método `len()` nos retorna o tamanho da String, como já era de se esperar.

Para converter, após remover o barra ene, podemos fazer com o turbofish `day.parse::<i16>()` ou anotando a variável, como em: `let int_day: i16 = day.parse()`. Como já disse em ambos os casos vamos ter um `Result`. Vamos assumir que nossas conversões funcionam lindamente e usaremos direto o método `unwrap()`, que desempacota o resultado de sucesso do `Result`. Nosso código ficará assim:

```
use chrono::prelude::*;
use std::io;

fn main() {
    // variáveis
    let mut day = String::new();
    let mut month = String::new();
    let mut year = String::new();
    let local = Local::now();

    // ler os dados a partir do prompt
    println!("Vamos ver quantos dias você já viveu?");

    println!("Digite o seu dia de nascimento (apenas o dia, de 1 a 31):");
    io::stdin().read_line(&mut day).expect("Dia invalido");

    println!("Digite o seu mês de nascimento (apenas o mês, de 1 a 12):");
    io::stdin().read_line(&mut month).expect("Mes invalido");

    println!("Digite o seu ano de nascimento (apenas o ano, com quatro digitos, por exemplo 1977):");
    io::stdin().read_line(&mut year).expect("Ano invalido");

    // com pop e turbofish
    day.pop();
    let int_day = day.parse::<i16>().unwrap();

    // com pop e tipo de variável anotado
    month.pop();
    let int_month: i16 = month.parse().unwrap();

    // com truncate
    year.truncate(year.len() - 1);
    let int_year = year.parse::<i16>().unwrap();

    println!("Voce nasceu no dia {}, do mes {}, do ano {}!", int_day, int_month, int_year);
}
```

Executando:

```
$ cargo run
warning: unused variable: `local`
 --> src/main.rs:9:9
  |
9 |     let local = Local::now();
  |         ^^^^^ help: if this is intentional, prefix it with an underscore: `_local`
  |
  = note: `#[warn(unused_variables)]` on by default

warning: 1 warning emitted

    Finished dev [unoptimized + debuginfo] target(s) in 0.01s
     Running `target/debug/er-01`
Vamos ver quantos dias você já viveu?
Digite o seu dia de nascimento (apenas o dia, de 1 a 31):
30
Digite o seu mês de nascimento (apenas o mês, de 1 a 12):
6
Digite o seu ano de nascimento (apenas o ano, com quatro digitos, por exemplo 1977):
1977
Voce nasceu no dia 30, do mes 6, do ano 1977!
```

Ainda temos um alerta em nosso código pois não estamos usando a variável `local` (ela será usada no próximo artigo). E este código pode ser facilmente quebrado bastando digitar letras ao invés de números na entrada, veja:

```
Digite o seu ano de nascimento (apenas o ano, com quatro digitos, por exemplo 1977):
asd
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: ParseIntError { kind: InvalidDigit }', src/main.rs:24:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

No próximo artigo sobre Rust vamos calcular a diferença de dias entre o nascimento e o dia atual. Também vamos tratar de uma maneira melhor o `Result`. Salve seu projetinho com muito amor que ele ainda será bem mexido.
