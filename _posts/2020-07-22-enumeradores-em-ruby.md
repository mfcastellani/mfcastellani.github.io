---
layout: post
author: marcelo castellani
title: Enumeradores em Ruby
tags: Ruby
description: Neste artigo mostro como funcionam os métodos map, each, collect e select.
---
‎¡Hola!

Tudo tranquilo? Espero que sim! Vamos falar de enumeradores em Ruby, que tal? Mas antes, para acompanhar este pequeno e singelo artigo, coloque esse som dos Deliquent Habits para rolar.

<iframe width="560" height="315" src="https://www.youtube.com/embed/w3qqN1BMnhk"
frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
allowfullscreen></iframe>

<p></p>

### Each

O objetivo deste artigo é falar rapidamente sobre os métodos de enumeração do Ruby, como `each`, `map`, `collect` e outros. Minha ideia aqui é ter uma referência rápida e simples destes métodos.

Vamos entar começar com o `each`. Este método permite percorrer todos os itens de uma coleção e executar para cada item um bloco de código. Veja a seguir um exemplo. Temos um array com os número de 1 a 10, e vou usar o método `each` para pegar cada um desses itens e imprimi-lo:

```
> array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

> array.each { |item| puts item }
1
2
3
4
5
6
7
8
9
10
```

Uma variação do `each` é a versão com índice, o `each_with_index`, que adiciona o indice do array a nossa enumeração. Veja:

```
> array.each_with_index { |item, index| puts "#{index.to_s.rjust(2)} = #{item}" }
 0 = 1
 1 = 2
 2 = 3
 3 = 4
 4 = 5
 5 = 6
 6 = 7
 7 = 8
 8 = 9
 9 = 10
 => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

Repare que para cada item de nosso array o código do bloco foi executado. Mas e se nosso objetivo fosse manipular cada um dos itens e os coletar? O `each` não permite que os dados que foram manipulados sejam lidos, ele apenas passa o item para o bloco e segue a vida. Já o `map` nos dá essa possibilidade

<p></p>

### map e collect

Com o `map` ou `collect` (ambos fazem a mesma coisa) a história é diferente. Ele permite retornar um novo array com os resultados dos dados manipulados. Por exemplo, imagine que para cada item de nosso array com os números de 1 a 10 queremos efetuar um incremento?

```
> array2 = array.map { |item| item+= 1}

> array
 => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

> array2
 => [2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

O mesmo exemplo, com `collect`

```
> array3 = array.collect { |item| item += 1 }

> array3
 => [2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

Wow! Cada item de nosso array original, chamado criativamente de `array`, manteve-se intocado. E o resultado de cada processamento do bloco foi salvo em um novo array, chamado criativamente de `array2`.

Mas e se eu quiser selecionar itens de meu array? Por exemplo, imagine que eu queira um array apenas com os números pares?

<p></p>

### select

É ai que entra o `select`. Este maroto método permite que executemos uma condição em nosso array, e se ela for verdadeira o item é retornado para o novo array. Legal, hein?

```
> pares = array.select { |item| item.even? }

> pares
 => [2, 4, 6, 8, 10]
```

<p></p>

### Pra fechar...

Muito maneiro e simples de usar. No próximo artigo sobre Ruby aqui do brogui vou mostrar como voce pode definir uma classe em Ruby e tornar ela enumerável e ordenável. Vai ser altos foda.
