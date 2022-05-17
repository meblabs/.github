# MEBlabs Guidelines

## SSH

Use git via SSH -> [Doc](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

## Signing commits

#### Requirements

- gnupg

Mac: 
```sh
brew install gnupg
```

#### Configuration

Creation: 

Make sure the email of the key is the same on GitHub and the same in git config.
```sh
gpg --gen-key
```

List:
```sh
gpg --list-keys
```

Distribution:
```sh
gpg --keyserver keyserver.ubuntu.com --send-keys <YOUR_KEY_ID>
```

Retrive: (it can take minutes from distrubution)
```sh
gpg --keyserver keyserver.ubuntu.com --recv-keys <YOUR_KEY_ID>
```

Once you have a private key to sign with, you can configure Git [[Doc](https://docs.github.com/en/authentication/managing-commit-signature-verification/telling-git-about-your-signing-key)]
```sh
git config --global user.signingkey <YOUR_KEY_ID>
git config --global commit.gpgsign true
```

If you aren't using the GPG suite, run the following command in the zsh shell to add the GPG key to your .zshrc file, if it exists, or your .zprofile file:

```sh
if [ -r ~/.zshrc ]; then echo 'export GPG_TTY=$(tty)' >> ~/.zshrc; \
else echo 'export GPG_TTY=$(tty)' >> ~/.zprofile; fi
```
Alternatively, if you use the bash shell, run this command:
```sh
if [ -r ~/.bash_profile ]; then echo 'export GPG_TTY=$(tty)' >> ~/.bash_profile; \
else echo 'export GPG_TTY=$(tty)' >> ~/.profile; fi
```

Optionally, to prompt you to enter a PIN or passphrase when required

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


## Rebase Pull

Standard pull method

```sh
git config --global pull.rebase true
```

I want to record what actually happened: then `merge` 
- History is preserved
- Messy commits are there

I want to tell the story of how your project was made: then `rebase`
- History is modified 
- Commits are cleaner

If the code of the repository is shared (ex: fork), rebase is not a good choice.

## Commits
To create consistent and convenvtional commits we use the [commitizen cli](https://github.com/commitizen/cz-cli).

#### Install
```sh
npm install -g commitizen
```

#### Make the repo commitizen friendly
```sh
commitizen init cz-conventional-changelog --save-dev --save-exact
```

#### To create a new commit
Use the command `cz` and follow the instructions

#### Result
This way all the commits will be generated with the same conventional structure which can be read from actions

## Versioning
To automatically keep track of the version of the code we use the [GitHub Tag](https://github.com/marketplace/actions/github-tag) action.
It tags only the *master* branch and creates a release by default, based on the commit generated by commitizen.

To create a tag and a version for each push on the *release* branch paste the following code in the .github/workflows/release.yml file (create the folders in case they do not exist)

```yml
name: Releaser

on:
  push:
    branches:
      - release
  pull_request:
    branches:
      - release

jobs:
  tag:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Bump version and push tag
        id: tag_version
        uses: mathieudutour/github-tag-action@v6.0
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          release_branches: release
      - name: Create a GitHub release
        uses: ncipollo/release-action@v1
        with:
          tag: ${{ steps.tag_version.outputs.new_tag }}
          name: Release ${{ steps.tag_version.outputs.new_tag }}
          body: ${{ steps.tag_version.outputs.changelog }}
```
