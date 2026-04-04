# BMSU: Back My "Stuff" Up

Gain full control over your backups instead of using cloud backup services.

This is directly using [rsync](https://github.com/RsyncProject/rsync) under
the hood which means you can perform backups to an external drive, network
mounted drive or over SSH to a remote server you control. Restoring is
painless too!

*It's a single file Bash script that works on Linux (including WSL 2) and
macOS.*

**If you've never used rsync**, that's no problem. The out of the box defaults
will make it easy for you to start backing up your important files.

**If you already know and use rsync**, that's great because there's no new API
to learn and no leaky abstractions if you want to do custom rsync things.

*In any case, in 3 lines of configuration you'll be all set to backup and
restore with lots of flexibility.*

## 🧠 Why Not Use rsync Directly?

You can but what if you:

- Have a dozen different directories you want backed up
- Want to ignore a bunch of cache / temporary files in your backup paths
- Have a few isolated backups that you want to run (desktop, laptop, etc.)

You'll find yourself running really long `rsync` commands with lots of source
paths and `--exclude=` patterns. You can create a function alias for this but
we can do better, both to remove duplication and add safety.

#### Since we're focused on backups, we can add a few quality of life features:

- Abstract source paths, destination, exclude patterns and more into a config file
- Make sure the destination path is mounted if it's a mount path
    - Without this, I once accidentally backed up 182 GB to the wrong drive
- Confirm the destination path is readable by your user
- Create the destination path if it's not already created
- Confirm each source path exists before calling `rsync`
- Confirm your source paths don't contain the destination path and vice versa
- Confirm trailing slashes are consistent (rsync treats this differently)
- Confirm your restore path is safe with the combo of rsync flags being used

## 🪄 Does It Work?!?

Yes, I've successfully backed up about 1 TB of data a few thousand times since
2018. This even includes using WSL 2 where file system performance for Windows
volume mounts is very slow. It's solid, and remember the underlying script is
calling `rsync` directly, if rsync works for you then this will too.

This script is an evolved version of [this blog post's
solution](https://nickjanetakis.com/blog/automatic-offline-file-backups-with-bash-and-rsync)
that I wrote in 2018. I've been using it on my system since then. The core of
this script is the same, I cleaned it up and made it more configurable.

It's not AI slop that was created HoPiNg FoR tHe BeSt. I use it every day to
backup my *life's work* from my desktop to external drives. You'll be surprised
at how little "real" code there is, it's basically 1 line to call rsync with a
thousand lines of defensive checks, config parsing and quality of life
features.

## ⚡️ Install

*If you're using [my dotfiles](https://github.com/nickjj/dotfiles), bmsu
comes pre-installed by default!*

There's only 1 dependency on `rsync`, your package manager (pacman, brew, apt,
etc.) likely has it.

This script uses Bash 4.4+ features (released in 2016), check your version with
`bash --version`. If you're on macOS you'll want to `brew install bash` to get
a modern version instead of using the 3.2 default.

#### One liner

```sh
# Install it on a well known system path that will work on most systems.
sudo curl \
  -L https://raw.githubusercontent.com/nickjj/bmsu/0.4.1/src/bmsu \
  -o /usr/local/bin/bmsu && sudo chmod +x /usr/local/bin/bmsu
```

Nothing about this script requires sudo or it living in `/usr/local/bin` but
that path is available on most Linux distros, macOS and within WSL 2 on
Windows. You're more than welcome to install it for your specific user, such as
in `~/.local/bin` if you already have that set up and on your system path.

## ⚙️ Configuration

When running `bmsu` it depends on having a "profile", this has configuration
such as defining your source and destination paths, exclude patterns and more.

A default profile gets created for you and all custom profiles you create
inherit from this default. You can overwrite any values as desired.

Unless you're backing up to multiple destinations or syncing files between
multiple devices (desktop, laptop, phone, etc.) the default is all you need.

All profiles are saved in: `${XDG_CONFIG_HOME}/.config/bmsu`

#### Default profile

This gets generated for you automatically when you first run `bmsu`:

```sh
#!/usr/bin/env bash

# Default to not using debug mode but allow running DEBUG=1 bmsu. If you
# want debug on by default set ${DEBUG-1} below. Even if it's enabled
# by default you can still run commands without it using DEBUG= bmsu.
export DEBUG="${DEBUG-}"

# Flags and arguments that will get passed to rsync by default. If you leave
# this as is it will fallback to what the script internally defines.
#
# - Left as is OR commented out            -> default options get used
# - Remove () and set an empty value OR "" -> no options will get set
# - Add your own array items               -> only your options get used
#
# For example, if you don't like verbose mode you can set this:
#   BMSU_DEFAULT_RSYNC_OPTS=("--archive" "--relative")
export BMSU_DEFAULT_RSYNC_OPTS=()

# Where do you want the source paths to be sync'd to? This is your backup
# destination. The path must not end with a forward slash.
#
# For example, I have mine set to BMSU_DESTINATION=/data/backup/desktop and in
# this case /data/backup is a mount pointing to an external drive. Inside of
# that, the desktop directory is where my desktop machine's backups go.
#
# It can be a local mount, network mount or SSH path. If it's a mount, it must
# be an absolute path (starts with '/') or be a variable that resolves to one.
export BMSU_DESTINATION=()

# Which paths do you want backed up? They must not end with a forward slash
# and all paths must be an absolute path or be a variable that resolves to one.
#
# It defaults to nothing, if you want ideas, here's what mine is set to:
#
# export BMSU_SOURCES=(
#   "${HOME}/.config"
#   "${HOME}/.docker/config.json"
#   "${HOME}/.ssh"
#   "${HOME}/src"
#   "/data/storage/docs"
#   "/data/storage/games/backups"
#   "/data/storage/media"
# )
export BMSU_SOURCES=()

# File and directory patterns that will get excluded. This avoids backing up
# cache, temporary and other generated files that exist in your source paths.
#
# All 3 variables below follow the same rules as BMSU_DEFAULT_RSYNC_OPTS for
# setting their values (keep it as is, empty or add in your own custom values).
#
# Unless you have a strong reason you'll probably only end up setting EXTRAS,
# in which case please open a PR if they're semi-general purpose patterns!
export BMSU_EXCLUDE_PATTERNS=()

# Append items to the default list.
export BMSU_EXCLUDE_PATTERNS_EXTRAS=()

# Remove items from the default list.
export BMSU_EXCLUDE_PATTERNS_SKIP=()

# This remains unused unless you run `bmsu restore`.
#
# When you're ready to restore, you'll want to set "/", since --relative is
# always used rsync knows to create your source paths where they should be.
# For testing you can use "/tmp" to see which files really get restored.
#
# Tip: you can use the rsync --update flag to avoid overwriting newer files
# that exist on your source. Be careful with --delete too!
#
# Debug mode is always enabled for it, there's also --dry-run and temporarily
# using "/tmp" to verify your paths before you perform your restore on "/".
#
# P.S., the special /./ path you'll see in the final rsync command configures
# rsync to only restore the path to the right of it, this avoids copying your
# BMSU_DESTINATION as part of the restore path.
export BMSU_RESTORE_PATH=
```

## 🚀 Usage

Here's a few ways to use this script:

```sh
# Use the default profile. This is the most common case if you're backing stuff
# up from A to B and B is the same for all of A's paths. This is what I do.
bmsu

# Provide more output and requires typing 'y' to confirm running the backup,
# this will print the exact rsync command that's going to run ahead of time.
DEBUG=1 bmsu

# Instead of performing a backup, it prints your config so you can see the values
# of everything in a zero risk way. Debug mode will output this automatically.
bmsu config

# If you have more than 1 backup profile, pass it to the BMSU_PROFILE= env var.
#
# This could be handy if you have a few files you want backed up to target A and
# also have a different set of files going to target B. This lets you have
# separate configs with their own sources / targets / options for each profile.
#
# All options inherit from the default profile, you can overwrite them as desired.
BMSU_PROFILE=laptop bmsu

# Restore source files from your backup destination to the path that
# BMSU_RESTORE_PATH is set to. Given how dangerous this command can be if it's
# misconfigured, debug mode is always enabled for it.
bmsu restore
```

Any flags you pass in will directly get forwarded to rsync, for example:

```sh
# See what will get transferred without really doing it.
bmsu --dry-run

# Ensure target files get deleted that don't exist in the sources. I run this
# a lot but prefer keeping it as something I always explicitly type to be safe.
bmsu --delete

# Pass as many flags as you want in any order rsync accepts them.
bmsu --dry-run --delete

# Passing rsync flags here helps see what will be used when you backup or restore.
bmsu config --dry-run

# A quick tip for restoring from a backup, but avoid clobbering newer files
# that exist on your source system.
bmsu restore --update
```

Quality of life commands to manage your profile configs:

```sh
# List all of your profile configs.
bmsu config list

# Show your profile's config, supports BMSU_PROFILE.
bmsu config show

# Show the default profile config for your downloaded version of bmsu, this could
# be helpful in case future updates adjust the config options or you want to see
# the defaults. If you're ever missing a config item, bmsu will let you know too.
bmsu config show example

# Show a diff of your profile config vs the example config, this could be helpful
# to compare any changes you've made. Supports BMSU_PROFILE.
bmsu config diff

# Edit your profile's config, supports BMSU_PROFILE.
bmsu config edit

# Create a new profile config and then edit it.
bmsu config new PROFILE_NAME

# Delete your profile's config (with confirmation), supports BMSU_PROFILE.
bmsu config delete
```

Details about bmsu itself:

```sh
# Show the version you have currently.
bmsu --version

# Show the latest version and offer to update if a newer version exists.
bmsu --version-latest

# Show the help.
bmsu --help
```

## 🤝 Feedback and Code Contributions

You're more than welcome to offer suggestions and improvements. There's a [test
suite](./tests/integration) too.

If you consider opening an issue, discussion or pull request please remember, I
am 1 person doing this as a hobby. I maintain dozens of projects and would love
to collaborate on anything related to this project but please don't blindly
drop a bunch of agent generated slop onto this project.

If you want to use AI or agents to help please do but there's an expectation
that you understand the code you're submitting. We're dealing with Bash here,
life is too short to deeply analyze a 200 line AI rewrite diff because it
hallucinated errors instead of changing double quotes to single quotes in 1
spot.

## 🍀 Donations

GitHub tips is available in the side bar of this repo.

I'm not expecting anything. I'm doing this because I enjoy it but if this tool
helps save you time, provides confidence in your backups or helps you avoid
paying a cloud provider to host your backups, tips would be greatly appreciated!

## 👀 Who Am I and Can You Trust Me?

Good question, I would be asking the same thing for anything related to
backups. The code is available to look at. You're more than welcome to audit
it. Nothing phones home or anything crazy like that.

I'm just a dude on the internet who has been building and deploying web apps
for 20+ years. I [blog](https://nickjanetakis.com/blog) and create [YouTube
videos](https://www.youtube.com/c/nickjanetakis) about everything I've learned
along the way and have a bunch of other [open source
projects](https://github.com/nickjj?tab=repositories).
