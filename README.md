# bubblewrap-wrap

A wrapper around [bubblewrap](https://github.com/projectatomic/bubblewrap) 
to make it a bit easier to use with normal desktop  
programs.

## What is this for

bubblewrap-wrap is meant to address this particular case: https://xkcd.com/1200/

Modern environments have most of the actual important user information held in  
the same user home directory which is exposed to any program run by the user.  

Effectively, this is a script which uses bubblewrap to isolate the HOME  
directory for an untrusted running process. While the script may provide a  
general secure sandbox environment, the first and only goal is to make it  
as painless as possible to isolate userspace non-privileged programs from each  
other.

Each program run using `bww` will have its own, isolated home directory and a  
readonly view of the host filesystem. It will inherit the host theme  
configuration and will be unable to modify the theme configuration in its  
isolated environment.

bubblewrap-wrap trades absolute security for ease of use by design - things  
like PulseAudio and the X11 socket are exposed by default to make normal  
programs easier to use, thus the wrapper is no more secure than runing a  
program normally under a normal environment.

One advantage of isolating the home of each program however is that programs  
will not be able to see the other configuration and related files that you  
store on the machine, and cleaning up any installed program is as simple as  
deleting its own specific directory instead of having to hunt through each  
folder in your user directory for configuration specific files.

By default, `bww` makes use of bubblewrap's ability to have split namespaces  
for processes, uts, and also hides the `/dev` `/proc` and `/tmp` folders. It also  
launches each program in a new session, and exposes the `dbus` and `pulseaudio`  
sockets, as well as providing the option to expose the `dconf` socket.

## Usage

Assuming both `bww` is on your `$PATH` you can do:
```
$ bww <program>
```

If you're sick and tired of writing `bww` before everything, you can use the  
included `bwrapper` script, which will create a file named `<program>` inside  
of the `$HOME/bin` directory that will call through to `bww` when run. It  
is up to the user to configure the `$HOME/bin` directory to be on the `$PATH`.  
`bww` makes no assumptions about the existing configuration, but will follow  
these defaults:

Jailed programs will live in `$HOME/.jails/<program>` and `bwrapper` generated  
scripts will live in `$HOME/bin`. The `.jails` directory will be created for  
you if it does not exist.

## License

GPLv2

```
  The GPLv2 License

    Copyright (C) 2018  Peter Kenji Yamanaka

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
