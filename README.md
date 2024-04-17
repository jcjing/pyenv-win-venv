<h1 align="center">pyenv-win-venv</h1>

[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/arzkar)

A CLI to manage virtual envs with pyenv-win<br>
To report issues for the CLI, open an issue at https://github.com/pyenv-win/pyenv-win-venv/issues

# Installation

## Dependencies

This script depends on the [pyenv-win](https://github.com/pyenv-win/pyenv-win) so it needs to be installed system to run this script.

## Power Shell

```
Invoke-WebRequest -UseBasicParsing -Uri "https://raw.githubusercontent.com/pyenv-win/pyenv-win-venv/main/bin/install-pyenv-win-venv.ps1" -OutFile "$HOME\install-pyenv-win-venv.ps1";
&"$HOME\install-pyenv-win-venv.ps1"
```

**Note:** Skip the [Add System Settings](#add-system-settings) Section

## Git

```
git clone https://github.com/pyenv-win/pyenv-win-venv "$HOME\.pyenv-win-venv"
```

You need to add the `\bin` path to your environment variables using the following steps.

### Add System Settings

Adding the following paths to your USER PATH variable in order to access the pyenv-win-venv command

```pwsh
[System.Environment]::SetEnvironmentVariable('path', $env:USERPROFILE + "\.pyenv-win-venv\bin;"  + [System.Environment]::GetEnvironmentVariable('path', "User"),"User")
```

# Update

Automatically using `pyenv-venv update self` (Recommended)

## Git (If the CLI was installed using Git)

Using `git pull`:

Go to `%USERPROFILE%\.pyenv-win-venv` (which is your installed path) and run `git pull`

## Power Shell (If the CLI was installed using the PowerScript Installation Script)

```
Invoke-WebRequest -UseBasicParsing -Uri "https://raw.githubusercontent.com/pyenv-win/pyenv-win-venv/main/bin/install-pyenv-win-venv.ps1" -OutFile "$HOME\install-pyenv-win-venv.ps1";
&"$HOME\install-pyenv-win-venv.ps1"
```

# Uninstallation

## CLI

```
pyenv-venv uninstall self
```

## Power Shell

```
Invoke-WebRequest -UseBasicParsing -Uri "https://raw.githubusercontent.com/pyenv-win/pyenv-win-venv/main/bin/install-pyenv-win-venv.ps1" -OutFile "$HOME\install-pyenv-win-venv.ps1";
&"$HOME\install-pyenv-win-venv.ps1" -Uninstall
```

# Usage

```
> pyenv-win-venv
    pyenv-win-venv v0.6
    Copyright (c) Arbaaz Laskar <arzkar.dev@gmail.com>

    Usage: pyenv-win-venv <command> <args>

    A CLI to manage virtual envs with pyenv-win

    Commands:
    init                search for .python-version file in the
                        current directory and activate the env
    activate            activate an env
    deactivate          deactivate an env
    install             install an env
    uninstall           uninstall an env
    uninstall self      uninstall the CLI and its envs
    list <command>      list all installed envs/python versions
    local               set the given env in .python-version file
    config              show the app directory
    update self         update the CLI to the latest version
    which <command>     show the full path to an executable
    help <command>      show the CLI/<command> menu

    Flags:
    debug               To show debug log
```

**Note:** `pyenv-venv` is an alias for `pyenv-win-venv` so either one can be used to call the CLI.

# Example

- To install an env using Python v3.8.5 (should be already installed in the system using `pyenv install 3.8.5`)

```
pyenv-venv install 3.8.5 env_name
```

- To uninstall an env

```
pyenv-venv uninstall env_name
```

- To activate an env

```
pyenv-venv activate env_name
```

- To deactivate an env

```
pyenv-venv deactivate
```

- To list all installed envs

```
pyenv-venv list envs
```

- To list all installed python versions

```
pyenv-venv list python
```

- To set an env to the `.python-version` file

```
pyenv-venv local env_name
```

- To show the app directory

```
pyenv-venv config
```

- To update the CLI to the latest version

```
pyenv-venv update self
```

- To show the full path to the executable

```
pyenv-venv which <exec_name>
```

- To get help for each command

```
pyenv-venv help install
```

# Note

## Env automatic activation using `.python-version` file

- You can set the env for a directory using a `.python-version`
  file and the CLI can automatically activate the env if a shell is
  opened in that directory.

- `.python-version` file: It should only contain the name of the env and can be created by manually or by using the command: `pyenv-venv local env_name`

- You can manually activate the env if the directory has a `.python-version` file by calling `pyenv-venv init`

- To enable the automatic feature, you need to add `pyenv-venv init` to your the PowerShell Profile.
  Steps to do this:

  - First check if you already have a powershell profile.

    ```
    Test-Path $profile
    ```

    If its `False`, then you need to create a new profile.

  - Create a new profile using:

    ```
    New-Item -path $profile -type file –force
    ```

    The location to the profile will be shown on the shell.

  - Open the `profile.ps1` file and append the following line.

    ```
    pyenv-venv init
    ```

    Save and restart the shell.

**Note:** If you want the CLI to search for a `.python-version` file by traversing from the current working directory to the root till it finds the file, use `pyenv-venv init root`

# Note Git-bash
  - for every Venv, need to copy/make a deactivate script

  - Added to to .bashrc to allow for activate and deactivation

  ```
  # pyenv-venv functions for activating and deactivating
  # Helper function to source activation script
  function activate_helper() {
      # source the activation script
      source "$(cygpath -u "$PYENV_VENV_ENVS/$1/Scripts/activate")"
      # don't use the shell prompt from the activation script
      PS1=$_OLD_VIRTUAL_PS1
      export PS1
      # change the venv_prompt to [env]
      VIRTUAL_ENV_PROMPT="[$1]"
      export VIRTUAL_ENV_PROMPT
  }
  
  # Helper function to source deactivation script
  function deactivate_helper() {
      if [[ -n "${VIRTUAL_ENV:-}" ]]; then
          # set from activation script
          deactivate
      fi
  }
  
  pyenv-venv() {
      if [[ "$1" == "activate" ]]; then
          if (( $# < 2 )); then
              printf "Usage: pyenv-venv activate <env_name>\n\n"
              printf "Parameters:\nenv_name   name of the installed virtualenv\n\n"
              printf "Example: pyenv-venv activate test_env\n"
          else
              activate_helper "$2"
          fi
      elif [[ "$1" == "deactivate" ]]; then
          deactivate_helper
      elif [[ "$1" == "init" ]]; then
          echo "init"
      else
          command pyenv-venv "$@"
      fi
  }

  python() {
      if [[ -n "${VIRTUAL_ENV:-}" ]]; then
          winpty python.exe "$@"
      else
          command python "$@"
      fi
  }
  ```