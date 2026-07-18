#!/usr/bin/env bash
#
# ~/.bashrc — Arch Linux edition, annotated
#
# This file is read by every NEW interactive, non-login bash shell
# (e.g. every terminal tab/window you open in Ghostty). Login shells
# read ~/.bash_profile first, which on most Arch setups just sources
# this file too — see the bottom of this file for a snippet to add
# there if you don't already have it wired up.

# -----------------------------------------------------------------
# 0. Bail out early if not running interactively
# -----------------------------------------------------------------
# Non-interactive shells (e.g. scripts run via `bash script.sh`, or
# scp/rsync invoking bash on the remote end) don't need prompts,
# aliases, colors, etc. `$-` contains the current shell's option
# flags; an interactive shell always has "i" in there.
case $- in
    *i*) ;;      # interactive — keep going
      *) return ;; # non-interactive — stop sourcing the rest of this file
esac


# ===================================================================
# 1. HISTORY
# ===================================================================

# Don't put duplicate lines or lines starting with a space in history.
# ignoreboth = ignorespace + ignoredups
HISTCONTROL=ignoreboth

# Number of commands to keep in memory for the current session.
HISTSIZE=10000

# Number of lines to keep in the ~/.bash_history file on disk.
HISTFILESIZE=20000

# Append to history file instead of overwriting it on shell exit.
# Without this, the LAST shell to close wins and earlier sessions'
# history gets clobbered.
shopt -s histappend

# Save multi-line commands as one history entry instead of one
# entry per line — makes for loops etc. easier to recall/edit.
shopt -s cmdhist

# Write history to disk after every command, not just on shell exit.
# This means multiple open terminals share history in near-real time.
PROMPT_COMMAND="history -a; history -c; history -r; ${PROMPT_COMMAND}"

# Ignore common no-op commands from being recorded.
HISTIGNORE="ls:cd:cd -:pwd:exit:clear:history"

# Add a timestamp to each history entry (visible with `history` and
# in ~/.bash_history). Format: 2026-07-18 14:03:11
HISTTIMEFORMAT="%F %T  "


# ===================================================================
# 2. SHELL BEHAVIOR / GLOBBING
# ===================================================================

# Fix window size after every command if the terminal was resized —
# prevents wrapping glitches in the prompt/output.
shopt -s checkwinsize

# Allow ** to match files/dirs recursively in pathname expansion.
# e.g. `ls **/*.log` finds .log files in all subdirectories.
shopt -s globstar

# Case-insensitive globbing (e.g. `ls *.JPG` also matches file.jpg).
shopt -s nocaseglob

# If a glob pattern matches nothing, don't pass the literal pattern
# through to the command — this avoids surprises like `rm *.bak`
# deleting a file literally named "*.bak" when no .bak files exist.
shopt -s nullglob

# Autocorrect minor typos in directory names when using `cd`.
# e.g. `cd /hom/user` -> corrects to `cd /home/user`
shopt -s cdspell

# Let you `cd` into a directory just by typing its name (no `cd` needed)
# when it's not a recognized command. Comment out if you find it
# too "magic".
shopt -s autocd

# Extended globbing: enables patterns like !(pattern), @(a|b), etc.
shopt -s extglob


# ===================================================================
# 3. COMPLETION
# ===================================================================

# Arch ships bash-completion in the `bash-completion` package.
# This enables tab-completion for pacman, git, systemctl, ssh hosts,
# and hundreds of other tools' subcommands/flags.
if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
fi

# Make completion case-insensitive.
bind "set completion-ignore-case on" 2>/dev/null

# Show all matches immediately on ambiguous completion instead of
# requiring a second Tab press.
bind "set show-all-if-ambiguous on" 2>/dev/null


# ===================================================================
# 4. ENVIRONMENT / EXPORTS
# ===================================================================

# Default editor for git commit messages, crontab -e, etc.
export EDITOR="nvim"       # change to vim / nano / code -w / whatever you use
export VISUAL="$EDITOR"

# Default pager, with some sane less flags:
#  -R  interpret color escape codes correctly (needed for `git log`, etc.)
#  -F  auto-exit if content fits on one screen
#  -X  don't clear the screen on exit
export PAGER="less"
export LESS="-R -F -X"

# Make `less` (and man pages via less) show color for man pages.
export MANPAGER="less -R --use-color -Dd+r -Du+b"

# Colored `ls` needs this on Arch (uses GNU coreutils dircolors).
if [ -x /usr/bin/dircolors ]; then
    eval "$(dircolors -b)"
fi

# Add ~/.local/bin and ~/bin to PATH if they exist and aren't already
# in it — common location for user-installed scripts/binaries.
for dir in "$HOME/.local/bin" "$HOME/bin"; do
    if [ -d "$dir" ] && [[ ":$PATH:" != *":$dir:"* ]]; then
        PATH="$dir:$PATH"
    fi
done
export PATH


# ===================================================================
# 5. PROMPT (classic user@host:dir$, colorized, with git branch)
# ===================================================================

# Colors are defined using ANSI escape codes. \[ \] tell bash these
# sequences take up zero visible width, so line-wrapping/cursor math
# doesn't get confused.
_C_RESET='\[\033[00m\]'
_C_USER='\[\033[01;34m\]'   # bold blue   — username (\u)
_C_AT='\[\033[01;36m\]'     # bold cyan   — the literal "@" separator
_C_HOST='\[\033[01;32m\]'   # bold green  — hostname (\h), normal user
_C_HOST_ROOT='\[\033[01;31m\]'  # bold red — hostname, when running as root/sudo
_C_DIR='\[\033[01;34m\]'    # bold blue   — current directory
_C_GIT='\[\033[00;33m\]'    # yellow      — git branch info

# Prints the current git branch (or short SHA if detached), plus a
# "*" if the working tree is dirty. Silent no-op outside a repo.
__git_branch_ps1() {
    local branch
    branch=$(git symbolic-ref --short HEAD 2>/dev/null) || \
        branch=$(git rev-parse --short HEAD 2>/dev/null) || return
    local dirty=""
    if ! git diff --quiet --ignore-submodules HEAD -- 2>/dev/null; then
        dirty="*"
    fi
    printf ' (%s%s)' "$branch" "$dirty"
}

# Decides whether the hostname should show red (root) or green (normal).
# Covers two cases:
#   1. You ARE root (logged in as root, or via `sudo -i` / `su -`)
#   2. The command you're about to run is prefixed with `sudo`
#      (checked via DEBUG trap below, since PS1 itself can't see
#      "the next command" ahead of time)
__ROOT_CMD_ACTIVE=0

__check_root_cmd() {
    # BASH_COMMAND is the command about to execute. Flag it if it
    # starts with sudo/doas (with or without leading whitespace).
    case "$BASH_COMMAND" in
        sudo\ *|doas\ *|sudo|doas) __ROOT_CMD_ACTIVE=1 ;;
        *) __ROOT_CMD_ACTIVE=0 ;;
    esac
}
trap '__check_root_cmd' DEBUG

__build_prompt() {
    local host_color="$_C_HOST"
    if [ "$EUID" -eq 0 ] || [ "$__ROOT_CMD_ACTIVE" -eq 1 ]; then
        host_color="$_C_HOST_ROOT"
    fi

    PS1="${_C_USER}\u${_C_RESET}${_C_AT}@${_C_RESET}${host_color}\h${_C_RESET}:${_C_DIR}\w${_C_RESET}${_C_GIT}\$(__git_branch_ps1)${_C_RESET}\$ "
}
PROMPT_COMMAND="__build_prompt; ${PROMPT_COMMAND}"

# Keep terminal title in sync with current directory (Ghostty, like
# most terminals, reads the OSC 0/2 escape sequence for this).
case "$TERM" in
    xterm*|rxvt*|ghostty*)
        PROMPT_COMMAND="${PROMPT_COMMAND} echo -ne \"\033]0;\${USER}@\${HOSTNAME}: \${PWD/#\$HOME/~}\007\";"
        ;;
esac


# ===================================================================
# 6. ALIASES — general
# ===================================================================

alias ls='ls --color=auto --group-directories-first'
alias ll='ls -lah --color=auto --group-directories-first'
alias la='ls -A --color=auto'
alias l='ls -CF --color=auto'
alias grep='grep --color=auto'
alias diff='diff --color=auto'
alias ip='ip -color=auto'

alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias -- -='cd -'   # `-` returns to previous directory

alias mkdir='mkdir -pv'   # always create parent dirs, verbosely
alias df='df -h'          # human-readable sizes
alias du='du -h'
alias free='free -h'

alias grep='grep --color=auto -n'  # -n = show line numbers by default

# Safety nets: confirm before overwrite/delete on interactive shells.
# (Scripts calling rm/cp/mv directly are unaffected since aliases
# only apply in interactive shells.)
alias rm='rm -Iv'    # -I only prompts once for 3+ files/recursive, not every single file
alias cp='cp -iv'
alias mv='mv -iv'


# ===================================================================
# 7. ALIASES — Arch Linux package management
# ===================================================================

# pacman shortcuts
alias pacs='sudo pacman -S'              # install
alias pacr='sudo pacman -Rns'            # remove package + unneeded deps + configs
alias pacu='sudo pacman -Syu'            # full system update
alias pacq='pacman -Qi'                  # query installed package info
alias pacss='pacman -Ss'                 # search repos
alias pacown='pacman -Qo'                # which package owns this file?
alias pacls='pacman -Qe'                 # list explicitly installed packages
alias pacclean='sudo pacman -Sc'         # clean package cache
alias pacorphan='sudo pacman -Rns $(pacman -Qtdq)'  # remove orphaned deps

# AUR helper — only aliased if yay is actually installed.
if command -v yay >/dev/null 2>&1; then
    alias yays='yay -S'
    alias yayu='yay -Syu'
    alias yayss='yay -Ss'
fi

# systemd shortcuts
alias sc='sudo systemctl'
alias scs='systemctl status'
alias jctl='journalctl -xe'
alias jctlf='journalctl -f'   # follow live logs


# ===================================================================
# 8. ALIASES — git shortcuts
# ===================================================================

alias g='git'
alias gs='git status'
alias ga='git add'
alias gc='git commit -v'
alias gp='git push'
alias gl='git log --oneline --graph --decorate -20'
alias gd='git diff'
alias gco='git checkout'
alias gb='git branch'


# ===================================================================
# 9. MISC QUALITY-OF-LIFE
# ===================================================================

# Make `less` play nicer with colored output from things like
# `man`, `git diff`, etc. (also see MANPAGER above).
export LESSHISTFILE=-   # don't keep a ~/.lesshst file around

# Quick extract function for common archive formats.
extract() {
    if [ -z "$1" ] || [ ! -f "$1" ]; then
        echo "Usage: extract <archive-file>"
        return 1
    fi
    case "$1" in
        *.tar.bz2)   tar xjf "$1"   ;;
        *.tar.gz)    tar xzf "$1"   ;;
        *.tar.xz)    tar xJf "$1"   ;;
        *.bz2)       bunzip2 "$1"   ;;
        *.rar)       unrar x "$1"   ;;
        *.gz)        gunzip "$1"    ;;
        *.tar)       tar xf "$1"    ;;
        *.tbz2)      tar xjf "$1"   ;;
        *.tgz)       tar xzf "$1"   ;;
        *.zip)       unzip "$1"     ;;
        *.7z)        7z x "$1"      ;;
        *)           echo "extract: unsupported file type: $1" ;;
    esac
}

# Make a directory and cd into it in one step.
mkcd() {
    mkdir -p "$1" && cd "$1" || return
}


# ===================================================================
# 10. LOCAL OVERRIDES
# ===================================================================
# Keep machine-specific or secret stuff (API keys, work-only aliases)
# out of this file / out of dotfile repos by sourcing a separate,
# gitignored file if it exists.
if [ -f "$HOME/.bashrc.local" ]; then
    . "$HOME/.bashrc.local"
fi
