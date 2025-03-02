# Posse (__Ownership__)

Rust não possui um __garbage collector__ como Java, Python ou Javascript, também não faz gerenciamento manual como C ou C++. Ela é uma linguagem __memory-safe__ que usa um método muito único chamado de __ownership__ que é verificado pelo seu famoso __borrow checker__ durante a compilação. Ou seja, um programa inseguro do ponto de vista de memória sequer pode ser compilado. Precisamos de entender o que é o sistema de posse do Rust e as regras do __borrow checker__ antes de poder continuar.

## Donos

A primeira coisa importante a se saber é que cada valor possui um dono

```rust
let ano = 2025;
let instituto = String::from("IME");
```

`ano` é dona de `2025` enquanto `instituto` é dono da string `"IME"`. Como cada valor tem um único dono, passar um valor para outra variável é abrir mão da posse do valor.

```rust
let ano = 2025;
let instituto = String::from("IME");

let ano2 = ano;
let instituto2 = instituto;
```

Porém existe um detalhe, valores muito pequenos como números, caracteres e booleanos são chamados de `Copy`, que em Rust quer dizer que é barato produzir uma cópia desse valor. No caso de valores `Copy`, a posse não é transferida, mas sim o valor é duplicado. Portanto `ano` e `ano2` tem como valor cópias de `2025`.

Mas strings não são `Copy`, uma string pode ter tamanho arbitrário, não dá pra saber se copiar ela vai ser barato ou não, por isso uma `String` é dita `Clone`. No exemplo acima, a posse da string `IME` é transferida de `instituto` para `instituto2`. Isso significa que desse ponto em diante `instituto` não tem valor definido e não pode ser usado.

```rust
fn main() {
    let instituto = String::from("IME");
    let instituto2 = instituto;

    println!("{} {}", instituto, instituto2);
}
```

O __borrow checker__ vai reprovar o programa acima durante a compilação pois estamos usando `instituto` após mover a posse do seu valor para outra variável.

```
error[E0382]: borrow of moved value: `instituto`
 --> src\main.rs:5:23
  |
2 |     let instituto = String::from("IME");
  |         --------- move occurs because `instituto` has type `String`, which does not implement the `Copy` trait
3 |     let instituto2 = instituto;
  |                      --------- value moved here
4 |
5 |     println!("{} {}", instituto, instituto2);
  |                       ^^^^^^^^^ value borrowed here after move
  |
  = note: this error originates in the macro `$crate::format_args_nl` which comes from the expansion of the macro `println` (in Nightly builds, run with -Z macro-backtrace for more info)
help: consider cloning the value if the performance cost is acceptable
  |
3 |     let instituto2 = instituto.clone();
  |                               ++++++++
```

Essa é a mensagem que o compilador emite informando o uso de um valor que foi "movido". Leia atentamente a essa mensagem de erro, pois ela é muito informativa. Ela nos diz:

1. O erro que cometemos (__borrow of moved value: 'instituto'__)
2. `String` não é `Copy`, portanto a posse é movida
3. Onde movemos a posse (na declaração de `instituto2`)
4. Onde tentamos usar `instituto` (na chamada de `println!`)
5. Uma sugestão de solução para considerar o uso de `.clone` para produzir uma cópia dessa string

Aplicando essa sugestão o código funciona

```rust
fn main() {
    let instituto = String::from("IME");
    let instituto2 = instituto.clone();

    println!("{} {}", instituto, instituto2);
}
```

```
IME IME
```

A grande importância do conceito de donos é que quando o dono de um valor sai de escopo o valor é liberado da memória. Similar a como um valor é apagado pelo __garbage collector__ quando ninguém o está referenciando ou quando se chama um `free`/`delete` em C/C++. O diferencial é que o __garbage collector__ é "custoso" e toma tempo de execução do programa, assim como você pode esquecer de dar um `free`. A vantagem é que a abordagem do Rust é automática e sem custos desnecessários em tempo de execução.

No exemplo acima, no `}` onde `instituto` e `instituto2` saem de escopo os seus valores são apagados da memória.

## Referências

Note o termo __borrow__ usado acima pelo compilador. Todo valor possui um dono e esse dono pode fazer empréstimos temporários por meio de referências sob certas circunstâncias.

Pense em referências como se fossem ponteiros seguros (Rust também possui ponteiros, mas não vamos falar deles aqui). São seguros pois eles atendem a um certo conjunto de regras.

- Existem referências de leitura(imutáveis) `&` e de escrita(mutáveis) `&mut`
- Não existem referências nulas
- Um empréstimo/referência não pode existir por mais tempo do que o valor emprestado/referenciado
- Pode-se ter infinitas referências imutáveis ou apenas uma única referência mutável (não se pode ter ambos)

Já demonstramos referências imutáveis anteriormente quando falamos de `String` e `&str` (string slice). Onde estavamos apenas referenciando um pedaço de uma string. Existem também `&String` e `&mut String` que são referências a strings inteiras.

No nosso exemplo acima, não precisavamos de fazer nada com o conteúdo de `instituto` quando criamos `instituto2`, poderiamos apenas pegar uma referência.

```rust
fn main() {
    let instituto: String = String::from("IME");
    let instituto2: &String = &instituto;

    println!("{} {}", instituto, instituto2);
}
```

```
IME IME
```

Com isso `instituto` continua existindo e `instituto2` é apenas uma referência imutável.

## Referências Mutáveis

Você certamente não gostaria que alguém pegasse sua caneta emprestada, trocasse a tampa e te devolvesse sem ter te avisado antes que iria fazer isso. Por isso existem `&mut`, elas avisam de antemão que irão modificar o valor que estão pegando emprestado. Vamos fazer uma função de exemplo para demonstrar isso.

```rust
fn cria_cumprimento(nome: &mut String) {
  *nome = format!("Olá, {}", nome);
}

fn main() {
  let mut nome = String::from("Paulo");
  cria_cumprimento(&mut nome);
  println!("{}", nome);
}
```

A função `cria_cumprimento` pede por uma referência mutável a uma string. Em seguida usa `*nome` para fazer a dereferência e trocar o valor para que inicie com a string `Olá, `. Na hora que estamos invocando a função, além de declarar a variável como `let mut`, precisamos de explicitamente passar `&mut nome` como argumento para a função. Estamos deixando explicito que aceitamos o "contrato" da função e estamos ciente que o valor de `nome` será modificado.

```
Olá, Paulo
```

Se `cria_cumprimento` usasse uma referência imutável como parâmetro, então o compilador emitiria um erro ao tentar derreferenciar `nome` e modificar seu valor.

```
error[E0594]: cannot assign to `*nome`, which is behind a `&` reference
 --> src\main.rs:2:5
  |
2 |     *nome = format!("Olá, {}", nome);
  |     ^^^^^ `nome` is a `&` reference, so the data it refers to cannot be written
  |
help: consider changing this to be a mutable reference
  |
1 | fn cria_cumprimento(nome: &mut String) {
  |                            +++
```

Leia bem a mensagem acima e note que no fim ela sugere a solução de usar uma `&mut` ao invés de uma `&`

A outra alternativa para não usar uma `&mut`, seria retornar o valor da string nova, ao invés de modificar o valor de entrada.

```rust
fn cria_cumprimento(nome: &str) -> String {
    return format!("Olá, {}", nome);
}
```

> `&str` e `&String` são diferentes, porém `&String` é automaticamente convertido em `&str`, por isso se usa `&str` para aumentar a flexibilidade da função.
> Apenas se usa `&String` se realmente for necessário

## __Lifetimes__

> Em breve

## Extra: Indicadores de Posse em Métodos

Usualmente em Rust um tipo pode ter muitos métodos com nomes quase identicos, sendo diferenciados por alguns sufixos/prefixos:

- `into_`: significa que o método toma posse do valor
- `as_`: vai tomar uma referência ao valor e o valor retornado também será uma referência condicionada ao tempo de vida do valor original
- `_mut`: vai usar uma referência mutável ao valor original e pode devolver uma referência mutável
- `to_`: geralmente vai tomar uma referência ao valor, o retorno pode ser tanto um valor quanto uma referência, mas raramente será uma referência mutável

Lembrando que isso não é uma regra, mas sim uma convenção seguida pela biblioteca padrão do Rust que pode, ou não, ser seguida por outras bibliotecas.
