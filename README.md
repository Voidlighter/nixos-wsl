# nixos-wsl-starter

This repository is intended to be a sane, batteries-included starter template
for running a LunarVim-powered NixOS development environment on WSL.

If you don't want to dig into NixOS too much right now, the only file you need
to concern yourself with is [home.nix](home.nix). This is where you can add and
remove binaries to your global `$PATH`.

Go to [https://search.nixos.org](https://search.nixos.org/packages) to find the
correct package names, though usually they will be what you expect them to be
in other package managers.

`unstable-packages` is for packages that you want to always keep at the latest
released versions, and `stable-packages` is for packages that you want to track
with the current release of NixOS (currently 23.11).

If you want to update the versions of the available `unstable-packages`, run
`nix flake update` to pull the latest version of the Nixpkgs repository and
then apply the changes.

Make sure to look at all the `FIXME` notices in the various files which are
intended to direct you to places where you may want to make configuration
tweaks.

If you found this starter template useful, please consider
[sponsoring](https://github.com/sponsors/LGUG2Z) and [subscribing to my YouTube
channel](https://www.youtube.com/channel/UCeai3-do-9O4MNy9_xjO6mg?sub_confirmation=1).

## What Is Included

This starter is a lightly-opinionated take on a productive terminal-driven
development environment based on my own preferences. However, it is trivial to
customize to your liking both by removing and adding tools that you prefer.

* The default editor is `lvim`
* `win32yank` is used to ensure perfect bi-directional copying and pasting to
  and from Windows GUI applications and LunarVim running in WSL
* The default shell is `zsh`
* Native `docker` (ie. Linux, not Windows) is enabled by default
* The prompt is [Starship](https://starship.rs/)
* [`fzf`](https://github.com/junegunn/fzf),
  [`lsd`](https://github.com/lsd-rs/lsd),
  [`zoxide`](https://github.com/ajeetdsouza/zoxide), and
  [`broot`](https://github.com/Canop/broot) are integrated into `zsh` by
  default
    * These can all be disabled easily by setting `enable = false` in
      [home.nix](home.nix), or just removing the lines all together
* [`direnv`](https://github.com/direnv/direnv) is integrated into `zsh` by
  default
* `git` config is generated in [home.nix](home.nix) with options provided to
  enable private HTTPS clones with secret tokens
* `zsh` config is generated in [home.nix](home.nix) and includes git aliases,
  useful WSL aliases, and
  [sensible`$WORDCHARS`](https://lgug2z.com/articles/sensible-wordchars-for-most-developers/)

## Quickstart

[![Watch the walkthrough video](https://img.youtube.com/vi/UmRXXYxq8k4/hqdefault.jpg)](https://www.youtube.com/watch?v=UmRXXYxq8k4)

* Get the [latest NixOS-WSL
  installer](https://github.com/nix-community/NixOS-WSL)
* Install it (tweak the command to your desired paths):
```powershell
wsl --import NixOS .\NixOS\ .\nixos-wsl.tar.gz --version 2

```

* Enter the distro:
```powershell
wsl -d NixOS
```

* Set up a channel:
```bash
sudo nix-channel --add https://nixos.org/channels/nixos-23.11 nixos
sudo nix-channel --update
```

* Get a copy of this repo (you'll probably want to fork it eventually):
```bash
nix-shell -p git neovim
git clone https://github.com/LGUG2Z/nixos-wsl-starter.git /tmp/configuration
cd /tmp/configuration
```

* Change the username to your desired username in `flake.nix` with `nvim` (or whichever editor you prefer)
* Apply the configuration
```bash
sudo nixos-rebuild switch --flake /tmp/configuration
```

* Restart and reconnect to the current WSL shell
```bash
wsl -t NixOS
wsl -d NixOS
```

* `cd ~` and then `pwd` should now show `/home/<YOUR_USERNAME>`
* Move the configuration to your new home directory 
```bash
mv /tmp/configuration ~/configuration
```

* Go through all the `FIXME:` notices in `~/configuration` and make changes
  wherever you want
* Apply the configuration
```bash
sudo nixos-rebuild switch --flake ~/configuration
```

Note: If developing in Rust, you'll still be managing your toolchains and components like `rust-analyzer` with `rustup`!

## Project Layout

In order to keep the template as approachable as possible for new NixOS users,
this project uses a flat layout without any nesting or modularization.

* `flake.nix` is where dependencies are specified
    * `nixpkgs` is the current release of NixOS
    * `nixpkgs-unstable` is the current trunk branch of NixOS (ie. all the
      latest packages)
    * `home-manager` is used to manage everything related to your home
      directory (dotfiles etc.)
    * `nur` is the community-maintained [Nix User
      Repositories](https://nur.nix-community.org/) for packages that may not
      be available in the NixOS repository
    * `nixos-wsl` exposes important WSL-specific configuration options
    * `nix-index-database` tells you how to install a package when you run a
      command which requires a binary not in the `$PATH`
* `wsl.nix` is where the VM is configured
    * The hostname is set here
    * The default shell is set here
    * User groups are set here
    * WSL configuration options are set here
    * NixOS options are set here
* `home.nix` is where packages, dotfiles, terminal tools, environment variables
  and aliases are configured





[This also helped.](https://github.com/nix-community/NixOS-WSL/issues/294)
> Got vscode wsl remoting with NixOS to work. This is using the nix-ld approach
> 
> ### Version Info
> ```
> Windows 11 22H2
> WSL version 1.2.5.0
> vscode 1.83.1 (user setup)
> ms-vscode-remote.remote-wsl-0.81.8
> ```
> 
> ### Setup Instructions
> * download latest release from https://github.com/nix-community/NixOS-WSL/releases
> * import into wsl using `wsl --import nixos . .\nixos-wsl.tar.gz --version 2`
> * update channel `sudo nix-channel --update`
> * update nixos configs `sudo nano /etc/nixos/configuration.nix`
> 
> ```nix
>   wsl.extraBin = with pkgs; [
>     { src = "${coreutils}/bin/uname"; }
>     { src = "${coreutils}/bin/dirname"; }
>     { src = "${coreutils}/bin/readlink"; }
>   ];
> 
>   programs.nix-ld.enable = true;
> 
>   nix.settings.experimental-features = [ "nix-command" "flakes" ];
> 
>   environment.systemPackages = [
>     pkgs.wget
>   ];
> ```
> 
> * rebuild `sudo nixos-rebuild switch`
> * shutodwn wsl from powershell `wsl --shutdown` and confirm shutdown `wsl --list --running`
> * from nixos shell `mkdir ~/.vscode-server` & `curl https://raw.githubusercontent.com/sonowz/vscode-remote-wsl-nixos/master/server-env-setup -o ~/.vscode-server/server-env-setup`
* or for vscodium run `mkdir ~/.vscodium-server` & `curl https://raw.githubusercontent.com/Voidlighter/vscodium-remote-wsl-nixos/master/server-env-setup -o ~/.vscodium-server/server-env-setup`
> * from vscode attempt to connect via wsl.   Find NodeExecServer log message with how its being invoked
> 
> ```
> [2023-11-01 03:41:35.475] NodeExecServer run: C:\Windows\System32\wsl.exe -d nixos sh -c '"$VSCODE_WSL_EXT_LOCATION/scripts/wslServer.sh" f1b07bd25dfad64b0167beb15359ae573aecd2cc stable code-server .vscode-server --host=127.0.0.1 --port=0 --connection-token=2218860889-3195581200-2104464773-1251764395 --use-host-proxy --without-browser-env-var --disable-websocket-compression --accept-server-license-terms --telemetry-level=all'
> ```
> 
> * take log message, modify slightly, and run
> 
> ```powershell
> # note the changes to `wsl.exe -d nixos -e sh -l -c` and expanding the `$VSCODE_WSL_EXT_LOCATION`
> C:\Windows\System32\wsl.exe -d nixos -e sh -l -c '"/mnt/c/Users/Acelinkio/.vscode/extensions/ms-vscode-remote.remote-wsl-0.81.8/scripts/wslServer.sh" f1b07bd25dfad64b0167beb15359ae573aecd2cc stable code-server .vscode-server --host=127.0.0.1 --port=0 --connection-token=3823671665-1023682691-1766917102-882463176 --use-host-proxy --without-browser-env-var --disable-websocket-compression --accept-server-license-terms --telemetry-level=all'
> ```
> 
> * vscode wsl remoting now works....
> 
> ### Thoughts
> Changing wslDaemon.js as mentioned https://nixos.wiki/wiki/Visual_Studio_Code#Remote_WSL did nothing. My guess is that that file is versioned outside of the wsl extension. No amount of changing versions or restarting vscode helped. The underlying wsl remoting commands being executed did not respect any modifications.
> 
> Really would like to see this user experience improved. Most of this appears to have been caused by the VSCode WSL extension changes. Closest issue I could find is [microsoft/vscode-remote-release#8305](https://github.com/microsoft/vscode-remote-release/issues/8305)
> 
> cc @Atry @sonowz

To finish getting VSCodium to work, I had to reference [this post](https://github.com/VSCodium/vscodium/issues/1265#issue-1378904326) and [this comment](https://github.com/VSCodium/vscodium/issues/1265#issuecomment-1310637145):
> After a while I managed to make the extension work with these changes:
> 
> 1.Download the .vsix from https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl and install manually from the extensions section
> 
> 2.In the file: C:\Users\%USERNAME%\.vscode-oss\extensions\ms-vscode-remote.remote-wsl-0.66.3\scripts\wslDownload.sh replace all "commit:$COMMIT" with "latest"
> 
> 3.Open the extension And wait the download of the .vscodium-server within WSL user profile
> 
> 4.In the file C:\Users\%USERNAME%\.vscode-oss\extensions\ms-vscode-remote.remote-wsl-0.66.3\scripts\wslServer.sh replace 'SERVER_APPNAME=$3' with 'SERVER_APPNAME="code-server"'
> 
> 5.In the file \\wsl$\Ubuntu\%USERNAME%\.vscodium-server\bin\%COMIT_ID%\product.json replace the value of the "commit": with the same value of "commit": found in your %VSCODIUM_INSTALLATION_DIRECTORY%\VSCodium\resources\app\product.json
> 
> 6.In the file \\wsl$\Ubuntu\%USERNAME%\.vscodium-server\bin\%COMIT_ID%\out\vs\server\node\server.main.js search "if(!ye){if(this._environmentService.isBuilt)return ie("Unauthorized client refused")" and change the first if condition from '!ye' to 'false'

> Just as an update, on step 6, search for "Unauthorized client refused". There should be 2 instances, apply the fix to the second search result. The variable name changes in different versions, apparently (for me it was `ve`).



