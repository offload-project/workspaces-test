# workspaces-test

A throwaway monorepo for exercising [release-champion](https://github.com/offload-project/release-champion)'s
`npm-workspaces` publishing end-to-end.

## Layout

```
package.json            ← private root, declares "workspaces": ["packages/*"]
packages/
  hello/                ← @offload-project/hello
  world/                ← @offload-project/world
.github/workflows/
  release.yml           ← runs release-champion on merged PRs
```

Both packages get bumped to the same version and published together whenever a PR is merged.

## Before you run it

1. **Rename the packages.** Replace `@offload-project` in `packages/hello/package.json` and
   `packages/world/package.json` with a real npm scope you own (e.g. your npm username: `@yourname`). Scoped
   names are published with `--access public` automatically.
2. **Add an npm token.** Create an automation token on npmjs.com and add it as a repo secret named `NPM_TOKEN`
   (Settings → Secrets and variables → Actions). Or switch the workflow to Trusted Publishers (OIDC) instead.
3. **Push this to its own GitHub repo.** This is a separate project from the action — give it its own remote.

## Try it

1. Make a change on a branch with a conventional-commit message, e.g.:
   ```bash
   git commit -am "feat: add a greeting"
   ```
2. Open a PR and merge it. release-champion opens a **release PR** (Phase 1) with both packages bumped.
3. Merge the release PR. release-champion tags it and publishes **both** packages to npm (Phase 2).
4. Confirm on npmjs.com that `hello` and `world` both appear at the new version.

## Things to watch for

- The root `package.json` has `"private": true`, so it is bumped but never published — only the two packages are.
- Both packages always share one version, even if only one changed. That's "fixed/locked" versioning.
