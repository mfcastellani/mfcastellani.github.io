---
layout: post
author: marcelo castellani
title: Diferença de datas, encadeamento de métodos e importação de estruturas
tags: Rust
description: Neste artigo vamos calcular a diferença de datas para obtermos o número de dias vividos. Falamos sobre Duration e encadeamento de métodos.
---
‎¡Hola!

Vamos de Rust, parte 3? No nosso último episódio vimos o Dr. Gori... ops, vimos o [começo de nosso projetinho de cálculo de dias vividos](https://mfcastellani.github.io/2020/07/28/everyday-rust-08.html). De quebra falamos sobre o super maneiro `Result`, sobre como desempacotar dados de dentro dele e sobre a conversão de tipos básicos de Rust. Pra dar continuidade em nosso trabalho vamos de Milquetoast, do sensacional Helmet, uma das preferidas aqui de casa.

<p></p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/lEnMb4m9lu0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Continuando de onde paramos

Vamos dar continuidade no que desenvolvemos no artigo anterior, então [se não viu, veja aqui](https://mfcastellani.github.io/2020/07/28/everyday-rust-08.html) pois sem o que fizemos por lá ficará impossível de faz o que apresento aqui.

A primeira funcionalidade que vamos implementar é de calcular quantos dias você já viveu. No artigo anterior mostrei como imprimir na tela uma mensagem e receber os dados, e também como converter o valor recebido em um inteiro de 16 bits. Vou fazer em breve um artigo falando sobre tipos inteiros no Rust, o que significa esses 8, 16 e 32 bits, e os tipos assinalados e sem sinal. Mas por enquanto não se preocupe com isso.

### Criando uma data com os valores de ano, mes e dia

Como já temos os valores de ano, mês e dia vamos agora criar uma data com eles. Precisei mudar os tipos i16 para u32 e i32 pois é o que o método que vou usar esperava, mas tirando o formato do dado todo o resto ficou como no projeto anterior.

Para criar a data de nosso nascimento, chamada `birth`, eu utilizei o método `ymd` de nosso pacote `Local`. Veja:

```
let birth = Local.ymd(int_year, int_month, int_day);
```

Agora temos o dia de hoje e o dia do nascimento, basta verificar a diferença entre eles. Para isso usamos o método `signed_duration_since`, que irá retornar uma estrutura `Duration`, que é uma estrutura que possibilita um valor em nanosegundos formatado de acordo com a ISO 8601. Você encontra [mais detalhes sobre a estrutura Duration aqui](https://docs.rs/chrono/0.3.0/chrono/struct.Duration.html).

A `Duration` nos dá alguns métodos interessantes para converter esse monte de nanosegundos em outras medidas, como o `num_days`, que transforma a `Duration` em um inteiro (i64) que representa o número de dias daquela duração. Existe métodos para converter uma `Duration` em número de semanas, meses, anos, entre outros.

```
let duration = today.signed_duration_since(birth);
println!("E ja viveu {} dias!", duration.num_days());
```

Ok, podemos transformar essas duas linhas em apenas uma, assim:

```
println!("E ja viveu {} dias!", today.signed_duration_since(birth).num_days());
```

É normal e perfeitamente aceitável encadear métodos em Rust.

Outras mudanças realizadas no código anterior inclui uma otimização em nossas importações.

```
use chrono::{TimeZone, Local};
use std::io;
```

Como a construção `use chrono::{estruturas}` eu posso dizer exatamente quais estruturas eu desejo importar, sendo que posso adicionar várias dentro das chaves, separadas por vírgulas. Nosso código final ficará assim:

```
use chrono::{TimeZone, Local};
use std::io;

fn main() {
    // variáveis
    let mut day = String::new();
    let mut month = String::new();
    let mut year = String::new();
    let today = Local::now().date();

    // ler os dados a partir do prompt
    println!("Vamos ver quantos dias você já viveu?");

    println!("Digite o seu dia de nascimento (apenas o dia, 1de 1 a 31):");
    io::stdin().read_line(&mut day).expect("Dia invalido");

    println!("Digite o seu mês de nascimento (apenas o mês, de 1 a 12):");
    io::stdin().read_line(&mut month).expect("Mes invalido");

    println!("Digite o seu ano de nascimento (apenas o ano, com quatro digitos, por exemplo 1977):");
    io::stdin().read_line(&mut year).expect("Ano invalido");

    // com pop e turbofish
    day.pop();
    let int_day = day.parse::<u32>().unwrap();

    // com pop e tipo de variável anotado
    month.pop();
    let int_month: u32 = month.parse().unwrap();

    // com truncate
    year.truncate(year.len() - 1);
    let int_year = year.parse::<i32>().unwrap();

    // criamos a data de nascimento
    let birth = Local.ymd(int_year, int_month, int_day);

    println!("Voce nasceu no dia {}, do mes {}, do ano {}!", int_day, int_month, int_year);
    println!("E ja viveu {} dias!", today.signed_duration_since(birth).num_days());
}
```

### O que ainda falta?

Nosso código ainda vai quebrar se você digitar letras numa das datas. No próximo artigo da série vou falar sobre como evitar isso.
