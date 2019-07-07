### `cgitrc`

Copy `cgitrc` to `/etc` as root to replace the existing cgit configuration with
an arrangement where repositories in `/repos` are automatically browseable via
the cgit web interface, accessible at `http://HOST/cgit` where `HOST` is replaced
by a domain name, hostname, or IP address through which the HTTP server on this
system may be accessed.

Backing up the existing `/etc/cgitrc` first would be a good idea. One way to
achieve this is to run `./deploy-cgitrc` to perform the copying, as it backs up
the old file unless the files are the same (in which case no backup is made, to
avoid overwriting an existing backup with a a copy of the current file).

Syntax highlighting is provided by pygments. The `python3-pygments` packages
should be installed. I've used pygments instead of highlight because pygments
works out of the box with files that have no suffix and whose language must
therefore be inferred from their hashbangs.

A `README.md` at the root of a repository is used as documentation. This isn't
really robust enough; ideally, a variety of files could serve the purpose, and
tie-breaking would distinguish which were best. (This can be done but I haven't
written the configuration for it.)

All repositories are listed on one page, unless there's a huge lot of them.

### `acl-repos`

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

### `redirect.conf`

Running `./deploy-redirect` as root installs `redirect.conf` as a configuration
file for the Apache2 web server and enables it.

This configuration file redirects traffic from the root of the web server to
the `cgit` directory. Note that it does *not* prevents files in the root or
elsewhere from being accessed by a correct URL, if they otherwise could.
