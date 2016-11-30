# github-semantic-version

> Automated semantic version releases powered by Github Issues.

[![travis build](https://img.shields.io/travis/ericclemmons/github-semantic-version.svg)](https://travis-ci.org/ericclemmons/github-semantic-version)
[![version](https://img.shields.io/npm/v/github-semantic-version.svg)](http://npm.im/github-semantic-version)
[![downloads](https://img.shields.io/npm/dm/github-semantic-version.svg)](http://npm-stat.com/charts.html?package=github-semantic-version)
[![MIT License](https://img.shields.io/npm/l/github-semantic-version.svg)](http://opensource.org/licenses/MIT)

- - -

## Getting Started

### 1. Install

```shell
$ npm install --save-dev github-semantic-version
```

### 2. Add `GH_TOKEN` & `NPM_TOKEN` to CI

For example, in Travis CI's "Settings" tab for your project, you'll see:
> ![tokens](tokens.png)

For your `GH_TOKEN` [create one in Github](https://github.com/settings/tokens)
with `repo` credentials.

You can find `NPM_TOKEN` in your `~/.npmrc` file:

```
//registry.npmjs.org/:_authToken=${NPM_TOKEN}
```

Once these are in place, your new versions can be pushed back to Github & NPM
without permissions & security issues.

### 3. Create labels

From your repo's `Issues > Labels` section, add three labels representing the major, minor, and patch level changes in your repo. Ex:

- "Version: Major"
- "Version: Minor"
- "Version: Patch"

Add your label definitions to either `package.json` or to a `gsv.json` file in the base directory of your project.

##### package.json example
```json
"gsv": {
  "major-label": "Version: Major",
  "minor-label": "Version: Minor",
  "patch-label": "Version: Patch"
}
```

##### gsv.json example
```json
{
  "major-label": "Version: Major",
  "minor-label": "Version: Minor",
  "patch-label": "Version: Patch"
}
```

### 4. Add labels to issues

Add one of your _custom defined labels_ to your open PRs:

As these get merged, `github-semantic-version` will use this to determine
how to bump the current version. _PRs merged without a label will be treated
as `patch` releases._

_If any un-tagged commits are pushed to `master` outside of a PR, they're
automatically treated as `patch` releases._

### 5. Update `.travis.yml`

```yaml
sudo: false
language: node_js
cache:
  directories:
    - node_modules
notifications:
  email: false
branches:
  except:
    - /^v[0-9]/
deploy:
  provider: script
  script: npm run release
  skip_cleanup: true
  on:
    branch: master
```

### 6. Usage

As automation related to your code and publishing to the world can sometimes be scary, `github-semantic-version` operates with an _additive functionality_ philosophy.

#### Options

`github-semantic-version`

Displays the usage information.

`github-semantic-version --bump`

Update the package version, no CHANGELOG, don't push to Github, don't publish to NPM.

_Meant to be used in a CI environment._

`github-semantic-version --init`

Generates a fresh CHANGELOG based on labeled PRs and any commits to `master` outside
of any PR. Also calculates the package version based on those PRs and commits. Won't push to Github or publish to NPM.

_Meant to be used outside of a CI environment to generate the initial CHANGELOG._

`github-semantic-version --changelog`

Append the latest change to an
existing CHANGELOG (must have already been generated by the `--init` flag above).

You'll want to run `github-semantic-version --init` outside of CI to generate the initial CHANGELOG
before enabling the `--changelog` flag.

`github-semantic-version --bump --changelog`

Bump the version and append the latest change to the CHANGELOG.

`github-semantic-version --bump --changelog --push`

Bump the version, append the latest change to the CHANGELOG, and push the changes to Github.

`github-semantic-version --bump --changelog --push --publish`

Bump the version, append the latest change to the CHANGELOG, push to Github, and publish to NPM.

#### Other flags

`--force`

The flags `--bump` and `--changelog` are meant to be used in a CI environment. _Override this if you know what you're doing._

`--dry-run`

Append this to see output of what _would_ happen without any writing to files, pushing to Github, or publishing to NPM.

#### Debug

Prepend `DEBUG=github-semantic-version:*` to the `github-semantic-version` command to show all debug output when running.

### 7. Update `package.json`

```json
{
  "scripts": {
    ...
    "prerelease": "npm run build",
    "release": "github-semantic-version --bump --changelog --push --publish",
    ...
  }
}
```

#### startVersion

You can add a `startVersion` to the `gsv` section of `package.json` (or to `gsv.json`) that will be used as the starting point to
calculate the package version of a repo. Ex:

```json
{
  ...
  "gsv": {
    "startVersion": "2.5.0",
    "major-label": "Version: Major",
    ...
  },
  ...
}
```

If you're working on a private project, you can leave out `--publish`, which
means you have no need for your `NPM_TOKEN` either.


### License

> MIT License 2016 © Eric Clemmons
