# semver.sh - Semantic Versioning

[Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html) version number generation based on git tags.

You already use git to version your source code? Why not use git to version the final product as well?

## Usage

```bash
$ semver
0.0.0+0.4d3d1e1.dirty
```

## Install

### Using [bpkg (Bash Package Manager)](https://bpkg.sh/)

```bash
bpkg install jeremfg/semver.sh
```

NOTE: bpkg can easily be installed by calling `curl -sLo- https://get.bpkg.sh | bash`

### By copying the source directly in your project

Simply copy the file [semver](src/semver) to your project. Under this scenario, there is two ways to use it:

* By [sourcing](#source) it.
* By [executing](#execute) it.

#### Source

The file can be sourced into another bash script, in which case you can simply call the function `semver`.

```bash
#!/usr/bin/env bash
. semver
semver # Invoke
```

#### Execute

The file can be executed like a program, such as via a bash command prompt.

```bash
./semver
```

See `--help` for more detais. By default the generated version number will be printed to stdout

## Theory of operations

This scripts assumes you use git tags to version your software. As such, internally, it will invoke
`git describe --long --broken` to retrieve the necessary information to construct a valid version number following the
[Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html) standard.

NOTE Git tags can be prefixed with a "v" if desired, it will get stripped.

Exemple of valid git tags:

* v1.2.3
* 0.1.0-beta.2+debug

This script adds it's own semver metadata as a suffix at the very end to uniquely identify the current commit, as shown
in the [usage example](#usage). It takes the following format:

> \<nb-commits-ahead>.\<sha>[.dirty|.broken]

1. The number of commits ahead of the closest git tag. This is mostly useful for humans, as a rough measure to the
amount of work since the last version.

1. The commit ID (SHA1) currently checked out in the workspace.
1. A `dirty` (if there are uncommited local changes) or `broken` (if the git repository is corrupted) indication. The
abscence of those indications proves you had a clean copy of the commit. Typically these indications will only appear on
a developper machine.

## Best practices

At least one valid git tag must exist on your repository for this script to work. The best practice is to tag the
initial and very first commit of your repository with `0.0.0`, thus ensuring that any branch stemming from the root will
always have at least this tag to reference. Please be aware that some CI tools, like the commonly used Jenkins git
plugin, won't fetch tags during clone. You may need to enable more advanced cloning behaviors so the git tags are
retrieved during CI/CD builds.

You **must** use annoted tags, and not lightweight tags. A good practice would be to add release notes in the commit
message of your git tag.

If you use GitFlow as a branching model, I recommend to not forget the last step of handling a `release` branch, and
merge it back into the `develop` and `main` branch at the end upon release. This will keep the numbering consistent on
those branches by making that release an ancestor. This was always a good practice to make sure no code was forgotten
behind, but using `semver.sh` gives a second reason to not forget this step.
