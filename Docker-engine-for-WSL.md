# Distord - WSL Distribution Installer

## Installation - Ubuntu

- Download the latest [latest-distrod-wsl-launcher](https://github.com/nullpo-head/wsl-distrod/releases/latest/download/distrod_wsl_launcher-x86_64.zip) and start the launcher
- Select `Ubuntu` and `focal` in the available options and continue to install
- It asks for username and password as part of the installation process

## Docker Setup

- Update OS and Install latest libs

  ```bash
  sudo apt-get update

  sudo apt-get install \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
  ```
- Add GPG key
  ```bash
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
  ```
- Setup stable repo
  ```bash
  echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  ```
- Install docker engine
  ```bash
  sudo apt-get update

  sudo apt-get install docker-ce docker-ce-cli containerd.io
  ```
- To use docker as non-root user
  ```bash
  sudo usermod -aG docker $USER
  ```

## References
- https://github.com/nullpo-head/wsl-distrod
- https://lippertmarkus.com/2021/09/04/containers-without-docker-desktop/
- https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository