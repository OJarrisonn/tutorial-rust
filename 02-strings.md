# Strings em Rust

Rust possui muitos tipos diferentes de strings que podem confundir quem está começando. Aqui tentarei esclarecer cada um deles

## `&str`

Vamos começar pelo tipo chamado de __string slice__, ele é o tipo para os literais de string `"algum texto"`. Recebe esse nome por não representar uma string inteira, mas sim ser uma referência a um pedaço de uma string guardada em algum lugar. No caso, literais ficam guardados no binário do programa, então uma `&str` vai ser um ponteiro para a parte do programa que contém essa string. Vale ressaltar que `&` indica o fato de ser um tipo de string imutável

Normalmente esse tipo vai ser usado na entrada de funções caso só precise de ler o conteúdo de uma string. É bom saber que `&String` pode ser convertido em `&str` automaticamente.

## `String`

O tipo string usual que tem a posse do seu conteúdo e pode ser modificado de diversas formas. Em geral é esse tipo de string que se deseja usar para armazenar strings em estruturas de dados ou ao se criar novas strings concatenando ou modificando strings existentes.

Raramente modificações em strings são feitas __in place__, geralmente uma `String` nova é produzida.

## `PathBuf`

É muito similar a `String` mas é especializada em caminhos de arquivos, possui métodos utilitários como `.join`, `.parent`, `.exists` para lidar com caminhos.

## `&Path`

`Path` está para `PathBuf` como `&str` está para `String`. Isto é, é uma referência a um pedaço de um `PathBuf`

## `OsString`

Usado em implementações baixo nível por ser a representação apropriada de uma `String` para a plataforma onde o código é executado. Isso significa que `OsString` tem uma implementação diferente em sistemas UNIX e em sistemas Windows.

## `&OsStr`

É similar ao caso de `&Path` e `&str` mas para `OsString`

## `&[u8]`

Em alguns casos bem baixo nível (comunicação de rede) você pode querer lidar com a string como um array de bytes. Em geral todos tipos de string possuem um método `.as_bytes` que retorna o tipo `&[u8]` chamado de __byte slice__.

## `Vec<u8>`

Além do método `as_bytes`, é usual ter o método `.into_bytes` que vai tomar posse do valor da string e produzir um vetor de bytes

## `Chars<'_>`

Um tipo muito especial para quando se deseja iterar sobre os caracteres de uma string. Em geral strings em Rust usam UTF-8 o que significa que nem todo caractere ocupa apenas 1 byte. Por isso o método `.chars` produz esse iterador que vai lhe permitir percorrer os caracteres da string.

Note que `'_` é um inidicativo que a existência de um valor do tipo `Chars` está condicionada ao tempo de vida da string de onde ele está tirando esses caracteres. O que significa que ele trabalha, internamente, com referências ao conteúdo da string onde `.chars` foi chamado, sem criar um clone.