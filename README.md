# setup-cgit - unofficial cgit how-to and scripts

*Written in 2019 by Eliah Kagan \<degeneracypressure@gmail.com\>.*

*To the extent possible under law, the author(s) have dedicated all copyright
and related and neighboring rights to this software to the public domain
worldwide. This software is distributed without any warranty.*

*You should have received a copy of the CC0 Public Domain Dedication along with
this software. If not, see
<http://creativecommons.org/publicdomain/zero/1.0/>.*

The scripts and associated files in this repository automate and document the
installation and configuration of cgit that works well for me in the
undemanding environment of a web server that is not directly accessible on the
public internet and that has only a handful of users.

The scripts can be run individually, or all together by just running
`./setup-all`. **They should all be run from the directory in which they
reside** unless you *really* know what you are doing. They look in the current
directory to find the files they use, so unless you're deliberately making them
use different files instead, you should make sure to `cd` to the directory
they're in. For example, `cd /path/to/this/repo` followed by `./setup-all`
would do the right thing (but `/path/to/this/repo/setup-all` would do the wrong
thing).

I learned some of the techniques used and described here from
[Floating Octothorpe](https://floatingoctothorpe.uk/about.html), especially:

- [Setting up cgit on Debian](https://floatingoctothorpe.uk/2017/setting-up-cgit-on-debian.html)
- [Markdown readme files in cgit](https://floatingoctothorpe.uk/2018/markdown-readme-files-in-cgit.html)

----

The remainder of this document describes the individual steps that should be
taken to set up cgit. It is organized by the files in this repository that
relate to each step.

## Installing the services

cgit needs git and a web server. Other software comes in handy to provide
syntax highlighting. Running `./install-services` installs packages for all
this and performs site-nonspecific configuration. cgid is enabled for
performance. Debian or a derivative (such as Ubuntu) is assumed.

## The `cgitrc` configuration file for cgit

Copy `cgitrc` to `/etc` as root to replace the existing cgit configuration with
an arrangement where repositories in `/repos` are automatically browseable via
the cgit web interface, accessible at `http://HOST/cgit` where `HOST` is replaced
by a domain name, hostname, or IP address through which the HTTP server on this
system may be accessed.

Backing up the existing `/etc/cgitrc` first would be a good idea. One way to
achieve this is to run `./deploy-cgitrc` to perform the copying, as it backs up
the old file unless the files are the same (in which case no backup is made, to
avoid overwriting an existing backup with a copy of the current file).

Syntax highlighting is provided by pygments. The `python3-pygments` package
should be installed. I've used pygments instead of highlight because pygments
works out of the box with files that have no suffix and whose language must
therefore be inferred from their hashbangs.

A `README.md` at the root of a repository is used as documentation. This isn't
really robust enough; ideally, a variety of files could serve the purpose, and
tie-breaking would distinguish which were best. (This can be done but I haven't
written the configuration for it.)

All repositories are listed on one page, unless there's a huge lot of them.

## `about-cgit.md`

Place this in `/var/www/html` (or wherever its location is specified in
`cgitrc`) to create an *about* tab on the main page with the contents of the
file rendered in it. Running `./deploy-about-cgit` as root (or some other user
wit write access in `/var/www/html`) will do this.

Or remove or comment out the `root-readme` line in `cgitrc`.

## The `acl-repos` and `unacl-repos` commands

Running `./deploy-acl-repos` as root places `acl-repos` and `unacl-repos`
commands in `/usr/local/bin`.

The `acl-repos` command applies ACLs recursively to `/repos`, and sets defaults
that apply to subsequently created files, so that the `www-data` user can read
*everything* in `/repos`. **This is potentially dangerous if sensitive data are
ever stored there!**

The `unacl-repos` command recursively removes *all* ACLs from `/repos`. This
may include ACLs that have nothing to do with those created or changed by a
previous run of `acl-repos`.

Neither of these commands is expected to succeed except when run as root.

## The `redirect.conf` configuration file for Apache

Running `./deploy-redirect` as root installs `redirect.conf` as a configuration
file for the Apache2 web server and enables it.

This configuration file redirects traffic from the root of the web server to
the `cgit` directory. Note that it does *not* prevent files in the root or
elsewhere from being accessed by a correct URL, if they otherwise could.
