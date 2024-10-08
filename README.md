
# Icotray

Icotray is a CLI tool to create custom acticons in the system tray.
An *acticon* is an icon in the systemtray which has one or multiple actions associated with it.

To download, head over to the [releases](https://github.com/ITCMD/Icotray/releases) and get the most recent copy of the executable.

This repository was forked from the @ITCMD's original.  It hosts the source code for a tool made for @ITCMD by [Mnoronen on Fiverr](https://www.fiverr.com/mnoronen). "<em>I very much recommend him</em>" -- @ITCMD.

If you detect any bugs, please report them on the issues page.

## Table of Contents

- [Icotray](#icotray)
  - [Table of Contents](#table-of-contents)
  - [Legacy Edition](#legacy-edition)
  - [Usage](#usage)
    - [Root Command](#root-command)
    - [Add Command](#add-command)
  - [Project structure](#project-structure)
  - [Technologies](#technologies)
    - [Open Source Libraries Used](#open-source-libraries-used)
  - [Installation](#installation)
  - [Building](#building)
    - [Building locally](#building-locally)
    - [Building using GitLab](#building-using-gitlab)
      - [Required Variables](#required-variables)

## Legacy Edition

The legacy edition of this code is more compatible with non-windows systems, such as a Unix environment. With some simple modifications, it could be built for such systems. The newer edition uses a Microsoft API that is incompatible with other systems. As such, the Legacy edition is available in the [Legacy](https://github.com/ITCMD/Icotray/tree/main/Legacy) folder for those wishing to build for such environments. We would love to include a release for a Unix environment, so if anyone would like to volunteer to do so, let us know.

## Usage

The usage of the CLI can be looked into by running the executable and using the `help` command or `--help` flag, or looking into the source of the commands inside `pkg/cmd/<command>/<command>.go`.  
Nevertheless, below are some descriptions and examples for using the command.

### Root Command

This is the command, which gets executed, if the program is called by itself.  
Doing this, the description and usage of the command will be printed out.

```bash
$ icotray.exe

Icotray is a CLI tool to create custom acticons in the system tray.
An acticon is an icon in the systemtray which has one or multiple actions associated with it.

The acticon will be available in the tray as long as the program is running

Usage:
  icotray [command]

Available Commands:
  add         Adds an acticon to the system tray
  completion  generate the autocompletion script for the specified shell
  help        Help about any command

Flags:
      --config string   config file (default is $HOME/.icotray.yaml)
  -h, --help            help for icotray
  -v, --version         version for icotray

Use "icotray [command] --help" for more information about a command.
```

### In-App Help

>[!TIP]
>The following help documentation can be accessed from the command line by calling `icotray.exe add --help` or `icotray.exe help add`.

```bash
Adds an acticon to the system tray.
The configuration of the acticon may either be passed
through arguments and flags or created using the interactive mode.


# INTERACTIVE
If using the latter, icotray will prompt the values needed for
configuring the acticon. You may sill preconfigure other options when using
the interactive mode. You then have the option to overwrite the fields.
Please note that this feature may not work in all shells.


# ICON
If no own icon is provided via the command flag, the default icon will be used.        
The file type of the icons depends on the operating system.
For example: Windows only accepts .ico files for the icons in the system tray.


# ACTION
The actions of the command can be provided as a list of key-value pairs.
The provided actions will be shown in a list format when clicking on the icon
in the system tray. Using the '--quittable' flag will ad an option to quit the program.
The key represents the title of the item and the key the action which will be run      
when clicking on the item.

## RUN MODES
### DEFAULT
By default the actions will be run using the 'open with default program' method.
The concrete method will be chosen depending on the operating system:
    OSX         :  "open"
    Windows     :  "start"    ->  rundll32.exe url.dll,FileProtocolHandler
    Linux/Other :  "xdg-open"

### COMMAND
By adding a 'cmd:' prefix to the action, it will be executed as a command.
For this the first value after 'cmd:' has to be the command / program
to use for the action (e.g. TASKKILL, TREE, bash ...).

After the first value, the following values are splitted into arguments by whitespace.

So >>"Name"="TASKKILL /PID 1234321"<< will result in:
    Program     :  "TASKKILL"
    Arguments   :  ["/PID", "1234321"]

Sometimes it will be necessary to keep multiple values together as a single argument.
For this case you will have to escape the whitespace using a '\' (backslash).

So >>"Name"="cmd:bash -c echo\ \"Hello\ World\"\ >\ ~/myfile.txt"<< will result in
    Program     :  "bash"
    Arguments   :  ["-c", "echo Hello World > ~/myfile.txt"]

## Default Action
By providing an action with the '--default' / '-d' flag, the action will be
interpreted as the 'default' action. The action passed with the flag will
be run when double-clicking the acticon. In order to open the context menu,
the acticon will have to be right-clicked.


# EXAMPLES
## Basic acticon with some actions
icotray add --title "Acticon Title" --actions "Run xy"="xy","Start Firefox"="firefox"

## The actions may also be provided through separate flags
icotray add --title "Acticon Title" --actions "Run xy"="xy" --actions "Start Firefox"="firefox"

## The menu item names may also be provided as separate list flags
icotray add --title "Acticon Title" --item-name "Run xy" --item-action "xy" --item-name "Start Firefox" --item-action "firefox"

## Acticon with custom icon
icotray add --title "Acticon Title" --icon "/path/to/icon"

## Show message when hovering the acticon
icotray add --title "Acticon Title" --hover "Hovertext"

Usage:
  icotray add [<identifier>] [flags]

Flags:
  -t, --title string              Title of the acticon { -t "<title text>" }
  -i, --icon string               Path to the icon to use for the acticon { -i "<path to icon>"}
  -o, --hover string              Text shown when hovering over the acticon { -o "<hover text>" }
  -d, --default string            Default action which will be executed when double-clicking the acticon { -d "<action>"
  -a, --actions stringToString    Menu-items providing the name of the item together with the action. { -a "<t1>"="<a1>","<t2>"="<a2>","<..>"="<..>" } (default [])
  -n, --item-name stringArray     Alternative way to configure the menu-items. Must be used together with the 'item-action' flag. { -n "<t1>","<t2>","<..>"}
  -c, --item-action stringArray   The action to associate with the 'item-name' flag. For each 'item-action' there must be an 'item-name'. { -c "<a1>","<a2>","<..>" }
  -q, --quittable                 Whether to append a 'quit' Option to the acticon or not { -q }
  -r, --interactive               Build the acticon interactively { -r }
  -p, --print                     Print the command for the final configuration. Useful if used together with the 'interactive' flag { -p }
  -h, --help                      help for add

Global Flags:
      --config string   config file (default is $HOME/.icotray.yaml)
```

## Project structure

In order to keep the project extendable, the project was build using an inofficial standard defined by the core Go dev team. The project structure was build according to the specification of [golang-standards/project-layout](https://github.com/golang-standards/project-layout). You will find the most important information to the folders inside said GitHub repository.  
The most important folder would have to be `cmd`, as the `main` packages for the binaries are located there.

## Technologies

The whole CLI is written using the programming language '[golang](https://golang.org/)' (often seen as [GO](https://golang.org/)).  
In order to simplify the program and not to rediscover the wheel, several open source Go libraries have been used in the project.

### Open Source Libraries Used

The libraries / packages used in the Go project may be found in the `go.mod` file, which is located at the project root.  
The following list is sorted by importance of the library.

- [spf13/cobra](https://github.com/spf13/cobra) - v1.2.1 - A widely used library for creating powerful modern CLI applications
  - [spf13/viper](https://github.com/spf13/viper) - v1.8.1 - A complete configuration solution for Go application including 12-Factor apps. This library is tightly integrated in [spf13/cobra](https://github.com/spf13/cobra)
  - [mitchellh/go-homedir](https://github.com/mitchellh/go-homedir) - v1.1.0 - A small library for detecting the user's home directory without using Cgo
- [lxn/walk](https://github.com/lxn/walk) v0.0.0-20210112085537 - Windows Application Library Kit: Used as an abstraction for the windows system tray API for creating the acticons
- [AlecAivazis/survey](https://github.com/AlecAivazis/survey/v2) - v2.2.14 - Library to create interactive 'survays' using inputs, selects, confirms, texts and so on.
- [jinzhu/copier](https://github.com/jinzhu/copier) - v0.3.2 - Library for creating deep copies of Go structs
- [josephspurrier/goversioninfo](https://github.com/josephspurrier/goversioninfo) - v1.2.0 - Microsoft Windows File Properties/Version Info and Icon Resource Generator for the Go Language
- [go-yaml/yaml](https://github.com/go-yaml/yaml/tree/v3) - v3.0.0 - Package for reading and writing YAML files. This is used to read in some configurations

## Installation

Currently there is no installer for the program. The built executable may either directly be used by executing the file or placed in a folder, which is known in the system PATH. In Windows, this may for example be done by opening the Environment variables (search for 'Environment variables' in the Windows search), and editing the 'Path' variable. Alternatively, the program may be placed in a folder, which is already known to the system. Refer [Usage](#usage) for more information on how to use the CLI.

## Building

In order to build the program, you must either have Go installed, or set up the project as a repository on GitLab. It would also be possible to create a virtualized container, which then builds the program (for example using [Docker](https://www.docker.com/)). If using Docker, a dockerfile would need to be added, where an image is created, which has Go installed. Then the build process can be configured the same way, as building the program locally.

### Building locally

Before being able to build the program locally, Go will have to be installed on the system. Refer to the [Go documentation](https://golang.org/doc/install) in order to get started.

Icotray uses the file located at `<projroot>/assets/data/version.gitlab.yaml` to print out the information on the application version by default.
Currently the file uses the GitLab variables, which will be replaced when running the GitLab pipeline. If you want to build the application locally, you will need to replace the values in the `version.yaml` file with the values you want to use for the application. Also, make sure the correct `version.yml` file is set in the `<projroot>/assets/data.go`. For more information on building the application using GitLab refer to the section below.

Go is built in a way, that the source may be cross-compiled to support multiple Operating Systems and Architectures. For this Go uses the variables `GOOS` and `GOARCH` to determine, how to build the program and which files to use. Refer to [this file](https://github.com/golang/go/blob/go1.16.5/src/go/build/syslist.go) for supported OS and Architectures. Although building the program for different operating systems than windows, it has not been tested. There will probably be some adjustments which will have to be made, before being able to build the program for different operating systems.  

If you have decided which combination of `GOOS` and `GOARCH` to build the program for, following command may be run to build the program, when the current working directory is the root of the project directory.

```bash
# with variables
# as the 'go generate' command will run goversioninfo.exe, this will only work on windows
GOOS=$GOOS GOARCH=$GOARCH go generate ./cmd/icotray
GOOS=$GOOS GOARCH=$GOARCH go build -ldflags "-extldflags '-static'" -o .cmd/icotray/icotray.exe ./cmd/icotray

# example for windows with amd64 architecture
GOOS=windows GOARCH=amd64 go generate ./cmd/icotray
GOOS=windows GOARCH=amd64 go build -ldflags "-extldflags '-static'" -o .cmd/icotray/icotray.exe ./cmd/icotray
```

The built executable can be found under `<projroot>/cmd/icotray/icotray.exe`. The `-o` flag of the `go build` command determines the output directory.

### Building using GitLab

Like GitHub, GitLab is a platform where one can manage Git repositories and use many other features. GitLab provides so called shared `gitlab runners`, which can be used by any account for free. These runners can be used to complete automated tasks either every time when the source in the repository changes, or the process is triggered manually.  
Inside the root of the project directory the `.gitlab-ci.yml` file can be found. This file describes the so called `pipeline` which the `gitlab runners` will go through, when the process is triggered. The `.gitlab-ci.yml` file includes all stages of the process as well as every step from the stages.  

The pipeline also uses some [variables](https://docs.gitlab.com/ee/ci/variables/#custom-cicd-variables), some of which are provided by GitLab (usually with `CI_` prefix) and and others, which we define in the `.gitlab-ci.yml` or the repository settings. These variables are important, as they ar necessary to correctly build the program.

#### Required Variables

| Name            | Description                                                         | Definition | Example value                  |
| --------------- | ------------------------------------------------------------------- | ---------- | ------------------------------ |
| `IT_REPOSITORY` | The name / path of the repository                                   | Project    | `gitlab.com/namespace/project` |
| `IT_VERSION`    | The version of the program, derived from the Git tag and commit SHA | Derived    | `1.2.3-dhwi820d`               |
| `IT_BINARY`     | The name of the output binary                                       | Static     | `icotray.exe`                  |
| `IT_GOOS_WIN`   | The OS to use for windows (only `windows` is sensible)              | Static     | `windows`                      |
| `IT_GOARCH_WIN` | The Architecture to use for windows builds                          | Static     | `amd64`                        |

The Definition column describes, where the variables are defined. `Static` variables are defined in the `.gitlab-ci.yml` file and use static values, `Derived` variables usually are defined in the `.gitlab-ci.yml` files, but their values are derived from other variables. Finally, `Project` variables must be manually configured in the settings of the repository / project.  

To configure `Project` level variables, go to `Settings > CI/CD > Variables > Expand` and add a new variable for each `Project` variable with the specified `Name` as the `Key` and your own value as the `Value`. Make sure to uncheck the `Protect variable` and, optionally `Mask variable` as the variables must be accessible by all jobs.
