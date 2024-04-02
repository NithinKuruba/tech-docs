# Signing Commits

## Installing GPG Tool Kit

- brew install gnupg

## Adding a Key

- Run `gpg --full-generate-key`

  - Select `RSA` for signing (Option 1 or 4)
  - Enter a passphrase and make sure you save this securely

- Run `gpg --list-secret-keys --keyid-format=long` to list the keys

- Copy the key id and run `gpg --armor --export <KEY_ID>` to print out the public key

- Go to your github `SSH and GPG` settings and add new GPG key and paste the public key

- Run `git config --global commit.gpgsign true` to enable signing

- Run `git config --global user.signingkey "<KEY_ID>"` to use the specific GPG key for signing commits

## Troubleshoot

- If you see the commits are being shown as `Unverified` then you may need to update your name and email information with Github CLI
- Run `git config --global user.name "xxxx"` to set name
- Run `git config --list` to show all the settings
- Run `gh auth login` to login to gh account
- Run `gh auth logout` to logout from gh account
