page_main_title: Yml structure
main_section: CI
sub_section: Configuration
page_title: Anatomy of shippable.yml
page_description: The structure of a basic shippable.yml

# Configure your build

All configuration for CI happens through shippable.yml which should be present at the root of the repository you want to build using Shippable. The following sections describe the overall structure of the shippable.yml file, as well as detailed descriptions of every section in it.

Advanced configurations are addressed in the **CI->Advanced Options** section in the left menu.

##Anatomy of shippable.yml

The structure of a basic shippable.yml is shown below. The sections below explore each section of the yml in greater detail.

```
language:

node_js:      # language runtime
  - #language version

# Optional: select the node pool or node pools you want to run this job on,if different from default
runtime:

services:
  - #any supported service

depth: #postive integer

gitConfig:
  - #git config 1
  - #git config 2

vote:
  on_success:
    Verified: 1
    Code-Review: 2
  on_failure:
    Verified: -1
    Code-Review: -2

env:
  - #env1=foo
  - #env2=bar

matrix:

build:

  pre_ci:

  pre_ci_boot:
    image_name:
    image_tag:
    pull:
    options:
  ci:
    - #command1
    - #command2
  post_ci:
    - #command1
    - #command2
  on_success:
    - #command1
    - #command2
  on_failure:
    - #command1
    - #command2
  always:
    - #command1
    - #command2
  cache:
  cache_dir_list:
    - #dir1
  push:
    - #command1

integrations:
 notifications:
   - integrationName:
     type:
     recipients:
       - #recp1
       - #recp2

  hub:
    - integrationName:
      type:
      agent_only:
```

A brief overview of each section of the yml is provided in this table. For a detailed explanation of each tag, you can scroll to the specific section of this page.


| **yml tag**           |** default behavior without tag**                                                                         | **Description of usage**                                                                                                                                                                                                                                                                                                                                                |
|---------------------|------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [**language:**](set-language/)           | language gets set to ruby                                                                            | Set to the language your project is written in. e.g. node_js. [Read more](set-language/)                                                                                                                                                                                                                                                                                                        |
| [**runtime:**](runtime-config/)            | depends on the default nodePool specified in subscription nodePools section | Set to the nodePool(s) you want to run the build against. [Read more](runtime-config/) |
| [**depth:**](shallow-clone/) | no default | Set to a positive integer for the repository to be shallow cloned. [Read more](shallow-clone/) |
| [**gitConfig:**](set-git-config/) | no default | Set to the list of git configs that are to be set globally before the repo is cloned. [Read more](set-git-config/) |
| [**vote:**](set-vote/) | default `Verified` label set | Set the labels and values to be set for Gerrit projects once the build completes. [Read more](set-vote/) |
| [**services:**](services-overview/)           | no services are available                                                                            | Specify the services you need for your CI workflow, e.g. postgres, mysql, etc. [Read more](services-overview/)                                                                                                                                                                                                                                                                                     |
| [**env:**](env-vars/)                | Only standard environment variables are available during your CI workflow                            | Set custom environment variables for your builds. , including  `secure` variables, i.e. encrypted variables  used to store sensitive information. [Read more](env-vars/)                                                                                                                                                                                                                    |
| [**matrix:**](matrix-builds/)             | no default                                                                                           | Used to include or exclude only specific combination(s) from a build matrix. This is only relevant  if you  are triggering matrix builds, i.e. running several builds per trigger.  [Read more](matrix-builds/)                                                                                                                                                                                |
| [**build:**](build-and-test/)              | no default                                                                                           | Wrapper for several sub-sections like `pre_ci`, `ci`, `post_ci`, `on_success`, `on_failure`, `cache`, and `push`. [Read more](build-and-test/)                                                                                                                                                                                                                                                  |
|&nbsp;&nbsp;&nbsp;&nbsp;[pre_ci:](build-image/)         | no default                                                                                           | Used primarily if you need to use a custom image for your build or if you need to customize behavior of the  default CI container. You can build your image from a Dockerfile or pull an image from a Docker registry  in this section. **Commands in this section run outside the CI container**, so you should not include any commands required  for your CI workflow here. [Read more](build-image/)     |
|&nbsp;&nbsp;&nbsp;&nbsp;[pre_ci_boot:](build-image/)    | no default                                                                                           | Used to override the default image used for CI with your own custom image. You can also set specific options  for booting up the default CI container. [Read more](build-image/)                                                                                                                                                                                                             |
|&nbsp;&nbsp;&nbsp;&nbsp;[ci:](build-and-test/)             | depends on language                                                                                  | Include all commands for your CI workflow. Commands in this section are run inside your CI container,  so any dependencies you need for your build should be installed as the first set of commands in this section.  If this section is missing or empty, we call some default commands based on language, e.g. `npm install`  and `npm test` for Node.js projects. [Read more](build-and-test/)   |
|&nbsp;&nbsp;&nbsp;&nbsp;[post_ci:](build-and-test/)        | no default                                                                                           | Include commands that are not really a part of your core CI workflow but should be run after CI finishes. Commands in this section are run inside your CI container. [Read more](build-and-test/)                                                                                                                                                                                                  |
|&nbsp;&nbsp;&nbsp;&nbsp;[on_success:](build-and-test/)      | no default                                                                                           | Include commands you want to execute only if your CI workflow passes, i.e. the ci section exits with 0.  Commands in this section are run inside your CI container. [Read more](build-and-test/)                                                                                                                                                                                                  |
|&nbsp;&nbsp;&nbsp;&nbsp;[on_failure:](build-and-test/)      | no default                                                                                           | Include commands you want to execute only if your CI workflow fails, i.e. the ci section does not exit with 0.  Commands in this section are run inside your CI container. [Read more](build-and-test/)                                                                                                                                                                                           |
|&nbsp;&nbsp;&nbsp;&nbsp;[always:](build-and-test/)      | no default                                                                                           | Include commands you want to execute irrespective of CI workflow status. Commands in this section are run inside your CI container. [Read more](build-and-test/)                                                                                                                                                                                           |
|&nbsp;&nbsp;&nbsp;&nbsp;[push:](#push)           | no default                                                                                           | Used to push Docker image to an image registry, especially if you are pushing to Google Container Registry  or Amazon ECR. **Commands in this section run outside your CI container.**                                                                                                                                                                                                                                           |
|&nbsp;&nbsp;&nbsp;&nbsp;[cache:](caching/)          | nothing is cached                                                                                    | Used to turn on caching. If set to true, the build directory SHIPPABLE_BUILD_DIR is cached. To cache specific folders, you can use the tag cache_dir_list. [Read more](/ci/caching/)                                                                                                                                                                                                           |
|&nbsp;&nbsp;&nbsp;&nbsp;[cache_dir_list:](caching/) | if cache is set to true, default is SHIPPABLE_BUILD_DIR                                              | Used to specify a list of folders that you want to cache between builds. [Read more](caching/)                                                                                                                                                                                                                                                                                           |
| [**integrations:**](/platform/integration/overview/)       | no default                                                                                           | Wrapper for several subsections like `notifications`, `hub`,  and `keys`. This overall section lets you specify what third party services you want to interact with a part of your build.                                                                                                                                                                           |
|&nbsp;&nbsp;&nbsp;&nbsp;[notifications:](send-notifications/)     | Email notifications sent to last  committer and author on build  failure or status change to success | Used to send Slack, Hipchat, IRC notifications as well as to customize default email notification settings. This section can also be used to trigger a custom webhook or to trigger another Shippable project at various points during your CI workflow. [Read more](send-notifications/)                                                                                                           |
|&nbsp;&nbsp;&nbsp;&nbsp;[hub:](push-artifacts/)            | no default                                                                                           | Include this section if you want to interact with any Docker registry to pull a private image or push an image. [Read more](push-artifacts/)                                                                                                                                                                                                                                                    |
|&nbsp;&nbsp;&nbsp;&nbsp;[keys:](#keys)           | no default                                                                                           | Include this section if you need to use SSH or PEM keys to interact with services that are not natively supported on Shippable. [Read more](/ci/ssh-keys/)                                                                                                                                                                       |

## YML templates
If some common scripts need to be used in multiple sections of your CI job then instead of writing them in all the sections repetitively you can define a template once and use this at all the places and keep your **shippable.yml** file clean and small. These templates are basically yml anchors, to know more about yml anchors and how to use them please click [here](http://yaml.org/spec/1.2/spec.html#id2765878).

Below is a sample yml using templates defined in the shippable.yml.

```
templates: &template-script
  - echo "common-script 1"
  - echo "common-script 2"
  - echo "common-script 3"

language: node_js
build:
  pre_ci:
    - echo "hello world!"
    - *template-script
  ci:
    - echo "hello world!"
    - *template-script
  post_ci:
    - echo "hello world!"
    - *template-script
  on_success:
    - echo "hello world!"
    - *template-script
```

## shippable.templates.yml
Defining separate templates for each of the files might be an overhead if you want to use some common templates in more than one file. You can avoid this by creating a separate `shippable.templates.yml` in the root directory of your project and define all your templates in this file and use them in your yml. This helps significantly in reducing the number of lines in your yml and also makes your ymls more neat and clean.

Below is a sample yml using templates defined in a separate file.

* `shippable.templates.yml`

```yaml

templates1: &template-script-1
  - echo "common-script 1"
  - echo "common-script 2"

templates2: &template-script-2
  - echo "common-script 3"
  - echo "common-script 4"

templates3: &template-script-3
  - echo "common-script 5"
  - echo "common-script 6"

```

* `shippable.yml`

```yaml
language: node_js
build:
  pre_ci:
    - echo "hello world!"
    - *template-script-1
  ci:
    - echo "hello world!"
    - *template-script-2
  post_ci:
    - echo "hello world!"
    - *template-script-3
  on_success:
    - echo "hello world!"
    - *template-script-1
```

There might be some cases where you need to use some common templates in more than one projects. In this case, instead of defining these templates in each of the projects you can rather keep them in some **public** repo and then mention their raw file path in `shippable.templates.yml` under `externalReferences` section, and then use templates defined in this file in your jobs.

Below is an example showing how you can import external templates files and use the templates defined in those files in your jobs.

* external template file `template1.yml`

```yaml

templates1: &template-script-1
  - echo "common-script 1"
  - echo "common-script 2"

templates2: &template-script-2
  - echo "common-script 3"
  - echo "common-script 4"

```

* external templates file `template2.yml`

```yaml

templates3: &template-script-3
  - echo "common-script 5"
  - echo "common-script 6"


templates4: &template-script-4
  - echo "common-script 7"
  - echo "common-script 8"

```

* `shippable.templates.yml`

```yaml

externalReferences:
  - https://github.com/sampleTest/sample_templates/raw/master/template1.yml
  - https://github.com/sampleTest/sample_templates/raw/master/template2.yml

templates5: &template-script-5
  - echo "common-script 1"
  - echo "common-script 2"

templates6: &template-script-6
  - echo "common-script 3"
  - echo "common-script 4"

```

* `shippable.yml`

```yaml
language: node_js
build:
  pre_ci:
    - echo "hello world!"
    - *template-script-1
  ci:
    - echo "hello world!"
    - *template-script-2
  post_ci:
    - echo "hello world!"
    - *template-script-3
    - *template-script-4
  on_success:
    - echo "hello world!"
    - *template-script-5
    - *template-script-6

```


**Note**:

* YML templates should be given in a single line script only. Passing template in a multi-line script wont work.

```
  ci:
    - |
      echo "This is job A"
      *template-script          # this is not allowed
      echo "some more scripts"
```

* Currently we support `externalReferences` only from a public repo, so please make sure your external templates file are publically accessible.
