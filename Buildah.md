# Buildah

## Installation

### WSL2

- Check Version of the OS
  ```
  cat /etc/os-release
  ```
- Update below commands if necessary
  ```
  sudo sh -c "echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/ /' > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list"
  wget -nv http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/Release.key -O Release.key
  sudo apt-key add - < Release.key
  sudo apt-get update -qq
  sudo apt-get -qq -y install buildah
  ```

## References

- https://github.com/containers/buildah/blob/main/install.md