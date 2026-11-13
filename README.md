# Monkey

A tree-walking interpreter for the Monkey programming language, written in Go with no dependencies. I built it while working through Thorsten Ball's [*Writing an Interpreter in Go*](https://interpreterbook.com/), and it covers the full pipeline from source text to evaluated result.

```
$ go run .
Hello you! This is the Monkey programming language!
>> let add = fn(a, b) { a + b };
>> add(2, 3);
5
>> let m = {"name": "monkey"};
>> m["name"];
monkey
>> len([1, 2, 3]);
3
```

## The language

- Integers, booleans, strings, arrays, and hash maps
- `let` bindings, `if` / `else`, and `return`
- First-class functions and **closures**:

  ```js
  let newAdder = fn(x) { fn(y) { x + y } };
  let addTwo = newAdder(2);
  addTwo(3); // 5
  ```

- Built-in functions: `len`, `first`, `last`, `rest`, `push`, `puts`

## How it works

```
source ──► lexer ──► tokens ──► parser ──► AST ──► evaluator ──► object
                                  (Pratt)          (tree-walking, with environments)
```

| Package | Role |
|---|---|
| `token/` | Token types and keyword lookup |
| `lexer/` | Turns source text into tokens |
| `parser/` | **Pratt parser** (top-down operator precedence) that builds the AST, with optional tracing to debug precedence |
| `ast/` | AST node types |
| `evaluator/` | Recursively evaluates AST nodes, plus the built-in functions |
| `object/` | The runtime object system, and `Environment` for scopes and closures |
| `repl/` | Read-eval-print loop |

Interesting parts:

- **Pratt parsing.** Each token type registers prefix and infix parse functions with a precedence. That handles operator precedence and associativity without a grammar generator.
- **Closures.** A function object captures the environment it was defined in. Calling it creates an *enclosed* environment that extends the captured one.
- **Error values.** Runtime errors are first-class objects that short-circuit evaluation, instead of Go panics.

## Run it

Requires Go 1.19+.

```bash
git clone https://github.com/learnercys/monkey && cd monkey
go run .        # start the REPL
go test ./...   # lexer, parser, AST, object, and evaluator tests
```

## What's next

- [ ] Compile to bytecode and run on a stack-based VM (the follow-up book, *Writing a Compiler in Go*)
- [ ] Macros
