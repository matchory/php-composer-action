Composer Action
===============
An action to run [composer](https://getcomposer.org/) commands in a GitHub Actions workflow.

## Usage

This action can be used to run any composer command. By default, it runs `composer install` with sensible configuration
out of the box:

```yaml
name: Composer Action Example
on: [ push ]

jobs:
  composer:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Composer Action
        uses: actions/composer-action@v1
```

You can also specify a different command to run by using the `args` input:

```yaml
name: Composer Action Example
on: [ push ]

jobs:
  composer:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Composer Action
        uses: actions/composer-action@v1
        with:
          args: outdated --direct --format=json
```

## Configuration

### Inputs

The action has some common inputs:

| Input               | Description                                                                                                                                                               | Required | Default   |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-----------|
| `args`              | Arguments to pass to composer. Accepts any valid Composer command args. If `install` (the default) or `update`, it will run the command with the configured option flags. | No       | `install` |
| `verbosity`         | Set the verbosity level. Must be one of: `normal`, `quiet`, `verbose`, `very`verbose", or `debug`.                                                                        | No       | `normal`  |
| `working-directory` | The working directory to run the command in.                                                                                                                              | No       | —         |
| `no-plugins`        | Whether to disable plugins.                                                                                                                                               | No       | `false`   |
| `no-scripts`        | Skips the execution of all scripts defined in `composer.json`.                                                                                                            | No       | `false`   |

It also provides additional convenience inputs that will only be applied with the `install` and `update` commands:

| Input                     | Description                                                                                                                                                                         | Required | Default         |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-----------------|
| `prefer`                  | Specify package source preference. Must be one of: `auto`, `dist`, or `source`.                                                                                                     | No       | `auto`          |
| `no-dev`                  | Disables installation of require-dev packages.                                                                                                                                      | No       | `false`         |
| `autoloader`              | Configures autoloader generation. Must be one of: `true`, `false`, `optimize`, `classma`",`or `apcu`.                                                                               | No       | `optimize`      |
| `audit`                   | Run an audit after installation is complete. Also takes an audit format name. Must be one of: `true` (equivalent to `summary`), `false`, `table`, or `plain`, `json`, or `summary`. | No       | `false`         |
| `ignore-platform-reqs`    | Ignore platform requirements. Must be one of: `true`, `false`, or a comma-separated list of platform requirements to ignore.                                                        | No       | `false`         |
| `cache`                   | Whether to use cache.                                                                                                                                                               | No       | `true`          |
| `artifact`                | Name of generated vendor artifact.                                                                                                                                                  | No       | —               |
| `artifact-path`           | Path of generated vendor artifact.                                                                                                                                                  | No       | `vendor.tar.gz` |
| `artifact-retention-days` | Number of days that the vendor artifact should be retained for.                                                                                                                     | No       | `7`             |

Some of the inputs are described in detail below.

### Outputs

| Output               | Description                            |
|----------------------|----------------------------------------|
| `artifact`           | Name of the generated vendor artifact. |
| `artifact-path`      | Path of generated vendor artifact.     |
| `composer-cache-dir` | Composer cache directory.              |

### Autoloader Generation

Autoloader generation is enabled by default. You can configure this by setting the `autoloader` input to one of the
following values:

- `true` - Generates the autoloader without optimizations (composer's default behavior).
- `false` - Disables autoloader generation (equivalent to passing `no-autoloader` to composer).
- `optimize` - Generates
  an [optimized](https://getcomposer.org/doc/articles/autoloader-optimization.md#optimization-level-1-class-map-generation)
  autoloader.
- `classmap` - Generates
  an [authoritative class map](https://getcomposer.org/doc/articles/autoloader-optimization.md#optimization-level-2-a-authoritative-class-maps)
  autoloader.
- `apcu` - Generates
  an [APCu](https://getcomposer.org/doc/articles/autoloader-optimization.md#optimization-level-3-apcu) autoloader.

Refer to the [Composer documentation](https://getcomposer.org/doc/articles/autoloader-optimization.md) for more details
on autoloader generation and optimization levels.

### Audit

The action can run an audit after installation is complete. This is enabled by setting the `audit` input to one of the
following values:

- `true` - Equivalent to `summary`.
- `false` - Disables audit.
- `table` - Displays audit results in a table format.
- `plain` - Displays audit results in a plain text format.
- `json` - Displays audit results in a JSON format.
- `summary` - Displays audit results in a summary format.

This combines the `--audit` and `--audit-format` options. The default value is `false`, which disables the audit.  
When enabled. The action will fail if the audit finds any issues.

### Ignoring Platform Requirements

The action can configure composer to ignore (some) platform requirements. This is enabled by setting the
`ignore-platform-reqs` input to one of the following values:
- `true` - Ignores all platform requirements.
- `false` - Does not ignore any platform requirements (composer's default behavior).
- `<comma-separated list>` - Ignores the specified platform requirements. This can be a comma-separated list of
  platform requirements to ignore, such as `ext-curl,ext-json`.

Refer to the [Composer documentation](https://getcomposer.org/doc/06-config.md#platform) for more

### Cache

The action can use a cache to speed up installation. This is enabled by setting the `cache` input to `true` (default)
or `false`. If enabled, the action will use the cache directory specified by the `COMPOSER_CACHE_DIR` environment
variable. If this variable is not set, the action will use the default cache directory for Composer, which is
`$HOME/.composer/cache` on Linux and macOS, and `%LOCALAPPDATA%/Composer/cache` on Windows.

### Artifact

The action can generate an artifact from the compressed vendor folder. This is enabled by setting the `artifact` input
to `true`. The archive will be generated in the working directory and will be named `vendor.tar.gz` by default.  
You can change the name of the artifact by setting the `artifact-path` input to a different path.
