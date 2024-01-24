# Setup SSH

## Concepts

### OpenSSH Suite

Includes the following command-line utilities and daemons

- scp
- sftp
- ssh
- ssh-add (ssh-agent)
- ssh-keygen
- ssh-keyscan
- sshd

### SSH Agent

- A program that stores unencrypted SSH keys in memory.
- It is not persistent. Keys should be added on every boot.
- If the SSH key has a passphrase, it should be re-entered.

### OpenSSH Builds

The guide uses the [Brew version] of OpenSSH for FIDO support.

- https://developer.apple.com/forums/thread/698683
- https://github.com/apple-oss-distributions/OpenSSH/pull/1

[Brew version]: https://formulae.brew.sh/formula/openssh

|                   | Brew Version | macOS Bundle |
| ----------------- | ------------ | ------------ |
| Security Keys     | O            | X            |
| Passphrase Saving | X            | O            |

The passphrase cannot be saved, and should be manually entered.

## Client

### Installation

```bash
brew install openssh

ssh -V
# OpenSSH_9.3p1, OpenSSL 1.1.1t  7 Feb 2023

which ssh
# /opt/homebrew/bin/ssh

which ssh-agent
# /opt/homebrew/bin/ssh-agent
```

### Generate Key

```bash
# Paste the text below, substituting in your GitHub email address.
# https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
ssh-keygen -t ed25519-sk -C "your_email@example.com"

# Generating public/private ed25519-sk key pair.
# You may need to touch your authenticator to authorize key generation.

# Enter PIN for authenticator:
# You may need to touch your authenticator again to authorize key generation.
# Enter file in which to save the key
# Enter passphrase (empty for no passphrase):
# Enter same passphrase again:

# Your identification has been saved in /Users/?/.ssh/id_ed25519_sk
# Your public key has been saved in /Users/?/.ssh/id_ed25519_sk.pub
```

### Add to SSH Agent

This process can be automated [using Keychain].

[using Keychain]: #use-keychain

```bash
eval "$(ssh-agent -s)"
# > Agent pid 59566

ssh-add ~/.ssh/id_ed25519_sk # Should match the filename printed above.

# Enter passphrase for /Users/?/.ssh/id_ed25519_sk:
# Identity added: /Users/?/.ssh/id_ed25519_sk (?)

# The following error can occur if the SSH agent is not running.
# Could not add identity "/Users/?/.ssh/id_ed25519_sk": agent refused operation

ssh-add -l
# 256 SHA256:? ? (ED25519-SK) # Should match the entered type. (-t flag)
```

## Server

### Add Public Key

```bash
nano ~/.ssh/authorized_keys
```

> [!NOTE]
> This can be achieved in the client using `ssh-copy-id username@host`.

### Disable Password Auth

```bash
sudo nano /etc/ssh/sshd_config
```

```
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication no
```

## Access Server

```bash
ssh username@host # Touch the blinking YubiKey.

# When there are multiple authorized keys in the server,
# and if the first public key's YubiKey is not connected,
# the following error can be shown. It can be ignored.

# sign_and_send_pubkey: signing failed for ED25519-SK "?" from agent: agent refused operation
```

## Check Server Fingerprint

```bash
# On a local machine
ssh username@host
# The authenticity of host 'host (100.x.y.z)' can't be established.
# ED25519 key fingerprint is SHA256:?.
```

```bash
# On the server
ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key.pub
# 256 SHA256:? root@host (ED25519)
```

## Use Keychain

> Allows you to easily have one long running ssh-agent process per system, rather than the norm of one ssh-agent per login session.
>
> With [keychain], you only need to enter a passphrase once every time your local machine is rebooted.

[keychain]: https://www.funtoo.org/Funtoo:Keychain

```bash
brew install keychain
```

Add the following line to `~/.zprofile`. It adds these two keys.

- `id_ed25519_sk`
- `id_ed25519`

```
eval `keychain --eval --agents ssh id_ed25519_sk id_ed25519`
```

Restar the `zsh` shell and enter the passphrase(s).

```
 * keychain 2.8.5 ~ http://www.funtoo.org
 * Starting ssh-agent...
 * Adding  2 ssh key(s): /Users/?/.ssh/id_ed25519_sk /Users/?/.ssh/id_ed25519

Enter passphrase for /Users/?/.ssh/id_ed25519_sk:
Enter passphrase for /Users/?/.ssh/id_ed25519:

 * ssh-add: Identities added: /Users/?/.ssh/id_ed25519_sk /Users/?/.ssh/id_ed25519
```

After the initial entry, known SSH keys will be shown.

```
* keychain 2.8.5 ~ http://www.funtoo.org
 * Found existing ssh-agent: 33487
 * Known ssh key: /Users/?/.ssh/id_ed25519_sk
 * Known ssh key: /Users/?/.ssh/id_ed25519
```

If an error occurs, kill Keychain and open a new `zsh` shell.

```bash
keychain -k all

# * keychain 2.8.5 ~ http://www.funtoo.org
# * All ? ssh-agents stopped: 1942 3277
```
