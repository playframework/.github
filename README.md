# Playframework GitHub integration

[![Twitter Follow](https://img.shields.io/twitter/follow/playframework?label=follow&style=flat&logo=twitter&color=brightgreen)](https://twitter.com/playframework)
[![Discord](https://img.shields.io/discord/931647755942776882?logo=discord&logoColor=white)](https://discord.gg/g5s2vtZ4Fa)
[![GitHub Discussions](https://img.shields.io/github/discussions/playframework/playframework?&logo=github&color=brightgreen)](https://github.com/playframework/playframework/discussions)
[![StackOverflow](https://img.shields.io/static/v1?label=stackoverflow&logo=stackoverflow&logoColor=fe7a16&color=brightgreen&message=playframework)](https://stackoverflow.com/tags/playframework)
[![YouTube](https://img.shields.io/youtube/channel/views/UCRp6QDm5SDjbIuisUpxV9cg?label=watch&logo=youtube&style=flat&color=brightgreen&logoColor=ff0000)](https://www.youtube.com/channel/UCRp6QDm5SDjbIuisUpxV9cg)
[![Twitch Status](https://img.shields.io/twitch/status/playframework?logo=twitch&logoColor=white&color=brightgreen&label=live%20stream)](https://www.twitch.tv/playframework)
[![OpenCollective](https://img.shields.io/opencollective/all/playframework?label=financial%20contributors&logo=open-collective)](https://opencollective.com/playframework)

[![Repository size](https://img.shields.io/github/repo-size/playframework/.github.svg?logo=git)](https://github.com/playframework/.github)
[![Scala Steward badge](https://img.shields.io/badge/Scala_Steward-helping-blue.svg?style=flat&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAQCAMAAAARSr4IAAAAVFBMVEUAAACHjojlOy5NWlrKzcYRKjGFjIbp293YycuLa3pYY2LSqql4f3pCUFTgSjNodYRmcXUsPD/NTTbjRS+2jomhgnzNc223cGvZS0HaSD0XLjbaSjElhIr+AAAAAXRSTlMAQObYZgAAAHlJREFUCNdNyosOwyAIhWHAQS1Vt7a77/3fcxxdmv0xwmckutAR1nkm4ggbyEcg/wWmlGLDAA3oL50xi6fk5ffZ3E2E3QfZDCcCN2YtbEWZt+Drc6u6rlqv7Uk0LdKqqr5rk2UCRXOk0vmQKGfc94nOJyQjouF9H/wCc9gECEYfONoAAAAASUVORK5CYII=)](https://scala-steward.org)
[![Mergify Status](https://img.shields.io/endpoint.svg?url=https://api.mergify.com/v1/badges/playframework/.github&style=flat)](https://mergify.com)

This repository contains a few configurations of GitHub features. For example a [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) or 
[Starter workflows](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization) as [GitHub Actions](https://docs.github.com/en/actions) features.  

## GitHub Actions Reusing Workflows

* [Universal CMD task](#universal-cmd-task)
* [Publishing to Sonatype](#publishing-to-sonatype)
* [Validate Binary Compatibility](#validate-binary-compatibility)
* [Mark Pull Request as Ready To Merge](#mark-pull-request-as-ready-to-merge)

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

## GitHub Actions Starter workflows

TODO
