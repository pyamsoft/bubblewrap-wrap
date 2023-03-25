# bubblewrap-wrap

A wrapper around [bubblewrap](https://github.com/projectatomic/bubblewrap)
to make it a bit easier to use with normal desktop programs.

## What is this for

bubblewrap-wrap is meant to address this particular case: https://xkcd.com/1200/

Modern environments have most of the actual important user information held in
the same user home directory which is exposed to any program run by the user.

This script uses `bwrap` to create an isolated "HOME" directory per program.
called a `jail`. It trades absolute security for ease of use and should generally
work with most programs out of the box. Unlike a true container, a program run with
`bww` has a read-only view of the host system (some directories are blocked),
but does have things like special namespacing.

The advantage of `bww` is that running a program will not pollute the real home
directory, and all configuration files can be deleted simply by removing the `jail`
directory. `bww` is not a replacement for a Flatpak. You should always elect to use
a Flatpak version of a program if one exists. `bww` is expected to run with interactive
GUI programs, and comes with support for things like X11 and PulseAudio by default. For
server applications, you should use `docker` or another container solution.

By default, `bww` makes use of bubblewrap's ability to have split namespaces
for processes, uts, and also hides the `/dev` `/proc` and `/tmp` folders.

You can share folders outside of the `jail` using the options `--bind-if-exists`. As the
name suggests, the folder will not be created for you, and will only be shared if it already
exists.

## Usage

Assuming both `bww` and the `<program>` you want to run are on your `$PATH` you can do:
```bash
$ bww <program>
```

If you're sick and tired of writing `bww` before everything, you can use the
included `bwrapper` script, which will create a file named `<program>` inside
of the `$HOME/.bww/bin` directory that will call through to `bww` when run. It
is up to the user to configure the `$HOME/.bww/bin` directory to be on the `$PATH`.
`bww` makes no assumptions about the existing configuration, but will follow
these defaults:

Jailed programs will live in `$HOME/.bww/jails/<program>` and `bwrapper` generated
scripts will live in `$HOME/.bww/bin`.

The `$HOME/.bww/jails` directory will be created for you if it does not exist.

## Troubleshooting

Not every program will work with `bww` due to its slightly more restrictive nature,
though many will "just work" out of the box.

If you find a program that does not work, simple possible fixes are things like
the option `--nodrop`, which will not drop the Linux capabilities that some programs
expect to be present, the option `--nodev` which grants access to the real `/dev`
folder, the option `--notmp` which grants access to the real `/tmp` folder.

Often times, a graphical application requires `--dbus`, and many GTK applications
require feature flags like `--p11`, `--gvfs`, or `--dconf`. In some rare cases, the
`--systemd` flag may be required. Each program is specific and you will need to
try various options out.

When in doubt, make sure the program runs on your system normally, and that it runs
"by default" in `bwrap` by using:
```bash
$ bwrap --dev-bind / / <program>
```

If it does, try running it in `bww` with all of the restrictive options turned off,
and if it launches, slowly add back restrictions until the program continues to launch.
Get used to launching programs in the terminal and reading their debugging output,
for example, common error messages including "system bus" or "session bus" would require
the flag `--dbus`.

You may also need to mount directories via `--bind-if-exists`.

## License

GPLv2

```
  The GPLv2 License

#   Copyright (C) 2023  pyamsoft

    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License along
    with this program; if not, write to the Free Software Foundation, Inc.,
    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
```
