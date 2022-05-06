# Playframework GitHub integration

This repository contains a few configurations of GitHub features. For example a [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) or 
[Starter workflows](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization) as [GitHub Actions](https://docs.github.com/en/actions) features.  

## GitHub Actions Reusing Workflows

* [Universal CMD task](#universal-cmd-task)
* [Publishing to Sonatype](#publishing-to-sonatype)
* [Validate Binary Compatibility](#validate-binary-compatibility)
* [Mark Pull Request as Ready To Merge](#mark-pull-request-as-ready-to-merge)
* [Clear Cache](#clear-cache)

### Universal CMD task

This workflow is used for running any CMD task on matrix of Java versions and other dimensions.

Every matrix dimension will be access by environment variable like `MATRIX_$(uppercase(dimension_name))` (for example `MATRIX_JAVA`).


**Path**: [`.github/workflows/cmd.yml`](.github/workflows/cmd.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)

**Uses actions**:
* [Coursier/Setup Action](https://github.com/coursier/setup-action)
* [Coursier/Cache Action](https://github.com/coursier/cache-action)

**Parameters**:

| Parameter         | Since | Required           | Default | Description                                       | 
|-------------------|-------|--------------------|---------|---------------------------------------------------|
| ref               | 2.0.0 | :heavy_minus_sign: | ''      | Branch, tag or SHA for checkout                   |
| cmd               | 2.0.0 | :exclamation:      | -       | Running command                                   |
| java              | 2.0.0 | :heavy_minus_sign: | 8       | _AdoptJDK_ version (space/comma delimited list)   |
| scala             | 2.0.0 | :heavy_minus_sign: | ''      | _Scala_ version (space/comma delimited list)      |
| add-dimensions    | 2.0.0 | :heavy_minus_sign: | ''      | Other matrix dimensions (json object)             |
| include           | 2.0.0 | :heavy_minus_sign: | []      | Matrix include's (json object array)              |
| exclude           | 2.0.0 | :heavy_minus_sign: | []      | Matrix exclude's (json object array)              |
| cache-key         | 2.0.0 | :heavy_minus_sign: | ''      | Key of custom cache                               |
| cache-path        | 2.0.0 | :heavy_minus_sign: | ''      | Path of custom cache                              |
| env               | 2.0.0 | :heavy_minus_sign: | ''      | Custom ENV vars                                   |


**How to use**:

```yaml
uses: playframework/.github/.github/workflows/cmd.yml@v2
with:
  java: 11, 8
  scala: 2.12.15, 2.13.8, 3.0.2
  add-dimensions: >-
    {
      "color": [ "red", "green"]
    }
  cmd: sbt "-Dcustom_var=$CUSTOM_VAR" "-Dcolor=$MATRIX_COLOR" "-Djava=$MATRIX_JAVA" ++$MATRIX_SCALA test
  env: |
    CUSTOM_VAR=value
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

| Parameter | Since | Required           | Default | Description                        |
|-----------|-------|--------------------|---------|------------------------------------|
| ref       | 2.0.0 | :heavy_minus_sign: | ''      | Branch, tag or SHA for checkout    |
| java      | 1.0.0 | :heavy_minus_sign: | 8       | _AdoptJDK_ version                 |

**How to use**:

```yaml
uses: playframework/.github/.github/workflows/publish.yml@v2
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

| Parameter | Since | Required           | Default | Description                        | 
|-----------|-------|--------------------|---------|------------------------------------|
| ref       | 2.0.0 | :heavy_minus_sign: | ''      | Branch, tag or SHA for checkout    |
| java      | 1.0.0 | :heavy_minus_sign: | 8       | _AdoptJDK_ version                 |

**How to use**:

```yaml
uses: playframework/.github/.github/workflows/binary-check.yml@v2
```

### Mark Pull Request as Ready To Merge

This workflow is used for mark pull request as ready to merge and **should be last** in the workflows chain.

:warning: For using this workflow don't forget to configure the `needs` ([GA docs](https://docs.github.com/en/actions/using-workflows/advanced-workflow-features#creating-dependent-jobs)) attribute to make this workflow run last.

**Path**: [`.github/workflows/rtm.yml`](.github/workflows/rtm.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)


**No Parameters**

**How to use**:

```yaml
needs: # Should be latest
  - "check-code-style"
  - "..."
  - "tests"
uses: playframework/.github/.github/workflows/rtm.yml@v2
```

### Clear Cache

This workflow is used for clearing cache. Run this workflow manually from _Actions_ page.

**Path**: [`.github/workflows/trigger-cache-invalidation.yml`](.github/workflows/trigger-cache-invalidation.yml)

**Image**: [Ubuntu 20.04](https://hub.docker.com/layers/ubuntu/library/ubuntu/20.04/images/sha256-57df66b9fc9ce2947e434b4aa02dbe16f6685e20db0c170917d4a1962a5fe6a9?context=explore)

**Uses actions**:
* [Actions/Cache](https://github.com/actions/cache)

**No Parameters**

**How to use**:

```yaml
uses: playframework/.github/.github/workflows/trigger-cache-invalidation.yml@v2
```

## GitHub Actions Starter workflows

TODO
