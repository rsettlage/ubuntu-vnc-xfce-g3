# Headless Ubuntu/Xfce container with VNC/noVNC and Chromium Browser

## accetto/ubuntu-vnc-xfce-chromium-g3

[Docker Hub][this-docker] - [Git Hub][this-github] - [Dockerfile][this-dockerfile] - [Docker Readme][this-readme-dockerhub] - [Changelog][this-changelog] - [Project Readme][this-readme-project] - [Wiki][this-wiki]

![badge-docker-pulls][badge-docker-pulls]
![badge-docker-stars][badge-docker-stars]
![badge-github-release][badge-github-release]
![badge-github-release-date][badge-github-release-date]

- [Headless Ubuntu/Xfce container with VNC/noVNC and Chromium Browser](#headless-ubuntuxfce-container-with-vncnovnc-and-chromium-browser)
  - [accetto/ubuntu-vnc-xfce-chromium-g3](#accettoubuntu-vnc-xfce-chromium-g3)
    - [Image tags](#image-tags)
    - [Ports](#ports)
    - [Volumes](#volumes)
    - [Version sticker](#version-sticker)
  - [Using headless containers](#using-headless-containers)
    - [Over VNC](#over-vnc)
    - [Over noVNC](#over-novnc)
  - [Container user accounts](#container-user-accounts)
  - [Running containers in background (detached)](#running-containers-in-background-detached)
  - [Running containers in foreground (interactively)](#running-containers-in-foreground-interactively)
  - [Startup options and help](#startup-options-and-help)
  - [Issues](#issues)
  - [Credits](#credits)

This repository contains resources for building Docker images based on [Ubuntu 20.04 LTS][docker-ubuntu] with [Xfce][xfce] desktop environment, [VNC][tigervnc]/[noVNC][novnc] servers for headless use and the current [Chromium][chromium] web browser.

This is the **third generation** (G3) of my headless images. The **second generation** (G2) of similar images is contained in the GitHub repositories [accetto/xubuntu-vnc][accetto-github-xubuntu-vnc] and [accetto/xubuntu-vnc-novnc][accetto-github-xubuntu-vnc-novnc]. The **first generation** (G1) of similar images is contained in the GitHub repository [accetto/ubuntu-vnc-xfce-chromium][accetto-github-ubuntu-vnc-xfce-chromium].

More information about the image generations can be found in the [project README][this-readme-project] file and in [Wiki][this-wiki].

**Remark** This container contains the current `Chromium Browser` version for `Ubuntu 18.04 LTS`, because the version for `Ubuntu 20.04 LTS` depends on `snap`, which is not working correctly in Docker at this time.

**Attention** [Chromium Browser][chromium] in this image runs in `--no-sandbox` mode. You should be aware of the implications. The image is intended for testing and development.

The main features and components of the images in the default configuration are:

utilities **ping**, **wget**, **sudo** (Ubuntu distribution)

- current version of JSON processor [jq][jq]
- light-weight [Xfce][xfce] desktop environment (Ubuntu distribution)
- current version of high-performance [TigerVNC][tigervnc] server and client
- current version of [noVNC][novnc] HTML5 clients (full and lite) (TCP port **6901**)
- popular text editor [nano][nano] (Ubuntu distribution)
- lite but advanced graphical editor [mousepad][mousepad] (Ubuntu distribution)
- current version of [tini][tini] as the entry-point initial process (PID 1)
- support for overriding both the container user account and its group
- support of **version sticker** (see below)
- current version of [Chromium Browser][chromium] open-source web browser (version for `Ubuntu 18.04 LTS`)

The history of notable changes is documented in the [CHANGELOG][this-changelog].

![container-screenshot][this-screenshot-container]

### Image tags

The following image tags are regularly maintained and rebuilt:

- `latest` is identical to `vnc-novnc`
- `vnc` implements only VNC
- `vnc-novnc` implements VNC and noVNC

Clicking on the version sticker badge in the [README on Docker Hub][this-readme-dockerhub] reveals more information about the actual configuration of the image.

### Ports

Following **TCP** ports are exposed:

- **5901** used for access over **VNC**
- **6901** used for access over [noVNC][novnc]

### Volumes

The containers do not create or use any external volumes by default. However, the following folders make good mounting points: `/home/headless/Documents/`, `/home/headless/Downloads/`, `/home/headless/Pictures/`, `/home/headless/Public/`

Both **named volumes** and **bind mounts** can be used. More about volumes can be found in the [Docker documentation][docker-doc] (e.g. [Manage data in Docker][docker-doc-managing-data]).

### Version sticker

Version sticker serves multiple purposes that are closer described in [Wiki][this-wiki]. Note that the usage of the version sticker has changed between the generations of images.

The **short version sticker value** identifies the version of the docker image and it is persisted in its *label* when the image is built. It is also shown as a badge in the README file.

The **verbose version sticker value** is used by the CI builder to decide if the image needs to be refreshed. It describes the actual configuration of essential components of the image. It can be revealed by clicking on the version sticker badge in the README file.

The version sticker values are generated by the script `version_sticker.sh`, which is deployed into the startup directory `/dockerstartup`. The script will show a short help if executed with the argument `-h`. There is also a convenient `Version Sticker` launcher on the container desktop.

## Using headless containers

There are two ways, how to use the created headless containers.

The default **VNC user** password is **headless** and it can be changed through the environment variable **VNC_PW**. For example the following container would use the password value **mynewpwd**:

```shell
docker run -d -P -e VNC_PW=mynewpwd accetto/ubuntu-vnc-xfce-chromium-g3
```

The argument `-P` means that the **VNC** and **noVNC** ports exposed by the container will be bound to the next free TCP ports on the host. It is also possible to bind them explicitly. For example:

```shell
docker run -d -p 25901:5901 -p 26901:6901 accetto/ubuntu-vnc-xfce-chromium-g3
```

In this case, if the host is named `mynas`, then **VNC** is accessible as `mynas:25091` and **noVNC** as `mynas:26901`.

### Over VNC

To be able to use the containers over **VNC**, some **VNC Viewer** is needed (e.g. [TigerVNC][tigervnc] or [TightVNC][tightvnc]).

The VNC Viewer should connect to the host running the container, pointing to its TCP port mapped to the container's TCP port **5901**.

For example, if the container has been created on the host called `mynas` using the parameters described above, the VNC Viewer should connect to `mynas:25901`.

### Over noVNC

To be able to use the containers over [noVNC][novnc], an HTML5 capable web browser is needed. It actually means, that any current web browser can be used.

The browser should navigate to the host running the container, pointing to its TCP port mapped to the container's TCP port **6901**.

The containers offer two [noVNC][novnc] clients - the **lite** client and the **full** client with more features. The connection URL differs slightly in both cases. To make it easier, a **simple startup page** is implemented.

For example, if the container has been created on the host called `mynas` using the parameters described above, then the web browser should navigate to `http://mynas:26901`.

The startup page will show two hyperlinks pointing to the both noVNC clients:

- `http://mynas:26901/vnc_lite.html`
- `http://mynas:26901/vnc.html`

It's also possible to provide the password through the links:

- `http://mynas:26901/vnc_lite.html?password=headless`
- `http://mynas:26901/vnc.html?password=headless`

## Container user accounts

Containers created from this image run under the **default application user** (headless, 1001:0) with the default password set also to **headless**. This password can be changed inside the container using the following command:

```shell
passwd
```

Please do not confuse the **default application user** password with the **VNC user** password, because they both have the same default value. However, the former one is used for **sudo** and it can be changed using `passwd` command. The latter one is used for VNC access and it can be changed through the **VNC_PW** environment variable (see above).

The **sudo** command allows user elevation, so the **default application user** can, for example, install new applications.

The following example shows how to install **git**:

```shell
sudo apt-get update
sudo apt-get install -y git
```

Note that the default application account's **group membership** (group zero) does not give it automatically the privileges of the **root** user. Technical details will be described in [Wiki][this-wiki].

The container user ID (1001 by default) can be changed by creating the container using the `--user` parameter, for example:

```shell
docker run -it -P --rm --user 2019 accetto/ubuntu-vnc-xfce-chromium-g3
```

**Remark**: The following feature is available also for this image, but those specific image tags (`fugo`) are currently not built by default. However, you can enable the feature `FEATURES_USER_GROUP_OVERRIDE` in the hook scripts and build them yourself.

The image supports also overriding the container user's group ID (0 by default). However, the image must be built with the argument `ARG_SUPPORT_USER_GROUP_OVERRIDE`. Otherwise the following command line would fail:

```shell
### This will fail (Permission denied)
docker run -it -P --rm --user 2019:2000 accetto/ubuntu-vnc-xfce-g3:vnc-novnc

### This will work (image built with ARG_SUPPORT_USER_GROUP_OVERRIDE)
docker run -it -P --rm --user 2019:2000 accetto/ubuntu-vnc-xfce-g3:vnc-novnc-fugo
```

The images having the tag suffix `-fugo` (**f**eatures **u**ser **g**roup **o**verride) are built just that way.

Note that only numerical ID and GID are supported. Technical details will be described in [Wiki][this-wiki].

## Running containers in background (detached)

The following container will keep running in the background and it will listen on an automatically selected TCP port on the host computer:

```shell
docker run -dP accetto/ubuntu-vnc-xfce-chromium-g3
```

The following container will listen on the host's TCP port **25901**:

```shell
docker run -d -p 25901:5901 accetto/ubuntu-vnc-xfce-chromium-g3
```

The following container will create (or re-use) the local named volume **my\_Downloads** mounted as `/home/headless/Downloads`:

```shell
docker run -d -P -v my_Downloads:/home/headless/Downloads accetto/ubuntu-vnc-xfce-chromium-g3
```

or using the newer syntax with **--mount** flag:

```shell
docker run -d -P --mount source=my_Downloads,target=/home/headless/Downloads accetto/ubuntu-vnc-xfce-chromium-g3
```

## Running containers in foreground (interactively)

The following container can be used interactively:

```shell
docker run -it --rm accetto/ubuntu-vnc-xfce-chromium-g3 bash
```

The opened `bash` session can be used as usual and then closed by entering `^C` (CTRL-C):

```shell
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

headless@cf4a4e01d94b:~$ whoami
headless
headless@cf4a4e01d94b:~$ pwd
/home/headless
headless@cf4a4e01d94b:~$
```

The container will remove itself.

## Startup options and help

The image supports multiple **start-up options** and **start-up modifiers**. There also also two help modes.

The following container will print out the short help and then it will remove itself:

```shell
docker run --rm accetto/ubuntu-vnc-xfce-chromium-g3 --help
```

Example of the short help text:

```text
Container startup script
Usage: /dockerstartup/startup.sh [-v|--version] [-h|--help] [-H|--help-usage] [--(no-)wait] [--(no-)skip-startup] [--(no-)tail-null] [--(no-)tail-vnc] [--(no-)version-sticker] [--(no-)version-sticker-verbose] [--(no-)skip-vnc] [--(no-)skip-novnc] [--(no-)debug] [--(no-)verbose] [--] [<command-1>] ... [<command-n>] ...
        <command>: Optional command with optional arguments. It is executed during startup.
        -v, --version: Prints version
        -h, --help: Prints help
        -H, --help-usage: Extended container usage help.
        --wait, --no-wait: Default background execution mode (on by default)
        --skip-startup, --no-skip-startup: Default foreground execution mode (off by default)
        --tail-null, --no-tail-null: Alternative background execution mode (off by default)
        --tail-vnc, --no-tail-vnc: Alternative background execution mode (off by default)
        --version-sticker, --no-version-sticker: Alternative foreground execution mode (off by default)
        --version-sticker-verbose, --no-version-sticker-verbose: Alternative foreground execution mode (off by default)
        --skip-vnc, --no-skip-vnc: Startup process modifier (off by default)
        --skip-novnc, --no-skip-novnc: Startup process modifier (off by default)
        --debug, --no-debug: Startup process modifier (off by default)
        --verbose, --no-verbose: Startup process modifier (off by default)

Use '-H' or '--help-usage' for extended container usage help.
For more information visit https://github.com/accetto/ubuntu-vnc-xfce-g3
```

The following container will print out the long help and then it will remove itself:

```shell
docker run --rm accetto/ubuntu-vnc-xfce-chromium-g3 --help-usage
```

Example of the long help text:

```text
CONTAINER USAGE:
docker run [<docker-run-options>] accetto/<image>:<tag> [<startup-options>] [<command>]

POSITIONAL ARGUMENTS:
command
    Optional command with optional arguments.
    It will be executed during startup before going waiting, tailing or asleep.
    It is necessary to use the quotes correctly or the 'bash -c "<command>"' pattern.

STARTUP OPTIONS:

--wait, or no options, or unknown option, or empty input
    Default background execution mode.
    Starts the VNC and noVNC servers, if available, then executes the command
    and waits until the VNC server process exits or goes asleep infinitely.
    Container keeps running in the background.

--skip-startup
    Default foreground execution mode.
    Skips the startup procedure, executes the command and exits.
    Be aware that the container user generator will be also skipped.
    Container does not keep running in the background.

--tail-null
    Alternative background execution mode.
    Similar to '--wait', but tails the null device instead of going asleep.
    Container keeps running in the background.

--tail-vnc
    Alternative background execution mode.
    Similar to '--wait', but tails the VNC log instead of waiting until the VNC process exits.
    Falls back to '--tail-null' if the VNC server has not been started.
    Container keeps running in the background.

--version-sticker
    Alternative foreground execution mode.
    Prints out the version sticker info.
    The VNC server is also started by default, if available, because some applications
    need a display to report their versions correctly. It can be suppressed by providing
    also '--skip-vnc'. The '--skip-novnc' option is always enforced automatically.
    Container does not keep running in the background.

--version-sticker-verbose
    Alternative foreground execution mode.
    Similar to '--version-sticker', but prints out the verbose version sticker info and features list.
    Container does not keep running in the background.

--skip-vnc
    Startup process modifier.
    If VNC and noVNC startup should be skipped.
    It also enforces '--skip-novnc'.

--skip-novnc
    Startup process modifier.
    If noVNC startup should be skipped.
    It is also enforced by '--skip-vnc'.

--debug
    Startup process modifier.
    If additional debugging info should be displayed during startup.
    It also enforces option '--verbose'.

--verbose
    Startup process modifier.
    If startup progress messages should be displayed.
    It is also enforced by '--debug'.

--help-usage, -H
    Prints out this extended container usage help and exits.
    The rest of the input is ignored.

--help, -h
    Prints out the short startup script help and exits.
    The rest of the input is ignored.

--version, -v
    Prints out the version of the startup script and exits.
    The rest of the input is ignored.

Use '-h' or '--help' for short startup script help.
Fore more information visit https://github.com/accetto/ubuntu-vnc-xfce-g3
```

## Issues

If you have found a problem or you just have a question, please check the [Issues][this-issues] and the [Wiki][this-wiki] first. Please do not overlook the closed issues.

If you do not find a solution, you can file a new issue. The better you describe the problem, the bigger the chance it'll be solved soon.

## Credits

Credit goes to all the countless people and companies, who contribute to open source community and make so many dreamy things real.

***

<!-- GitHub project common -->

[this-github]: https://github.com/accetto/ubuntu-vnc-xfce-g3/
[this-changelog]: https://github.com/accetto/ubuntu-vnc-xfce-g3/blob/master/CHANGELOG.md
[this-readme-dockerhub]: https://hub.docker.com/r/accetto/ubuntu-vnc-xfce-chromium-g3
[this-readme-project]: https://github.com/accetto/ubuntu-vnc-xfce-g3/blob/master/README.md
[this-wiki]: https://github.com/accetto/ubuntu-vnc-xfce-g3/wiki
[this-issues]: https://github.com/accetto/ubuntu-vnc-xfce-g3/issues

<!-- Docker image specific -->

[this-docker]: https://hub.docker.com/r/accetto/ubuntu-vnc-xfce-chromium-g3/
[this-dockerfile]: https://github.com/accetto/ubuntu-vnc-xfce-g3/blob/master/docker/Dockerfile.xfce.chromium

[this-screenshot-container]: https://raw.githubusercontent.com/accetto/ubuntu-vnc-xfce-g3/master/docker/xfce-chromium/ubuntu-vnc-xfce-chromium.jpg

<!-- Previous generations -->

[accetto-github-xubuntu-vnc]: https://github.com/accetto/xubuntu-vnc/
[accetto-github-xubuntu-vnc-novnc]: https://github.com/accetto/xubuntu-vnc-novnc/
[accetto-github-ubuntu-vnc-xfce-chromium]: https://github.com/accetto/ubuntu-vnc-xfce-chromium

<!-- External links -->

[docker-ubuntu]: https://hub.docker.com/_/ubuntu/

[docker-doc]: https://docs.docker.com/
[docker-doc-managing-data]: https://docs.docker.com/storage/

[jq]: https://stedolan.github.io/jq/
[mousepad]: https://github.com/codebrainz/mousepad
[nano]: https://www.nano-editor.org/
[novnc]: https://github.com/kanaka/noVNC
[tigervnc]: http://tigervnc.org
[tightvnc]: http://www.tightvnc.com
[tini]: https://github.com/krallin/tini
[xfce]: http://www.xfce.org

[chromium]: https://www.chromium.org/Home

<!-- github badges common -->

[badge-github-release]: https://badgen.net/github/release/accetto/ubuntu-vnc-xfce-g3?icon=github&label=release

[badge-github-release-date]: https://img.shields.io/github/release-date/accetto/ubuntu-vnc-xfce-g3?logo=github

<!-- docker badges specific -->

[badge-docker-pulls]: https://badgen.net/docker/pulls/accetto/ubuntu-vnc-xfce-chromium-g3?icon=docker&label=pulls

[badge-docker-stars]: https://badgen.net/docker/stars/accetto/ubuntu-vnc-xfce-chromium-g3?icon=docker&label=stars
