install homebrew
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

update package information
`brew update`

install cask(if necessary)
`brew install caskroom/cask/brew-cask`
`export HOMEBREW_CASK_OPTS="--appdir=/Applications"`

install packages
`brew cask install virtualbox minikube kubernetes-cli docker`

enable kubernetes in docker desktop
click Docker Desktop > Perferences > Kubernetes > Enable Kubernetes
click Docker Desktop > Perferences > Kubernetes > Show system containers(advanced)

check kubernetes context
`kubectl config get-contexts`
```
CURRENT   NAME                 CLUSTER                      AUTHINFO             NAMESPACE
*         docker-for-desktop   docker-for-desktop-cluster   docker-for-desktop
```
