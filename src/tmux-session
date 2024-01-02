#!/usr/bin/bash

usage="Usage: tmux-session [options] [directory]

For a given directory, the following will happen:
- a new tmux session is created with a name that corresponds to the top folder. 
- if the session already exists, the session is activated and no new one is created.
- if the directory does not exist the command is aborted. 
- if no directory is given, fzf is activated to select one. 

If a file exists at ~/.config/tmux-session/dirs, where each line is a 
directory, fzf will search for projects in those locations. Alternatively,
the config file can be specified using the --config option.

If a command is specified using the --hook option, it will be run after the
session is created. The following variables are available:
    - \$name: the name of the session
    - \$directory: the directory of the session
    - \$config: path to the config file
    - \$hook: the hook command itsel
    - \$symlinks: symlinks follow option

options:
    -h --help       shows this help messages
    -c --config     a config file with directories to search for
       --hook       a command to run after the session is created.
    -d --debug      print debug messages
    -L              follow symlinks

positional:
    <directory>   a directory to create a tmux session for"

##############################################################################
# Prints debug messages to stdout
# Arguments:
#  $@: the message to print
##############################################################################
debug() {
    echo "$@" &>$output
}

##############################################################################
# Parses the command line arguments
# Arguments:
# $@: the command line arguments
# Sets the following variables:
# - $config: the config file to use
# - $hook: the hook command to run
# - $directory: the directory to create the session for
# - $output: the output file to use for debug messages
# - $name: the name of the session
# - $symlinks: whether to follow symlinks
##############################################################################
parse_args() {
    local options
    options=$(getopt -o hc:dL --long help,config:,hook:,debug -- "$@")
    eval set -- "$options"

    while [[ $# -gt 0 ]]; do
        key="$1"
        case $key in
            -h | --help)
                echo "$usage"
                exit 0
                ;;
            -c | --config)
                config="$2"
                shift
                shift
                ;;
            --hook)
                hook="$2"
                shift
                shift
                ;;
            -d | --debug)
                output=/dev/stdout
                shift
                ;;
            -L)
                symlinks="$key"
                shift
                ;;
            --)
                directory="$2"
                shift
                break
                ;;
            *)
                echo "Unknown option: $key"
                exit 1
                ;;
        esac
    done
    debug " The hook is $hook"
}

##############################################################################
# Uses fzf to select a directory
# Sets the following variables:
# - $directory: the directory to create the session for
##############################################################################
fzf_directory() {
    local dirs

    if [[ -f $config ]]; then
        dirs=$(cat $config | tr '\n' ' ')
        eval "find $symlinks $dirs -maxdepth 1 -type d 2> /dev/null| fzf"
    else
        find $symlinks $HOME -maxdepth 1 -type d | fzf
    fi
}

##############################################################################
# Gets the top folder of a directory
# Arguments:
# - $1: the directory to get the top folder for
# Returns:
# - the top folder of the directory
##############################################################################
get_top_folder() {
    basename "$directory" | tr . _
}

##############################################################################
# Checks if tmux is active
# Returns:
# - 0 if tmux is not active
# - 1 if tmux is active
##############################################################################
no_tmux() {
    [[ -z $TMUX ]]
}

##############################################################################
# Checks if a tmux session exists
# Arguments:
# - $1: the name of the session
# Returns:
# - 0 if the session exists
# - 1 if the session does not exist
##############################################################################
has_session() {
    tmux has-session -t $1 &>/dev/null
}

##############################################################################
# Creates a new tmux session
# Arguments:
# - $1: the name of the session
# - $2: the directory of the session
# - $3: additional arguments to pass to tmux
##############################################################################
new_session() {
    tmux new-session -s $1 -c $2 $3
    eval $hook
}

##############################################################################
# Main
##############################################################################
hook=""
config=~/.config/tmux-session/dirs
output=/dev/null
directory=""
symlinks=""
parse_args "$@"

[[ -z $directory ]] && directory=$(fzf_directory)
[[ -z $directory ]] && exit 0

if [[ ! -d $directory ]]; then
    message="Directory $directory not found."
    no_tmux && echo "$message" || tmux display-message -d 2000 "$message"
    exit 0
fi

name=$(get_top_folder $directory)

if no_tmux; then
    if has_session $name; then
        debug "Attach to tmux session $name"
        tmux attach-session -t $name
    else
        debug "Create new session $name"
        new_session $name $directory
    fi
else
    if ! has_session $name; then
        debug "Tmux active. Create new session $name"
        new_session $name $directory -d
    fi
    debug "Switch to tmux session $name"
    tmux switch-client -t $name
fi
