# MEBlabs Guidelines

## SSH

Use git via SSH -> [Doc](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

## Signed commits

#### Requirements

- gnupg

Mac:

```sh
brew install gnupg
```

#### Configuration

Creation:

Make sure the email associated with the key is the same on GitHub and in your git config.

```sh
gpg --gen-key
```

List:

```sh
gpg --list-secret-keys --keyid-format=long
#[keyboxd]
#---------
#sec   ed25519/171280C1A910RADA 2024-09-16 [SC] [expires: 2027-09-16]
#      66ADE85TH0D58A9894C51F947171280C1A910TOFU
#uid                 [ultimate] user <user email>
#ssb   cv25869/32A287714RT8JK3F0 2024-09-16 [E] [expires: 2027-09-16]

#THE KEY_ID is 171280C1A910RADA
```

Distribution:

```sh
gpg --keyserver keyserver.ubuntu.com --send-keys <YOUR_KEY_ID>
```

Retrive: (it can take minutes from distribution)

```sh
gpg --keyserver keyserver.ubuntu.com --recv-keys <YOUR_KEY_ID>
```

Once you have a signing key, you can configure Git [[Doc](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)]

```sh
git config --global user.signingkey <YOUR_KEY_ID>
git config --global commit.gpgsign true
```

If you're not using the GPG suite, run the following command in your zsh shell to add the GPG key to your .zshrc file (if it exists), or to your .zprofile file:

```sh
if [ -r ~/.zshrc ]; then
    echo -e '\nexport GPG_TTY=$(tty)' >> ~/.zshrc;
else
    echo -e '\nexport GPG_TTY=$(tty)' >> ~/.zprofile;
fi
```

Alternatively, for bash users, run this command:

```sh
if [ -r ~/.bash_profile ]; then
    echo -e '\nexport GPG_TTY=$(tty)' >> ~/.bash_profile;
else
    echo -e '\nexport GPG_TTY=$(tty)' >> ~/.profile;
fi
```

Optionally, to prompt you to enter a PIN or passphrase when required:

```sh
brew install pinentry-mac
echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf
killall gpg-agent
```

#### GitHub

Add the gpg public key to GitHub account [[Doc](https://docs.github.com/en/enterprise-server@3.1/authentication/managing-commit-signature-verification/checking-for-existing-gpg-keys)]

```sh
gpg --armor --export <YOUR_KEY_ID>
```

#### VScode

```json
"git.enableCommitSigning": true
```

#### Import to another computer

Export private key

```sh
gpg --export-secret-keys <YOUR_KEY_ID> > private.key
```

Import from another computer

```sh
gpg --import private.key
```

## Git Username

Set your username on gitconfig, it will be required for AWS development resources.

```sh
git config --global user.name "nickname"
```

## Rebase Pull

Standard pull method

```sh
git config --global pull.rebase true
```

Choose `merge` when you want to record exactly what happened:

- Preserves full history
- Includes all messy commits

Choose `rebase` when you want to tell a cleaner story of how your project was built

- Modifies history
- Results in cleaner commits

Note: If the repository code is shared (e.g., in a fork), rebase is generally not recommended.

## Commits

To create consistent and conventional commits we follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

## Optional: Zsh + Powerlevel10k (MacOS)

For an enhanced user experience, install the zsh shell along with the oh-my-zsh framework, and apply the powerlevel10k custom theme.

Zsh:

```sh
brew install zsh
```

[oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh#uninstalling-oh-my-zsh):

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

[powerlevel10k](https://github.com/romkatv/powerlevel10k):

```sh
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc`.
