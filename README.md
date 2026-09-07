# matiasjrossi/homebrew-tap

Homebrew casks for things that aren't in `homebrew-core` or `homebrew-cask`.

```sh
brew tap matiasjrossi/tap
```

## agent-vault

```sh
export HOMEBREW_GITHUB_API_TOKEN=<token with read access to matiasjrossi/agent-vault>
brew install --cask matiasjrossi/tap/agent-vault
```

The **tap is public but the release asset is not.** `Casks/agent-vault.rb` points at
the GitHub API asset endpoint and attaches `HOMEBREW_GITHUB_API_TOKEN` with a
`header:` on the url; no credential is stored in this repo. Homebrew sends that
header to `api.github.com` and then drops it on the cross-host redirect to
`release-assets.githubusercontent.com`, which is what makes this work — the
redirect target is already pre-signed.

**Without a token the download fails as a bare `404`**, because GitHub reports
private resources as Not Found rather than Unauthorized. That is the error to
recognise; it does not mean the cask is broken.

`brew` re-execs with only `HOMEBREW_*` variables plus a fixed allowlist, so a
plain `GITHUB_TOKEN` in the environment will **not** reach it. The variable has to
be `HOMEBREW_GITHUB_API_TOKEN`. Homebrew also accepts a `gh auth` login or a
macOS Keychain entry as fallbacks.

### The cask is generated, don't hand-edit it

`Casks/agent-vault.rb` is written by
[`agent-vault`'s release workflow](https://github.com/matiasjrossi/agent-vault)
on every tag, from `.github/scripts/render-cask.sh` in that repo. A private
release asset is addressable only by an opaque numeric asset ID, so the url
can't interpolate `version` and the file has to be regenerated per release.
Edits made here are overwritten by the next release.
