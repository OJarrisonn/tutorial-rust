# Sintaxe Básica

Abra seu `main.rs`. Vamos entender o que temos nele:

```rust
fn main() {
    println!("Hello, world!");
}
```

Temos uma função (`fn`) chamada `main`, sem parâmetros e sem retorno. O compilador do Rust vai usar ela
como ponto de início para o seu projeto. Dentro dela invocamos a _macro_ `println!` passando a string
`"Hello, world!"` como parâmetro. Não se preocupe com a difenreça entre uma macro e uma função por agora,
apenas lembre de usar `println!` e não `println`. Por fim, temos que usar um ponto e vírgula para encerrar o comando.

## Variáveis

Vamos criar algumas variáveis simples:

```rust
fn main() {
    let alunos: i32 = 60;
    let disciplina: &str = "MAC0350";

    println!("Temos {} alunos em {}", alunos, disciplina);
}
```

Usamos `let` para declarar uma variável em Rust. Depois inidicamos o nome, seguido de `:` e o tipo da variável. Então podemos atribuir um valor a ela com `=` e o valor propriamente dito.

Você só pode usar `let` dentro de funções. O tipo pode ser omitido na maioria esmagadora dos casos (viva a inferência de tipos).

`i32` é o equivalente a um `int` nas outras linguagens, enquanto `&str` é uma string um pouco diferente do que você está acostumado. Rust possui muitos tipos diferentes de strings que vamos focar depois, por hora apenas saiba que `"..."` é um `&str` e que isso é levemente diferente de uma `String` (use `String::from("alguma string")` para converter `&str` em `String`). Outros tipos que você pode querer usar são: `i64` (um long), `f32` (um float), `f64` (um double), `bool`, `char` 

> O nome da variável precisa ser `snake_case` (apenas letras minúsculas, dígitos e underlines) ascii começando com uma letra

```rust
fn main() {
    let alunos = 60;
    let disciplina = "MAC0350";

    println!("Temos {} alunos em {}", alunos, disciplina);
}
```

Não mencionei antes, mas podemos usar `{}` para fazer interpolação de strings em Rust dentro da macro `println!`.

Tenho que destacar 3 detalhes sobre variáveis em Rust.

### Redeclaráveis

```rust
fn main() {
    let idade = 18; // linha 3
    // ...
    let idade = "dezoito"; // linha n
}
```

O código acima é completamente válido, o compilador vai cuidar para que os usos de `idade` estejam corretos, entre a linha 3 e n `idade` é um inteiro, daí em diante uma string.

### Imutáveis

Por padrão você não pode mudar o valor de uma variável. Isso pode soar estranho, mas na maioria das vezes você não vai precisar fazer isso mesmo. Se precisar, declare a variável como `let mut`

```rust
fn main() {
    let mut idade = 18;

    idade++; // Incrementa a idade

    println!("A idade é {}", idade); // A idade é 19
}
```

### Obrigatoriamente válidas

Você não pode usar uma variável antes de definir um valor inicial para ela

```rust
fn main() {
    let idade: i32;
    let idade_maior = 20 + idade;
}
```

O compilador vai reprovar o seu programa. Isso é algo muito bom do Rust, programas inválidos sequer compilam.

## Condicionais

Vamos começar com um simples `if`

```rust
fn main() {
    let idade = 20;

    if idade >= 60 {
        println!("Idoso");
    } else if idade >= 18 {
        println!("Maior de idade");
    } else {
        println!("Menor de idade");
    }
}
```

A sintaxe é bastante simples: `if` seguido de uma expressão booleana e então o corpo que deve estar entre `{ }` obrigatoriamente. A parte legal é que o `if` do Rust é uma expressão.

```rust
fn main() {
    let nota: f64 = 6.5;
    let situacao = if nota >= 5.0 {
        "aprovado"
    } else if nota >= 3.0 {
        "de recuperação"
    } else {
        "reprovado"
    };

    println!("Você está {}", situacao);
}
```

Você tem que admitir que isso é muito legal. A última linha do corpo do `if` é usada como valor de retorno e não pode ter `;` no fim. Portanto, podemos fazer mais coisas dentro do corpo do `if`


```rust
fn main() {
    let nota: f64 = 6.5;
    let situacao = if nota >= 5.0 {
        println!("Deu bom");
        "aprovado"
    } else if nota >= 3.0 {
        println!("Foi quase");
        "de recuperação"
    } else {
        println!("Lascou");
        "reprovado"
    };

    println!("Você está {}", situacao);
}
```

Os operadores booleanos válidos são diversos: `>`, `<`, `>=`, `<=`, `==`, `!=`, `&&`, `||`, `!` e seguem a precedência usual. Você pode usar `( )` para agrupar expressões.

## Laços

Rust possui 3 tipos de laços:

### `loop`

Um simples loop infinito (nada de `while (true)` por aqui).

```rust
fn main() {
    loop {
        let dados = recebe_dados();
        
        // Faz algo

        if !sucesso {
            println!("Falha");
            break
        }
    }
}
```

Você pode usar `break` e `continue`. A parte legal é que sendo um loop infinito, a única forma de sair dele é com `break`, isso significa que...

```rust
fn main() {
    let falha = loop {
        //...
        if !sucesso {
            break dados
        }
    };

    println!("Falhou com {:?}", falha); // {:?} é uma formatação de debug
}
```

Sim, `loop` pode ser usado como expressão, diferentemente dos nossos dois laços seguintes:

### `while`

A sintaxe é quase identica ao `if` (exceto que não existe `else`) e funciona como um `while` qualquer

```rust
fn main() {
    let mut valor = 0;

    while valor < 10 {
        valor += 1;
    }

    println!("Final {}", valor);
}
```

### `for`

O `for` do Rust é bastante diferente do `for` do C, mas similar ao que outras linguagens oferencem.

```rust
fn main() {
    let mensagem = "Olá mundo";

    for c in mensagem.chars() {
        println!("Temos: {}", c);
    }
}
```

Usamos `for ... in`, aqui usamos `.chars()` para obter a lista de caracteres de uma string. Mas temos muitos iteradores disponíveis.

```rust
fn main() {
    let numeros = [10, 12, 18, 5, 27, 31, 4];
    let tamanho = numeros.len();

    // Vamos percorrer a lista de numeros 
    for c in numeros {
        println!("Temos: {}", c);
    }

    // Vamos percorrer números de 0 até 8 
    for c in 0..tamanho {
        println!("Temos: {}", numeros[c]);
    }

    // Vamos percorrer números de 1 até 9 
    for c in 1..=tamanho {
        println!("Temos: {}", numeros[c-1]);
    }
}
```

Note que usamos `[]` para criar um array de numeros e também para acessar um elemento desse array e `.len()` para obter o seu comprimento. 
Usamos os operadores `..` e `..=` para criar intervalos inteiros (fechado-aberto e fechado-fechado, respectivamente).

## Strings

Vamos tirar o elefante da sala: `&str` vs `String`

`String` é o que você está acostumado, uma "lista" de caracteres que você pode modificar a vontade. 
`&str` por outro lado é chamado de __string slice__, ela é um ponteiro para um pedaço de uma string.

No caso de você escrever `"alguma coisa"` no seu programa, a string `alguma coisa` vai ficar escrita no binário do seu programa, então você toma
apenas um ponteiro para ela.

Você também pode usar ela para conseguir pegar um pedaço de uma outra string sem ter que copiar os caracteres:

```rust
fn main() {
    let nome = String::from("Jorge Harrisonn");
    let primeiro_nome = &nome[0..5]; // "Jorge"
}
```

A diferença fundamental entre `String` e `&str` é que `String` é dona dos caracteres, enquanto `&str` apenas se refere aos caracteres possuídos por
outra string.

A seguir, vamos ver [casamento de padrões](./02-padroes.md), uma das features mais legais da linguagem