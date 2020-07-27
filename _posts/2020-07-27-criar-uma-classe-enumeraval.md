---
layout: post
author: marcelo castellani
title: Enumeradores em Ruby - parte 2
tags: Ruby
description: Neste artigo mostro como adicionar a sua classe os métodos map, each, collect e select.
---
‎¡Hola!

Tudo tranquilo? Espero que sim! Vamos falar de enumeradores em Ruby mais uma vez, que tal? E pra dar ritmo a nosso papo vamos de Limp Bizkit!.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Dn8vzTsnPps" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p></p>

### Enumerators, again!

Neste artigo vamos dar sequencia a [este artigo aqui](https://mfcastellani.github.io/2020/07/22/enumeradores-em-ruby.html) onde falamos sobre como funcionam os métodos `each`, `map`, `collect` e `select`. Neste post, dado sequencia ao anterior, vamos criar uma classe marota e adicionar estes métodos a ela. Essa nossa classe será uma lista agenda de nome e telefone.

Antes, porém, vamos criar uma classe que tratará os dados dos contatos na agenda. No caso vamos ter apenas o nome e o telefone dessas pessoas.

```
class Person
  attr_accessor :name, :phone

  def initialize(name, phone)
    self.name = name
    self.phone = phone
  end
end
```

Até ai nenhuma surpresa. Vamos agora criar nossa `Agenda`. Ela será um tanto quanto limitada, recebendo em seu `initialize` duas pessoas, não podendo ter outros telefones. Poderíamos utilizar um array aqui, mas o array já tem o `Enumerable` implementado, então aí estaríamos aprendendo necas de pitibiribas como dizia meu saudoso avô Aldo.

```
class Agenda
  include Enumerable

  attr_accessor :person1, :person2

  def initialize(person1, person2)
    self.person1 = person1
    self.person2 = person2
  end

  def each
    yield person1
    yield person2
  end
  end
```

Perceba que implementamos um método `each` maroto alí, além de incluirmos o módulo `Enumerable`. Essas são as duas coisas necessárias para ganharmos um monte de métodos bonitos em nossa classe. Vamos iniciar uma nova agenda agora:

```
> agenda = Agenda.new(
>   Person.new("Nicolas", "11923456789"),
>   Person.new("Marcelo", "11912345678"))
```

Parece pouca coisa mas fizemos muito aqui. Temos a condição de usar o método `map` (ou `collect`), por exemplo:

```
> puts agenda.map { |person| person.name }
Marcelo
Nicolas
```

Ou o método `select`:

```
> puts agenda.select { |person| person.name == "Marcelo" }
#<Person:0x000056503ede9ce0>
```

Louco não? E como sempre, em Ruby resolvemos tudo com poucas linhas de código da melhor forma. No próximo artigo vou falar sobre como usar o modulo `Comparable`.
