# Sleip's registry

The registry of plugins for [Lokyr](https://lokyr.dev), a terminal for macOS, from which Lokyr's package manager, Sleip, installs them. Lokyr 1.0 is on its way; its plugins' API is experimental until then.

A plugin lives in a public git repository of its own. This repository holds a file of each plugin, `plugins/<owner>/<name>.toml`, of its published versions, each of its repository's tag and the tag's commit; publishing a version is a pull request that adds it, which this repository's CI checks, packages and, once merged, releases.

## Publishing a version

1. The plugin's repository holds its `plugin.toml` at its root, as `sleip new owner/name` makes it, and passes `sleip check` and `sleip test`.
2. Its version is a tag of that repository, whose `plugin.toml` names it.
3. A pull request adds the plugin's file, or a version at its end:

   ```toml
   repository = "https://github.com/acme/hello"

   [[versions]]
   version = "0.1.0"
   tag = "v0.1.0"
   commit = "0123456789abcdef0123456789abcdef01234567"   # the tag's commit, its whole hash
   ```

## What the pull request's check does

- **The owner**: the plugin's owner is the pull request's author, its GitHub account, or an organization that the author is a public member of.
- **The name**: an owner and a name of lowercase letters, digits and `-`, from a letter, 64 characters at most each; `lokyr/*` is Lokyr's first-party plugins'; a name is unique across owners, as Lokyr knows a plugin by its name alone, so that `acme/hello` and `bob/hello` never meet.
- **The versions published** stay as they were: a later version takes a version's place, or its yank, `yanked = true`.
- **Each new version** is cloned at its tag, which must name its commit; its manifest must be of the plugin's name and of the version; it is checked as `sleip check` checks it, its tests run as `sleip test` runs them, and its archive is packaged.
- The check tells what it found in a comment of the pull request.

## Merging

- A new plugin, the maintainer reviews.
- A new version that asks for nothing that the version published last did not, no capability, no host, path or variable of one, and no command in `[uses] commands`, merges by itself; one that asks for more, the maintainer reviews.
- A yank merges by itself; a change of anything else, the maintainer reviews.

## The archives

As a version merges, the CI packages its archive again from its commit, alike, and uploads it to a release of this repository of its own, `<owner>.<name>.<version>`, which stays as it was whatever later happens to the plugin's repository.

The registry's signed index, which Sleip reads, and its site at `pkg.lokyr.dev` come next.

## Sleip in the CI

The checks run a build of Sleip for Linux, a release of this repository, which `.github/actions/sleip/action.yml` pins by its SHA-256.

## License

This repository's files are of the MIT license, as `LICENSE` tells; each plugin is of its own.
