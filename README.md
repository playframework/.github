# Playframework GitHub integration

This repository contains a few configurations of GitHub features. For example a [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) or 
[Starter workflows](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization) as [GitHub Actions](https://docs.github.com/en/actions) features.  

## GitHub Actions Reusing Workflows

* [Single SBT task](#single-sbt-task)
* [Java/Scala matrix SBT task](#javascala-matrix-sbt-task)
* [Publishing to Sonatype](#publishing-to-sonatype)
* [Validate Binary Compatibility](#validate-binary-compatibility)

### Single SBT task

This workflow is used for running a single SBT task. It can to use for running an any BASH script or command, but we don't recommend doing that.

**Path**: [`.github/workflows/sbt.yml`](.github/workflows/sbt.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)

**Uses actions**: 
* [Coursier/Setup Action](https://github.com/coursier/setup-action)
* [Coursier/Cache Action](https://github.com/coursier/cache-action)

**Parameters**:

| Parameter  | Since | Required           | Default | Description          | 
|------------|-------|--------------------|---------|----------------------|
| cmd        | 1.0.0 | :exclamation:      | -       | Running command      |
| java       | 1.0.0 | :heavy_minus_sign: | 8       | _AdoptJDK_ version   |
| scala      | 1.0.0 | :heavy_minus_sign: | ''      | _Scala_ version      |
| cache-key  | 1.0.0 | :heavy_minus_sign: | ''      | Key of custom cache  |
| cache-path | 1.0.0 | :heavy_minus_sign: | ''      | Path of custom cache |
| env        | 1.0.0 | :heavy_minus_sign: | ''      | Custom ENV vars      |

**How to use**:

```yaml
uses: playframework/.github/.github/workflows/sbt.yml@v1
with:
  cmd: sbt "-Dvar1=$VAR_1" "-Dvar2=$VAR_2" ++$SCALA_VERSION test
  env: |
    VAR_1=value
    VAR_2=value
```

### Java/Scala matrix SBT task

This workflow is used for running an SBT task on matrix of Java/Scala versions.

**Path**: [`.github/workflows/sbt-matrix.yml`](.github/workflows/sbt-matrix.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)

**Uses actions**:
* [Coursier/Setup Action](https://github.com/coursier/setup-action)
* [Coursier/Cache Action](https://github.com/coursier/cache-action)

**Parameters**:

| Parameter  | Since | Required           | Default | Description          | 
|------------|-------|--------------------|---------|----------------------|
| cmd        | 1.0.0 | :exclamation:      | -       | Running command      |
| java       | 1.0.0 | :exclamation:      | -       | _AdoptJDK_ version   |
| scala      | 1.0.0 | :exclamation:      | -       | _Scala_ version      |
| include    | 1.0.0 | :heavy_minus_sign: | []      | Matrix include's     |
| exclude    | 1.0.0 | :heavy_minus_sign: | []      | Matrix exclude's     |
| cache-key  | 1.0.0 | :heavy_minus_sign: | ''      | Key of custom cache  |
| cache-path | 1.0.0 | :heavy_minus_sign: | ''      | Path of custom cache |
| env        | 1.0.0 | :heavy_minus_sign: | ''      | Custom ENV vars      |


**How to use**:

```yaml
uses: playframework/.github/.github/workflows/sbt-matrix.yml@v1
with:
  java: >-
    [ "11", "8" ]
  scala: >-
    [ "2.12.15", "2.13.8", "3.0.2" ]
  cmd: sbt "-Dvar1=$VAR_1" "-Dvar2=$VAR_2" ++$SCALA_VERSION test
  env: |
    VAR_1=value
    VAR_2=value
```

### Publishing to Sonatype

This workflow is used for publishing snapshots artifacts to [Sonatype Snapshots](https://oss.sonatype.org/content/repositories/snapshots/com/typesafe/play/) repository or release artifacts to [Maven Central](https://repo1.maven.org/maven2/com/typesafe/play/). 

:warning: For using this workflow project must uses the [CI Release](https://github.com/sbt/sbt-ci-release) plugin.

**Path**: [`.github/workflows/publish.yml`](.github/workflows/publish.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)

**Uses actions**:
* [Coursier/Setup Action](https://github.com/coursier/setup-action)
* [Coursier/Cache Action](https://github.com/coursier/cache-action)

**Parameters**:

| Parameter | Since | Required           | Default | Description        | 
|-----------|-------|--------------------|---------|--------------------|
| java      | 1.0.0 | :heavy_minus_sign: | 8       | _AdoptJDK_ version |

**Secrets**:

| Secret         | Since | Required        | Default | Description               | 
|----------------|-------|-----------------|---------|---------------------------|
| username       | 1.0.0 | :exclamation:   | -       | Sonatype account username |
| password       | 1.0.0 | :exclamation:   | -       | Sonatype account password |
| pgp_passphrase | 1.0.0 | :exclamation:   | -       | Password for GPG key      |
| pgp_secret     | 1.0.0 | :exclamation:   | -       | Base64 of GPG private key |

**How to use**:

```yaml
uses: playframework/.github/.github/workflows/publish.yml@v1
secrets:
  username: ${{ secrets.SONATYPE_USERNAME }}
  password: ${{ secrets.SONATYPE_PASSWORD }}
  pgp_passphrase: ${{ secrets.PGP_PASSPHRASE }}
  pgp_secret: ${{ secrets.PGP_SECRET }}
```

### Validate Binary Compatibility

This workflow is used for validate binary compatibility the current version.

:warning: For using this workflow project must uses the [SBT MiMa](https://github.com/lightbend/mima) plugin.

**Path**: [`.github/workflows/binary-check.yml`](.github/workflows/binary-check.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)

**Uses actions**:
* [Coursier/Setup Action](https://github.com/coursier/setup-action)
* [Coursier/Cache Action](https://github.com/coursier/cache-action)

**Parameters**:

| Parameter | Since | Required           | Default | Description        | 
|-----------|-------|--------------------|---------|--------------------|
| java      | 1.0.0 | :heavy_minus_sign: | 8       | _AdoptJDK_ version |

**How to use**:

```yaml
uses: playframework/.github/.github/workflows/binary-check.yml@v1
```

## GitHub Actions Starter workflows

TODO
