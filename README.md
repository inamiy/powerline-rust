# powerline-rust
[![Build Status](https://travis-ci.org/Xeoeen/powerline-rust.svg?branch=lib)](https://travis-ci.org/Xeoeen/powerline-rust)

powerline-rust is an altervative to [powerline-shell](https://github.com/b-ryan/powerline-shell). It's heavily inspired by it, but focuses on **minimalizing time of execution**.

Nobody wants to see latency between pressing enter in favourite shell and seeing prompt. This is main aim of this crate and that's why some features of other alternatives like dynamic segments choosing and theming via **commandline arguments** is **not possible here**.

Although, similar results **can be archived** by **customization**.

There is a demand to recompile every time while customizing, but you change your prompt only once upon a time. I think performance beneficence is worth it.

With default settings `powerline-rust` uses `libgit` for git prompt. Unfortunately results vary from system to system so if you want every last bit of a performance you can try disabling this feature and benchmarking.
## Advantages 
- blazing fast (less than 0.010s)
- only necessary dependencies
- runs git backend only when needed (huge time improvements in directories not in git tree)
- optional caching git results in memory or file

## Simple installation
```bash
git clone https://github.com/Xeoeen/powerline-rust
cd powerline-rust
cargo install --path .
```
You can also install one of examples for different look.
```bash
git clone https://github.com/Xeoeen/powerline-rust
cd powerline-rust
cargo install --path . --example minimalistic
```

## Setting up shell
#### Make sure you have executable in `$PATH`
### bash
```bash
function _update_ps1() {
    PS1="$(powerline $?)"
}

if [ "$TERM" != "linux" ]; then
    PROMPT_COMMAND="_update_ps1; $PROMPT_COMMAND"
fi
```
### zsh
You must also compile with `zsh-shell` feature.
```zsh
_update_ps1() {
    PS1="$(powerline $?)"
}
precmd_functions+=(_update_ps1)
```
### fish
You must also compile with `bare-shell` feature.
```bash
function fish_prompt
    powerline $status
end
```

## Custom shell prompt
Simply create new rust program that fulfils your requirements.
```rust
use powerline::{modules::*, theme::SimpleTheme};

fn main() {
	let mut prompt = powerline::Powerline::new();

	prompt.add_module(User::<SimpleTheme>::new());
	prompt.add_module(Host::<SimpleTheme>::new());
	prompt.add_module(Cwd::<SimpleTheme>::new(45, 4, false));
	prompt.add_module(Git::<SimpleTheme>::new());
	prompt.add_module(ReadOnly::<SimpleTheme>::new());
	prompt.add_module(Cmd::<SimpleTheme>::new());

	println!("{}", prompt);
}


```
## Tips and trics
### Strip executable
Remove unnecessary symbols from file to greatly reduce size of it.
Theoretically it can reduce time of execution.
```bash
cd ~/.cargo/bin/
strip powerline
```
### Use LTO and other

```toml
# Cargo.toml
[profile.release]
lto = true
panic = 'abort'
```

### Custom theme

```rust
use powerline::{modules::*, terminal::Color};

struct Theme;

impl CmdScheme for Theme {
	const CMD_FAILED_BG: Color = Color(161);
	const CMD_FAILED_FG: Color = Color(15);
	const CMD_PASSED_BG: Color = Color(236);
	const CMD_PASSED_FG: Color = Color(15);
}

fn main() {
	let mut prompt = powerline::Powerline::new();
	prompt.add_module(Cmd::<SimpleTheme>::new());

...
```



