# Shell Integration

Did I mention that Homebrew embraces UNIX?

Homebrew provides a couple of shell integration points.

## Completion Scripts

There are completion scripts for Bash, Fish and Zsh. The Bash script
is usually more up-to-date, since I maintain it. Improvements are
welcome and encouraged.

These scripts are used like any other completion, by sourcing from your
profile. From my dotfiles:

    for comp in \
        /usr/local/etc/bash_completion.d/git-completion.bash \
        $HOME/homebrew/Library/Contributions/brew_bash_completion.sh \
        $HOME/source/custom-django/extras/django_bash_completion \
        $HOME/bin/fab_completion.bash
    do
        [[ -e $comp ]] && source $comp
    done

## Environmental Variables

When installing in debug or interactive mode, Homebrew may drop into
a shell in the temporary folder being used to build the software package.

    $ brew install -d something
    $ brew install -i something

When this happens, Homebrew will set HOMEBREW\_DEBUG\_INSTALL to the
name of the formula being brewed. This variable can be used to change
your shell prompt. From my dotfiles:

    function set_prompt() {
      [[ -n $HOMEBREW_DEBUG_INSTALL ]] && \
        p_homebrew"${BROWN}Homebrew:${COLOR_NONE} debugging ${HOMEBREW_DEBUG_INSTALL}\n"

      p_git="$(parse_git_branch)"
      export PS1="[\w] ${p_git}${COLOR_NONE}\n${p_homebrew}\$ "
    }

    export PROMPT_COMMAND=set_prompt
