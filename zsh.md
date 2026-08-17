### ZSH Install
* One Command to Install All
```sh
  sudo apt update && sudo apt install -y zsh git curl && sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && [ -f ~/.bash_aliases ] && cp ~/.bash_aliases ~/.zsh_aliases; git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting && sed -i 's/plugins=(git)/plugins=(git zsh-autosuggestions zsh-syntax-highlighting)/' ~/.zshrc && echo -e "\nif [ -f ~/.zsh_aliases ]; then\n    source ~/.zsh_aliases\nfi" >> ~/.zshrc && chsh -s $(which zsh) && echo "Setup completed! Please log out and log back in."
```

* Step by Step Commands
```sh
sudo apt install zsh

sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

cp .bash_aliases .zsh_aliases


git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting


vim .zshrc

plugins=(git zsh-autosuggestions zsh-syntax-highlighting)

if [ -f ~/.zsh_aliases ]; then
    source ~/.zsh_aliases
fi


source .zshrc


echo $SHELL
which zsh
chsh -s $(which zsh)

logout

```
