<img src="nix-snowflake-colours.svg" alt="Nix snowflake" width="100">

# Tech Talk - NixOS
by Vladimir Marko

## Intro

The name "Nix" is overloaded:

1. **Nix**, the **package manager**:
https://en.wikipedia.org/wiki/Nix_(package_manager)
> Nix package names are content-identifying since packages with the same name will have had the same inputs and the same build platform, and therefore the same build result.
- The "building" packages is a pure function.
  - Caching is trivial.
    - Binary cache available. 

2. **Nix**, the **language**:
https://nix.dev/manual/nix/2.28/language/
- pure (i.e., All values are immutable.)
- functional
- lazy
- dynamically typed

Used to define Nix packages (called "derivations").


3. **Nixpkgs**, the default **package repository**
- Git repo: https://github.com/NixOS/nixpkgs
- Search: https://search.nixos.org/packages

4. **NixOS**, the **OS** built on top of (1)

Why?

Video: NixOS Explained - Vimjoyer:
https://www.youtube.com/embed/9OMDnZWXjn4?end=229

## Demo - NixOS @ WSL

Get NixOS for WSL:
https://github.com/nix-community/NixOS-WSL

Documentations: https://nix-community.github.io/NixOS-WSL/

(Latest release:
https://github.com/nix-community/NixOS-WSL/releases/download/2505.7.0/nixos.wsl
)

### Installation
```ps
wsl -l

cd ~/Downloads

wsl --install --from-file nixos.wsl --name TestNixOSDistro
# `--name TestNixOSDistro`, as I already have a NixOS WSL disro
# wsl -d TestNixOSDistro # This is unnecessary, as a shell for the new distro starts automatically after installation.
```

### Try it out

A test:
```bash
cowsay "Moo!" # fails
nix-shell -p cowsay
cowsay "Moo!"
```

What's going on?
```bash
which cowsay
exit
which cowsay # fails
# <oldCowsayPath> "Moo!" # works
```

### NixOS Configuration

Video: NixOS Configuration - Vimjoyer:
https://www.youtube.com/watch?v=2eNJy9DSGNw

NixOS says:
> Please run `sudo nix-channel --update` and `sudo nixos-rebuild switch` now, to ensure you're running the latest NixOS and NixOS-WSL versions.

Instead, let's look at the NixOS configuration:
```bash
ls /etc/nixos
cat /etc/nixos/configuration.nix
```

#### Rebuild

Clone my custom private NixOS configuration into WSL distro from Windows: 
```ps
cd \\wsl$\TestNixOSDistro\home\nixos\
# https://github.com/VladimirMarko/NixOS-WSL-1-config (my private repo)
git clone git@github.com:VladimirMarko/NixOS-WSL-1-config.git
```

```bash
cd ~/NixOS-WSL-1-config

# If we don't want to be bothered by the OS rebuild process, we can use the following:
# Just build the new OS version (populates the cache)
# sudo nixos-rebuild dry-activate --flake .

# Build and then immediately switch to the new OS version
sudo nixos-rebuild switch --flake .
```

Now rebuilding without cache.
Let's watch a video in the meantime.

### Nix, the language

Video: Nix Language Explained - Vimjoyer:
https://www.youtube.com/watch?v=UgrwoAGSPOQ

Language reference:
https://nix.dev/manual/nix/2.28/language/index.html

Let's try out the language:
```bash
nix repl
```

```nix
[1 2 3]
[1 2 "three"]
[1 2 "three" ~/.]
```
> `./foo.png`
> A path relative to the file containing this Nix expression
```nix
{x = 1; y = 2;}
{some.nested.object = 42;}
:p {some.nested.object = 42;}
:p {some.nested.object = 42;}.some

let a = 42; in a

let a = 42; in a

:p let value = 42; in {key = value;}
:p let some.nested.value = 42; in {some.nested.key = some.nested.value;}

# Functions

x: x + 1
(x: x + 1) 1

## Destructuring

{someArg}: {key = someArg;}
({someArg}: {key = someArg;}) 42 # error
({someArg}: {key = someArg;}) {someArg = 42;}

:q
```

### Impurity without Nix flakes

```bash
cat /etc/nixos/configuration.nix`
```

We see the line:
```nix
<nixos-wsl>
```

What does that reference?
```bash
nix repl
```
```nix
<nixos-wsl>
:q
```
```bash
# ls <nixos-wsl>
# cat <nixos-wsl>/flake.nix
```
This is some nix code not managed in `/etc/nixos/`! We can't put that into a Git repo!

Evaluation via:
```bash
echo $NIX_PATH
# nixpkgs=/nix/var/nix/profiles/per-user/root/channels/nixos:nixos-config=/etc/nixos/configuration.nix:/nix/var/nix/profiles/per-user/root/channels
ls /nix/var/nix/profiles/per-user/root/channels
ls /nix/var/nix/profiles/per-user/root/channels/nixos-wsl
```

#### Solution: Nix flakes

All inputs are explicit.

`flake.lock` nails the exact versions of inputs down.

## Appendix

Further reading watching: https://www.youtube.com/@vimjoyer

NixOS options:
https://search.nixos.org/options

Home Manager options:
https://home-manager-options.extranix.com/

(Home Manager is for managing per-user dotfiles (i.e., `.*`).)
