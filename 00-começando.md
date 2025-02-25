# Começando com Rust

Para dar seus primeiros passos com Rust, você precisa instalar o compilador. Para isso vamos usar o
[`rustup`](https://rustup.rs/), que nos ajuda a gerenciar ferramentas de desenvolvimento Rust.

Depois de instalar o `rustup`, vamos instalar toda _toolchain_ do Rust. Para isso, execute o seguinte comando:

```bash
rustup default stable
```

Você provavelmente vai querer reiniciar o seu terminal depois disso.

Vamos agora criar um novo projeto Rust. Abra o diretório onde quiser guardar o projeto e execute:

```bash
cargo new meu_projeto
```

Isso vai criar uma nova pasta, chamada `meu_projeto` com um respositório Git e arquivos iniciais. Abra essa pasta no seu editor favorito. A propósito, instale a extensão `rust analyzer` no seu editor, ela vai te ajudar muito.

Você deve ter uma estrutura de arquivos com pelo menos:

```
Cargo.toml
src/
  main.rs
```

- `Cargo.toml`: arquivo que define dependências e configurações do projeto.
- `src/main.rs`: ponto de entrada do código

O seu `main.rs` deve ter o seguinte conteúdo:

```rust
fn main() {
    println!("Hello, world!");
}
```

Vamos executar o nosso código. Abra um terminal na pasta do projeto e execute:

```bash
cargo run
```

> Caso use o VS Code. Logo acima da `fn main` deve haver um botão `run` para executar o código, se tiver instalado a extensão recomendada.

Ao executar, você deve ver `Hello, World!` escrito no seu terminal. Parabéns! Você acabou de executar seu primeiro código Rust.

A seguir, [sintaxe básica](./01-sintaxe.md)
