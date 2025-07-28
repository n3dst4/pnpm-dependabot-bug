# Minimal repro for dependabot/pnpm bug with github dependencies

[Dependabot issue](https://github.com/dependabot/dependabot-core/issues/10124)

[pnpm issue](https://github.com/pnpm/pnpm/issues/8343)

[PR on this repo that shows the issue](https://github.com/n3dst4/pnpm-dependabot-bug/pull/3)

## Short version

If your project uses pnpm and has a dependency on a public github project, like this:

```json5
{
  "dependencies": {
    "@lumphammer/investigator-fvtt-types": "github:n3dst4/investigator-fvtt-types#cards-support-types",
    // ...other deps...
}
```

It should be reflected in your `pnpm-lock.yaml` like this:

```
    dependencies:
      '@lumphammer/investigator-fvtt-types':
        specifier: github:n3dst4/investigator-fvtt-types#cards-support-types
        version: https://codeload.github.com/n3dst4/investigator-fvtt-types/tar.gz/60a416b50644449e7b9bd0629079c47fda46eb6e
```

However, when dependabot opens a PR on your repo, even if that GH dependency is not being updated, it changes the `pnpm-lock.yaml` like this:

```yaml
    dependencies:
      '@lumphammer/investigator-fvtt-types':
        specifier: github:n3dst4/investigator-fvtt-types#cards-support-types
        version: git+https://git@github.com:n3dst4/investigator-fvtt-types.git#60a416b50644449e7b9bd0629079c47fda46eb6e
```

In other words, the `https://codeload.github.com/` URL is being changed to a `git+https://git@github.com:` URL. This format requires an SSH key, which is almost certainly not present in CI.


## Other notes

This only applies to dependencies on PUBLIC GitHub repos (private repos would require an SSH key anyway.)

If you switch to the dependabot branch locally you probably won't be able to reproduce
