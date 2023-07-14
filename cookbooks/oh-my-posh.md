# [Oh My Posh](https://ohmyposh.dev)

A prompt theme engine for any shell.

## Installation

- System-wide
    1. Create `/opt/bin` directory, if it does not exist.
    2. Download latest `oh-my-posh` binary from [GitHub](https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest) to `/opt/bin`.
    3. Set executable bit on `/opt/bin/oh-my-posh`.
    4. Append `/opt/bin` directory to `PATH`, if it does not contain it.
       ```shell
       sudo --shell <<'EOF'
       install --directory /opt/bin && \
       curl --location --output /opt/bin/oh-my-posh https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-amd64 && \
       chmod +x /opt/bin/oh-my-posh && \
       sed --expression '\;^PATH="\(.\+:\)\?/opt/bin\(:.\+\)\?"$;! s;^PATH="\(.\+\)"$;PATH="\1:/opt/bin";' --in-place /etc/environment
       EOF
       ```
- Current user
    1. Create `~/.local/bin` directory, if it does not exist.
    2. Download latest `oh-my-posh` binary from [GitHub](https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest) to `~/.local/bin`.
    3. Set executable bit on `~/.local/bin/oh-my-posh`.
       ```shell
       install --directory "$HOME"/.local/bin && \
       curl --location --output "$HOME"/.local/bin/oh-my-posh https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-amd64 && \
       chmod +x "$HOME"/.local/bin/oh-my-posh
       ```
## Upgrade

Requires `jq` package for determining latest version by parsing GitHub API output.

- System-wide  
  Upgrade `oh-my-posh` binary in `/opt/bin` directory, if it is older than latest.
  ```shell
  sudo --shell <<'EOF'
  [ "v$(/opt/bin/oh-my-posh version)" != "$(curl --header 'Accept: application/json' --silent https://api.github.com/repos/JanDeDobbeleer/oh-my-posh/releases/latest | jq --raw-output '.name')" ] && \
  curl --location --output /opt/bin/oh-my-posh https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-amd64 && \
  chmod +x /opt/bin/oh-my-posh && \
  /opt/bin/oh-my-posh version --verbose
  EOF
  ```
- Current user  
  Upgrade `oh-my-posh` binary in `~/.local/bin` directory, if it is older than latest.
  ```shell
  [ "v$("$HOME"/.local/bin/oh-my-posh version)" != "$(curl --header 'Accept: application/json' --silent https://api.github.com/repos/JanDeDobbeleer/oh-my-posh/releases/latest | jq --raw-output '.name')" ] && \
  curl --location --output "$HOME"/.local/bin/oh-my-posh https://github.com/JanDeDobbeleer/oh-my-posh/releases/latest/download/posh-linux-amd64 && \
  chmod +x "$HOME"/.local/bin/oh-my-posh && \
  "$HOME"/.local/bin/oh-my-posh version --verbose
  ```

## Configuration

Refer to the [docs](https://ohmyposh.dev/docs/installation/prompt) and append initialization snippet
to your script that runs on every interactive shell start (method varies by shell type).

For example, if you are using `bash` and want to use my theme (which relies heavily on
[Nerd Fonts](https://www.nerdfonts.com/)), you should append snippet to `~/.bashrc` like this:

```shell
tee --append "$HOME"/.bashrc > /dev/null <<'EOF'

# oh-my-posh
eval "$(oh-my-posh init bash --config https://raw.githubusercontent.com/g1bb0nz/g1bb0nz-etc-opt/main/g1bb0nz.omp.yaml)"
EOF
```

Now, sometimes you can reach your shell other than your favourite terminal emulator where the
necessary font is available and configured. In these situations, it may be wise to put a guard
before/around that line, so it initializes the prompt engine conditionally, and avoid a potentially
garbled prompt.  
In my case, I'm using Microsoft Terminal, which always defines the environment variable
`WT_SESSION`. In WSL sessions I make it available using `WSL_ENV`, in SSH sessions I send this
variable (and accept it server-side). Therefore on these systems I use it like this:

```shell
tee --append "$HOME"/.bashrc > /dev/null <<'EOF'

# oh-my-posh
[ -n "$WT_SESSION" ] && eval "$(oh-my-posh init bash --config https://raw.githubusercontent.com/g1bb0nz/g1bb0nz-etc-opt/main/g1bb0nz.omp.yaml)"
EOF
```
