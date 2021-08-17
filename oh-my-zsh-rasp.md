# Install Oh My Zsh

https://ohmyz.sh/


## Steps
```bash

# 1
sudo apt install zsh

# 2 - Change default bash
chsh -s /bin/zsh 

# 3
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 4 - Change Theme
cd
nano .zshrc 

#change the lines
ZSH_THEME="gentoo"

plugins=(
  git
  zsh-autosuggestions
)


# 5 - Install auto suggestion
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions


```