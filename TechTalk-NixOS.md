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



(
Video: NixOS Configuration - Vimjoyer:
https://www.youtube.com/watch?v=2eNJy9DSGNw
)

## Demo - NixOS @ WSL

Get NixOS for WSL:
https://github.com/nix-community/NixOS-WSL

Documentations: https://nix-community.github.io/NixOS-WSL/

(Latest release:
https://github.com/nix-community/NixOS-WSL/releases/download/2505.7.0/nixos.wsl
)

### Install
```ps
wsl -l

cd ~/Downloads`

wsl --install --from-file nixos.wsl --name TestNixOSDistro
# `--name TestNixOSDistro`, as I already have a NixOS WSL disro
# wsl -d TestNixOSDistro # The new disro opens automatically after installation.
```

A test:
```bash
cowsay "Moo!"
nix-shell -p cowsay
cowsay "Moo!"
```

What's going on?
```bash
which cowsay
exit
which cowsay
# <oldCowsayPath> "Moo!"
```

NixOS says:
> Please run `sudo nix-channel --update` and `sudo nixos-rebuild switch` now, to ensure you're running the latest NixOS and NixOS-WSL versions.

Let's look at the NixOS configuration:
```bash
ls /etc/nixos
cat /etc/nixos/configuration.nix
```

```ps
cd \\wsl$\TestNixOSDistro\home\nixos\
# https://github.com/VladimirMarko/NixOS-WSL-1-config
git clone git@github.com:VladimirMarko/NixOS-WSL-1-config.git
cd NixOS-WSL-1-config
```

```bash
cd ~/NixOS-WSL-1-config
ls
git status # fails
nix-shell -p git
git status

# Fix line endings
rm -rf *
git revert .
git status

# Just build the new OS version (populates the cache)
# nixos-rebuild dry-activate --flake .

# Build and switch to the new OS version
sudo nixos-rebuild switch --flake .
```

Now rebuilding without cache.
Let's watch a video in the meantime.

### Nix, the language

Video: Nix Language Explained - Vimjoyer:
https://www.youtube.com/watch?v=UgrwoAGSPOQ

Language reference:
https://nix.dev/manual/nix/2.28/language/index.html

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



cd ~/Downloads
wsl --install --from-file nixos.wsl --name TestNixOSDistro
# wsl -d TestNixOSDistro

cat /etc/nixos/configuration.nix

# nixos-rebuild switch
# nixos-rebuild switch --flake path/to/flake/directory#hostname

<nixos-wsl>
echo $NIX_PATH
nixpkgs=/nix/var/nix/profiles/per-user/root/channels/nixos:nixos-config=/etc/nixos/configuration.nix:/nix/var/nix/profiles/per-user/root/channels
ls /nix/var/nix/profiles/per-user/root/channels
ls /nix/var/nix/profiles/per-user/root/channels/nixos-wsl



\\wsl.localhost\TestNixOSDistro\home\nixos\



https://www.youtube.com/watch?v=2eNJy9DSGNw&t=11s


1
-----------
What is "Nix"
=============

Is:
- Nix package manager

- Nix language

- NixOS

Is not:
- "Unix-like" (e.g., Unix, Linux, macOS)
  - but: runs here

- "Nichts"
  - but: some stupid names (e.g., "nil" - A Nix language server)


2
------------
Nix, the package manager - why not just APT?
=================

Nix the package manager





2 Notes
----------
Advanced Package Tool (APT) - Debian


-------------
Nix, the language - why not just JSON?
==================
3


JS ecosystem - NPM - `package.json`










Further reading / watching:
- https://www.youtube.com/@vimjoyer




https://search.nixos.org/options
https://home-manager-options.extranix.com/

