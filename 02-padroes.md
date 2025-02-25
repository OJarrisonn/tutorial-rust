# Casamento de Padrões (Pattern Matching)

Vamos usar uma estrutura especial do rust chamada `match`, ela funciona como um `if` com super poderes.

Mas antes vamos introduzir dois tipos fundamentais da linguagem: `Option` e `Result`.

## `Option<T>`

Não existem ponteiros nulos em Rust. Se algo pode ser nulo isso deve ficar explicito:

```rust
fn main() {
    let pessoas = ["Fulano", "Beltrano", "Ciclano"];
    let indice = 10;

    let alguem: Option<&&str>  = pessoas.get(indice);
    let fulano: Option<&&str>  = pessoas.get(0);

    dbg!(alguem);
    dbg!(fulano);
}
```

> A macro `dbg!` é útil para visualizar valores 

> Lembra que uma &str se refere aos caracteres de outra string? `.get` vai nos dar uma referência também, para evitar
> custos de cópia, por isso `&&str`, uma referência a uma &str ("Fulano", "Beltrano" ou "Ciclano")

A saída do programa acima é.:

```
[src/main.rs:8:5] alguem = None
[src/main.rs:9:5] fulano = Some(
    "Fulano",
)
```

Isso mesmo, nada de crashes, ou acessos à memória alheia.
Mas isso apenas com o método `.get`, usando `pessoas[indice]` vai crashar o programa.

Um `Option` pode ser `None` (nenhum valor) ou `Some` algum valor. Você pode usar `.unwrap` para extrair o valor
mas isso vai crashar o programa caso o valor seja `None`.

```rust
fn main() {
    let pessoas = ["Fulano", "Beltrano", "Ciclano"];
    let indice = 10;

    let fulano: Option<&&str>  = pessoas.get(0);
    let alguem: Option<&&str>  = pessoas.get(indice);

    dbg!(fulano.unwrap());
    dbg!(alguem.unwrap());
}
```

```
[src/main.rs:8:5] fulano.unwrap() = "Fulano"
thread 'main' panicked at src/main.rs:9:17:
called `Option::unwrap()` on a `None` value
```

A mensagem é clara: o programa entrou em pânico ao tentar usar `.unwrap` num `None`.

Sempre bom lembrar que `Option<T> != T`, portanto você preciisa de extrair o valor `T` do `Option` antes de usar em uma função que precise de `T`

```rust
fn main() {
    let none: Option<String> = None;
    let chars = none.chars();
}
```

`.chars` é um método de `String`, mas não é um método de `Option`. O programa acima não compila

## `Result<T, E>`

Similar ao `Option`, temos o `Result` que é usado para indicar sucesso ou falha ao executar uma operação.

```rust
use std::num::ParseIntError;

fn main() {
    let num: Result<i32, ParseIntError> = "42".parse::<i32>();
    let batata: Result<i32, ParseIntError> = "batata".parse::<i32>();

    dbg!(num);
    dbg!(batata);
}
```

A notação é assustadora, mas basicamente temos uma variável `num` que pode ser um `i32` ou um `ParseIntError` e isso tudo vai depender se a função `.parse` 
do tipo `String` consegue ou não fazer o parse da string `"42"` em um `i32`. Evidentemente a `"42"` pode ser convertido em um inteiro, o que não é o caso com `"batata"`

```
[src/main.rs:7:5] num = Ok(
    42,
)
[src/main.rs:8:5] batata = Err(
    ParseIntError {
        kind: InvalidDigit,
    },
)
```

`num` está `Ok`, mas `batata` é um `Err` do tipo dígito inválido, parece razoável. Podemos usar `.unwrap` para extrair o valor no caso `Ok` ou `.unwrap_err` se quiser obter o valor no caso `Err`.

## Usando `match`

Vamos ver qual é a forma idiomática de se tratar `Option` e `Result` em Rust

```rust
fn main() {
    let mut buffer: String = String::new();
    let result: Result<usize, std::io::Error> = std::io::stdin().read_line(&mut buffer);

    match result {
        Ok(len) => {
            println!("você digitou {} caracteres: {}", len, buffer);
        },
        Err(error) => {
            println!("Erro ao ler entrada: {}", error);
        }
    }
}
```