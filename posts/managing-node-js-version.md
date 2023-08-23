# Managing Multiple Node.js Versions

The [pnpm package manager] also doubles as a Node.js version manager with the `pnpm env` CLI commands.

[pnpm package manager]: https://pnpm.io/cli/env

```bash
% pnpm env use -g lts
# Fetching Node.js 18.17.1 ...
# Node.js 18.17.1 is activated

% pnpm env list
# 18.17.1

% node -v
# v18.17.1

% npm -v
# 9.6.7
```

## Installation

Note that it should be installed using the standalone script. [Homebrew installation] does not support it.

[Homebrew installation]: https://formulae.brew.sh/formula/pnpm

> If you want to manage Node.js with pnpm, you need to remove any Node.js that was installed by other tools, then install pnpm using one of the standalone scripts

If [nvm] is installed, it and its shell configurations should be manually uninstalled or removed beforehand.

[nvm]: https://github.com/nvm-sh/nvm#readme

```bash
$ rm -rf "$NVM_DIR"
```

## Global Packages

With nvm and its npm, global packages were installed per Node.js installation. [Migration] is required.

[Migration]: https://github.com/nvm-sh/nvm#migrating-global-packages-while-installing

```bash
% nvm install node --reinstall-packages-from=node
```

This is not the case with pnpm. This alone was a killer feature.

```bash
% pnpm add -g pm2
# Already up to date
# Progress: resolved 156, reused 155, downloaded 0, added 0, done
# Done in 5.8s

% pm2 -v
# 5.3.0
```
