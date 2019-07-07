### `cgitrc`

Copy `cgitrc` to `/etc` as root to replace the existing cgit configuration with
an arrangement where repositories in `/repos` are automatically browseable via
the cgit web interface, accessible at `http://HOST/cgit` where `HOST` is replaced
by a domain name, hostname, or IP address through which the HTTP server on this
system may be accessed.

Syntax highlighting is provided by pygments. The `python3-pygments` packages
should be installed. I've used pygments instead of highlight because pygments
works out of the box with files that have no suffix and whose language must
therefore be inferred from their hashbangs.

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
