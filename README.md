# Sleip's registry

The registry of plugins for [Lokyr](https://lokyr.dev), a terminal for macOS, from which Lokyr's package manager, Sleip, installs them. Lokyr 1.0 is on its way; its plugins' API is experimental until then.

A plugin lives in a public git repository of its own. This repository holds a file of each plugin, `plugins/<owner>/<name>.toml`, of its published versions, each of its repository's tag and the tag's commit; publishing a version is a pull request that adds it, which this repository's CI checks, packages and, once merged, releases.

## Plugins

The plugins published, as the registry's signed index holds them, which its CI writes here after each release; [pkg.lokyr.dev](https://pkg.lokyr.dev) shows and searches them.

<!-- plugins:start -->
| Plugin | Version | Description | Capabilities |
|---|---|---|---|
| [sephiroth74/hello](https://pkg.lokyr.dev/plugins/sephiroth74/hello/) | 0.1.10 | The registry's plugin of its tests: its command, Say Hello, shows a notice. | clipboard-read, clipboard-write |
<!-- plugins:end -->

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

- **The changes**: the pull request's own, since the commit that it shares with `main`, each judged against `main` as it is now, so that a branch made before another plugin's version merged needs no rebase for it; a plugin's file is a plain file, never a link nor a submodule; a file outside `plugins/`, the maintainer reviews.
- **The owner**: the plugin's owner is the pull request's author, its GitHub account, or an organization that the author is a public member of; a push to the pull request of another account than the author's, the maintainer reviews.
- **The name**: an owner and a name of lowercase letters, digits and `-`, from a letter, 64 characters at most each; `lokyr/*` is Lokyr's first-party plugins'; a name is unique across owners, as Lokyr knows a plugin by its name alone, so that `acme/hello` and `bob/hello` never meet, against `main`'s plugins and the pull request's together; the first-party plugins' names, as `tabs` or `search`, and those of the app's own commands, as `app`, `window` and `pane`, are Lokyr's.
- **The versions published** stay as they were: a later version takes a version's place, or its yank, `yanked = true`.
- **Each new version** is fetched at its tag, which must name its commit, a tag's name as git takes it and as no revision reads it, of its commit alone; its manifest must be of the plugin's name and of the version; it is checked as `sleip check` checks it, its tests run as `sleip test` runs them, and its archive is packaged.
- The check tells what it found in a comment of the pull request.

## Merging

- A new plugin, the maintainer reviews.
- A new version that asks for nothing that the version published last did not, no capability, no host, path or variable of one, and no command in `[uses] commands`, merges by itself; one that asks for more, the maintainer reviews.
- A yank merges by itself; a change of anything else, the maintainer reviews.
- The workflows, their actions and `keys.json` are the maintainer's, as `.github/CODEOWNERS` says: a pull request that changes them waits for the maintainer's review, whoever opened it.

## The archives

As a version merges, the CI packages its archive again from its commit, alike, and uploads it to a release of this repository of its own, `<owner>.<name>.<version>`, which stays as it was whatever later happens to the plugin's repository.

## The index

After the releases, the CI of `main` writes the registry's index, which Sleip reads: `config.json`, `keys.json` and a file of each plugin, a line of each version released, each file signed whole by the index key, which the environment `index` holds for `main` alone. A line, once signed, stays as it was, its yank alone changing: each run keeps the lines of the index deployed and reads the new versions alone of their archives, so that an asset replaced in a release changes no line; a version that fails is told, the others published. `keys.json`, in this repository, lists the index keys, signed by the maintainer's root key, which stays offline and whose public part Sleip holds: a host that serves the index cannot change it, and a stolen index key is taken off the list. The same CI writes the registry's site of the index, a page of each plugin, of its versions, their capabilities and checksums, and its README, searched by Pagefind, and deploys the site and the index to GitHub Pages, at [pkg.lokyr.dev](https://pkg.lokyr.dev), the index's files at their paths: `config.json`, `keys.json` and `index/<owner>/<name>`.

## Sleip in the CI

The checks run a build of Sleip for Linux, a release of this repository, which `.github/actions/sleip/action.yml` pins by its SHA-256. The job that runs a pull request's plugins' code, of Luau's interpreter alone, holds no right of the repository's; the jobs that comment, merge and release take its report as data, out of their workspace, and give their token to `gh` alone.

## License

This repository's files are of the MIT license, as `LICENSE` tells; each plugin is of its own.
