# README - tmux-session
Manage your tmux sessions using fzf.

# Contents
- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

# Introduction
`tmux-session` is a script that allows you to manage your tmux sessions using
fzf. It allows you to: 
- create or attach to sessions using fzf
- create or attach to sessions using a keybinding
- run a hook after creating a session
This repository is heavily inspired on ThePrimeagen's [tmux-sessionizer](https://github.com/ThePrimeagen/.dotfiles/blob/master/bin/.local/scripts/tmux-sessionizer).

# Installation
Ensure that you have the following tools installed:
- [fzf](www.github.com/junegunn/fzf)

Next, run the following command to install `tmux-session` in the 
`/usr/local/bin` directory.
```bash
local tmp_dir=$(mktemp -d);
git clone https://github.com/BartSte/tmux-session.git $tmp_dir;
sudo $tmp_dir/install;
rm -rf $tmp_dir;
```
# Configuration
TODO

# Usage
For a given directory, the following will happen:
- a new tmux session is created with a name that corresponds to the top folder. 
- if the session already exists, the session is activated and no new one is created.
- if the directory does not exist the command is aborted. 
- if no directory is given, fzf is activated to select one. 

# Troubleshooting
If you encounter any issues, please report them on the issue tracker at:
[tmux-session issues](https://github.com/BartSte/tmux-session/issues)

## Contributing
Contributions are welcome! Please see [CONTRIBUTING](./CONTRIBUTING.md) for
more information.

# License
Distributed under the [MIT License](./LICENCE).
