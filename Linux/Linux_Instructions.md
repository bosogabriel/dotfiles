# Linux Dotfile
The purpose of this folder is to quickly set-up my working enviroment in any working linux distro (ideally). 
I'll start adding distros as I test them. 
## Distro
### Ubuntu
```bash
sudo apt-get update && sudo apt-get install -y \
  curl \
  git \
  gnupg \
  htop \
  jq \
  pass \
  pwgen \
  python3-pip \
  ripgrep \
  shellcheck \
  unzip \
  docker.io
#VSCode
sudo snap install --classic code
#Insomnia
sudo snap install insomnia
#Docker on start
sudo systemctl enable --now docker
```
## Documentation Links per tool
### Git
https://github.com/git-guides/install-git

### Docker
https://docs.docker.com/engine/install/

### Minikube
https://minikube.sigs.k8s.io/docs/start/

### VSCode
https://code.visualstudio.com/docs/setup/linux

### Insomnia
https://support.insomnia.rest/article/156-installation
