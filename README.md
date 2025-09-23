# Dotfiles

Personal dotfiles managed with GNU Stow.

## Installation

1. Clone this repository:

   ```bash
   git clone <repository-url> ~/.dotfiles
   cd ~/.dotfiles
   ```

2. Install GNU Stow:

   ```bash
   # macOS
   brew install stow

   # Ubuntu/Debian
   sudo apt install stow

   # Arch Linux
   sudo pacman -S stow
   ```

## Usage

### Install dotfiles

```bash
# Install all dotfiles
stow .

# Install specific package
stow git
stow vim
```

### Remove dotfiles

```bash
# Remove all dotfiles
stow -D .

# Remove specific package
stow -D git
```

### Update dotfiles

```bash
# Pull latest changes
git pull

# Restow packages
stow -R .
```

## Directory Structure

Each subdirectory represents a package that will be stowed to `$HOME`:

```
dotfiles/
├── git/          # .gitconfig, .gitignore_global
├── vim/          # .vimrc, .vim/
├── zsh/          # .zshrc, .zprofile
└── ...
```

## Adding New Dotfiles

1. Create a new directory for your package
2. Add your dotfiles (without the leading dot)
3. Run `stow <package-name>`

Example:

```bash
mkdir tmux
# Add tmux.conf to tmux/ directory
stow tmux
```
