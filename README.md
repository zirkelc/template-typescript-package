# TypeScript Single Package Project Template

This template provides an opinionated setup for a single package TypeScript project.

## 🚀 Features

- [PNPM](https://pnpm.io/) for efficient package management
- [Oxlint](https://oxc.rs/docs/guide/linter/cli) and [Oxfmt](https://oxc.rs/docs/guide/formatter/cli) for linting and formatting
- [Vitest](https://vitest.dev/) for fast, modern testing
- [tsdown](https://github.com/rolldown/tsdown) for TypeScript building and bundling
- [tsx](https://tsx.is/) for running TypeScript files
- [Husky](https://github.com/typicode/husky) for Git hooks
- [lint-staged](https://github.com/lint-staged/lint-staged) for running linting on staged files
- [GitHub Actions](.github/workflows/ci.yml) for continuous integration
- [VSCode](.vscode/) debug configuration and editor settings
- [@total-typescript/tsconfig](https://github.com/total-typescript/tsconfig) for TypeScript configuration
- [Are The Types Wrong?](https://github.com/arethetypeswrong/arethetypeswrong.github.io) for type validation
- [publint](https://github.com/publint/publint) for package.json validation
- [EditorConfig](https://editorconfig.org/) for consistent coding styles

## 🚀 Getting Started

### 1. Create a new repository

Create a new repository [using this template](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template)

### 2. Replace placeholders

Replace all occurrences of the following placeholders in [`package.json`](package.json) with the correct values:

| Placeholder   | Field            | Description                             |
| ------------- | ---------------- | --------------------------------------- |
| `PACKAGE`     | `name`           | Your npm package name                   |
| `DESCRIPTION` | `description`    | Your package description                |
| `LICENSE`     | `license`        | Your license (`MIT`, `Apache-2.0`, ...) |
| `AUTHOR`      | `author`         | Your name                               |
| `USERNAME`    | `repository.url` | Your GitHub username                    |
| `REPO`        | `repository.url` | Your GitHub repository name             |

### 3. Apply ToDos

Find all occurrences of `TODO` and apply them:

| TODO            | File                                                        | Description                                                       |
| --------------- | ----------------------------------------------------------- | ----------------------------------------------------------------- |
| `TODO: PREVIEW` | `.github/workflows/ci.yml`                                  | Create [preview releases](#preview-releases)                      |
| `TODO: RELEASE` | `.github/workflows/ci.yml`, `.github/workflows/release.yml` | [Release with Release Please](#release-please) and publish to NPM |

### 4. Install, Build, Test

Verify your project is working by running `install`, `build`, and `test`:

```sh
pnpm install
pnpm build
pnpm test
```

Happy coding! 🎉

## 📋 Details

### Package

The [`package.json`](package.json) is configured as ESM (`"type": "module"`) and ships an ESM-only build. The `exports` map points consumers at `./dist/index.mjs` along with the matching type declarations, so the package can be consumed via `import` from any modern ESM project. Consumers stuck on CommonJS can still load it via dynamic `import()`.

The `exports` field does not need to be maintained by hand: tsdown's `exports: true` option (in [`tsdown.config.ts`](tsdown.config.ts)) regenerates `exports` (and `main` / `module` / `types`) on every build based on the actual entries it produced, so any new `src/**/index.ts` entry shows up in the published package automatically.

If you later need to support CommonJS consumers as well, switch to dual publishing by setting `format: ['esm', 'cjs']` in [`tsdown.config.ts`](tsdown.config.ts). tsdown will then emit both `dist/index.mjs` and `dist/index.cjs` (plus matching `.d.mts` and `.d.cts` declarations) and rewrite `exports` into the conditional `import` / `require` form. No manual `package.json` edits required.

### Oxlint & Oxfmt

[Oxlint](https://oxc.rs/docs/guide/linter/cli) and [Oxfmt](https://oxc.rs/docs/guide/formatter/cli) are Rust-based replacements for ESLint and Prettier with much faster execution. [`./.oxlintrc.json`](.oxlintrc.json) and [`./.oxfmtrc.json`](.oxfmtrc.json) contain the default configurations. They complement the formatter settings from the [`.editorconfig`](.editorconfig) file, which is read by both tools so indentation, line endings and final-newline behavior stay consistent.

### Vitest

An empty Vitest config is provided in [`vitest.config.ts`](vitest.config.ts) as a starting point. By default Vitest discovers any `*.test.ts` (or `*.spec.ts`) files in the project, so tests can live next to the code they cover (under `src/`) or in a dedicated [`test/`](test/) folder.

### Build and Run

- `tsdown` builds every `src/**/index.ts` entry into ESM (`dist/index.mjs`) along with type declarations (`dist/index.d.mts`). Build options, including the `attw` and `publint` integrations, live in [`tsdown.config.ts`](tsdown.config.ts).
- `tsx` compiles and runs TypeScript files on-the-fly, useful for ad-hoc scripts and local debugging without a build step.

### Git Hooks

[Husky](https://github.com/typicode/husky) runs the [.husky/pre-commit](.husky/pre-commit) hook before every commit, which delegates to lint-staged so only staged files are linted and formatted. This keeps commits fast even in larger repos and prevents broken style from landing on `main`.

### Continuous Integration

[`.github/workflows/ci.yml`](.github/workflows/ci.yml) defines a GitHub Actions workflow that runs on every push and pull request. It executes two jobs in parallel: `lint` (Oxlint and Oxfmt in check mode) and `test` (build followed by Vitest). Optional `preview` and `release` jobs in the same file are commented out and can be enabled via the `TODO` markers.

### VSCode Integration

#### Debugging

[`.vscode/launch.json`](.vscode/launch.json) provides VSCode launch configurations:

- `Debug (tsx)`: Run and debug TypeScript files
- `Test (vitest)`: Debug tests

It uses the [JavaScript Debug Terminal](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) to run and debug.

#### Editor Settings

[`.vscode/settings.json`](.vscode/settings.json) configures Oxfmt as the formatter and enables format-on-save.

### EditorConfig

[`.editorconfig`](.editorconfig) ensures consistent coding styles across different editors and IDEs:

- Uses spaces for indentation (2 spaces)
- Sets UTF-8 charset
- Ensures LF line endings
- Trims trailing whitespace (except in Markdown files)
- Inserts a final newline in files

This configuration complements Oxfmt and helps maintain a consistent code style throughout the project.

### Types Validation

The project includes the [`@arethetypeswrong/cli`](https://github.com/arethetypeswrong/arethetypeswrong.github.io) tool to validate TypeScript types in your package. It checks that the type declarations resolve correctly for ESM consumers and that the published types match the actual runtime exports, catching common publishing mistakes. It is wired into `tsdown` (with the `esm-only` profile in [`tsdown.config.ts`](tsdown.config.ts)) and runs automatically during the build.

### Publint

The project includes [`publint`](https://github.com/publint/publint) to validate your `package.json` against current packaging conventions, flagging issues like missing `exports` entries, mismatched `main`/`module`/`types` fields or files that would not actually be published. It is wired into `tsdown` and runs automatically during the build.

## Optional

### <a name="release-please"></a> Release with Release Please

[Release Please](https://github.com/googleapis/release-please-action) automates versioning, changelog generation and GitHub Releases based on [Conventional Commits](https://www.conventionalcommits.org/). The npm publish step runs in a separate workflow that uses [npm Trusted Publishing](https://docs.npmjs.com/trusted-publishers), so no `NPM_TOKEN` is required.

#### Flow

1. You merge conventional commits (`feat:`, `fix:`, ...) into `main`.
2. The `release` job in [`ci.yml`](.github/workflows/ci.yml) runs Release Please, which opens (or updates) a release PR. The PR contains the bumped version in `package.json`, an updated `CHANGELOG.md` and a bumped `.release-please-manifest.json`.
3. When you merge the release PR, Release Please creates a Git tag and a GitHub Release.
4. Publishing the GitHub Release triggers [`release.yml`](.github/workflows/release.yml), which builds the package and runs `pnpm publish --provenance` against npm using Trusted Publishing.

The two workflows are split so that release notes (handled by Release Please) and publishing (handled by npm with provenance) stay independent. The `release.yml` filename and workflow name must match what is registered with npm, so do not rename them.

The release behavior is configured in [`release-please-config.json`](release-please-config.json) and the current released version is tracked in [`.release-please-manifest.json`](.release-please-manifest.json).

To enable this, apply the `TODO: RELEASE` markers in both workflow files. You also need to perform the two setup steps below.

#### Setup: Release Please token

Release Please needs a token that can push commits and open pull requests. The default `GITHUB_TOKEN` works, but releases (and tags) created with it [do not trigger other workflows](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#using-the-github_token-in-a-workflow), which means `release.yml` would never run. To fix this, create a Personal Access Token and expose it as `RELEASE_PLEASE_TOKEN`.

1. Create a [fine-grained PAT](https://github.com/settings/personal-access-tokens/new) (or a classic PAT with the `repo` scope) with the following repository permissions:
   - `Contents`: Read and write (push commits, create tags and releases)
   - `Pull requests`: Read and write (open release PRs)
2. In your repository, open `Settings` → `Secrets and variables` → `Actions` → `New repository secret`.
3. Name it `RELEASE_PLEASE_TOKEN` and paste the PAT.

See the [release-please-action credentials docs](https://github.com/googleapis/release-please-action?tab=readme-ov-file#github-credentials) for more details.

#### Setup: npm Trusted Publishing

Trusted Publishing lets GitHub Actions publish to npm via OIDC, without long-lived tokens. The npm registry verifies the workflow identity (repo, workflow filename, environment) on each publish.

1. Publish the package once manually (`npm publish`) so it exists on npm. Your npm account needs publish rights on the package.
2. Open `https://www.npmjs.com/package/<your-package>/access`.
3. In the `Trusted Publisher` section, click `Add Trusted Publisher` and select GitHub Actions, then enter:
   - Organization or user: your GitHub user or org
   - Repository: your repository name
   - Workflow filename: `release.yml`
   - Environment: leave empty (unless you use one)
4. Save. From now on, the `release.yml` workflow can publish without an `NPM_TOKEN`. The `id-token: write` permission in the workflow is what enables OIDC.

See the [npm Trusted Publishers docs](https://docs.npmjs.com/trusted-publishers) for more details.

### <a name="preview-releases"></a> Preview Releases

[pkg.pr.new](https://github.com/stackblitz-labs/pkg.pr.new) publishes a throwaway version of the package for every push and pull request, with an installable URL like `npm i https://pkg.pr.new/<owner>/<repo>@<sha>`. This lets you (or a reviewer) try a change in a real consumer project before it gets merged or released, without polluting the npm registry with prerelease versions.

Setup:

1. Install the GitHub App: [pkg.pr.new](https://github.com/apps/pkg-pr-new).
2. Apply the `TODO: PREVIEW` marker in [`.github/workflows/ci.yml`](.github/workflows/ci.yml).
