# Technical Documentation

## Setup ASDF

1. Install `asdf` according to the `asdf` installation guide.
   - https://asdf-vm.com/#/core-manage-asdf?id=install

1. Install `asdf` packages defined in `.tool-versions`.
   ```sh
      cat .tool-versions | cut -f 1 -d ' ' | xargs -n 1 asdf plugin-add || true
      asdf plugin-update --all
      asdf install
      asdf reshim
   ```
