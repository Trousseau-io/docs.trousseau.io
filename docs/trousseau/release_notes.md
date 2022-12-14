## KMS Server Support

The below table provides a compatibility/supportability matrix:

| Trousseau      | Kubernetes | AWS KMS              | Azure KMS            | HashiCorp Vault      |
|----------------|------------|----------------------|----------------------|----------------------|
| [v1.1.3](https://docs.trousseau.io/trousseau/release_notes/#fortune-cookie)         | 1.18+      | :material-close:     | :material-close:     | :material-check-all: |
| [v2.0.0-alpha.2](https://docs.trousseau.io/trousseau/release_notes/#v200-alpha-smells-like-a-teen-spirit-pre-release) | 1.22+      | :material-check-all: | :material-check-all: | :material-check-all: |

!!! tip 
    For the latest and greatest news about Trousseau's releases, please consult [GitHub Releases](https://github.com/ondat/trousseau/releases).

## **Trousseau v2** 

### v2.0.0-alpha "Smells like a Teen Spirit (Pre-release)"

!!! note "Release notes - v2.0.0-alpha.2"
    This is a first pre-release of Trousseau v2.
    The documentation is not yet rendered and using this alpha pre-release for any other purpose than development is not recommended. 


!!! info "What's Changed?"
    Trousseau v2 is a redesign of the enntire architecture bringing extra resiliency, extending Kubernetes KMS capabilities, and bringing multi-KMS support. 
    
    ``` 
    Here are the new features: 
    
    - Multi KMS support including Azure KMS by @mhmxs in #147
    
    Here are code library updates:

    - Bump github.com/stretchr/testify from 1.7.1 to 1.7.2 by @dependabot in #98
    - Bump go.uber.org/zap from 1.19.0 to 1.21.0 by @dependabot in #100
    - Change Dockerfile LABEL by @romdalf in #106
    - Update README.md by @romdalf in #108
    - Bump github.com/stretchr/testify from 1.7.2 to 1.7.4 by @dependabot in #115
    - Bump k8s.io/apiserver from 0.24.1 to 0.24.2 by @dependabot in #114
    - Update GitHub Issues templates by @romdalf in #122
    - Bump github.com/stretchr/testify from 1.7.4 to 1.8.0 by @dependabot in #126
    - Bump k8s.io/klog/v2 from 2.60.1 to 2.70.0 by @dependabot in #116
    - Update README.md by @romdalf in #140
    - Add Go report card to readme by @mhmxs in #141
    - Validate log level by @mhmxs in #142
    - v2 alpha to main by @romdalf in #145
    - Bump google.golang.org/grpc from 1.47.0 to 1.48.0 in /providers/vault by @dependabot in #156
    - Bump k8s.io/apiserver from 0.24.2 to 0.24.3 in /trousseau by @dependabot in #155
    - Update Trousseau image version in readme by @mhmxs in #157
    - Otherwise git hook fails if only untracked by @mhmxs in #170
    - Upgrade Husky and SKIP_GIT_PUSH_HOOK option by @mhmxs in #176
    ```

!!! note "Release notes - v2.0.0-alpha.1"
    This is a first pre-release of Trousseau v2.
    The documentation is not yet rendered and using this alpha pre-release for any other purpose than development is not recommended. 
    

!!! info "What's Changed"
    Trousseau v2 is a redesign of the entire architecture bringing extra resiliency, extending Kubernetes KMS capabilities, and bringing multi-KMS support.
    
    ```
    Here are the new features:

    - Multi KMS support included AWS KMS by @mhmxs in #112
    - Delete socket file before listen and terminate if socket is missing by @mhmxs in #127
    - Generate manifests for producation usage by @mhmxs in #130
    - Generate helm chart by @mhmxs in #131
    
    Here are code library updates:

    - Bump github.com/stretchr/testify from 1.7.1 to 1.7.2 by @dependabot in #98
    - Bump go.uber.org/zap from 1.19.0 to 1.21.0 by @dependabot in #100
    
    Here are CI/CD updates:

    - e2e test with the supported versions of Kubernetes by @mhmxs in #128
    - Split Taskfile to sections by @mhmxs in #129
    - Multi Kube version e2e and tasks polish #125
    - e2e test of AWS KMS #124
    - e2e tests with debug provider #123
    - Change Dockerfile LABEL by @romdalf in #106
    - Update README.md by @romdalf in #108
    ```

!!! example "How to use"
    Replace the image parameter within the DaemonSet with:
    ```
    ghcr.io/ondat/trousseau:trousseau-v2.0.0-alpha.1
    ghcr.io/ondat/trousseau:proxy-v2.0.0-alpha.1
    ghcr.io/ondat/trousseau:vault-v2.0.0-alpha.1
    ghcr.io/ondat/trousseau:awskms-v2.0.0-alpha.1
    ghcr.io/ondat/trousseau:debug-v2.0.0-alpha.1
    ```

## **Trousseau v1** 

### Fortune Cookie
!!! note "Release notes - v1.1.3"
    This minor version is a code cleaning and polishing release including:  
    Updating the logging system to provide richer insights during normal operations and when reporting failures  

    [Git Compare with previous version](https://github.com/ondat/trousseau/compare/v1.1.2...v1.1.3)


!!! info "What's Changed"
    ```
    Bump google.golang.org/grpc from 1.38.0 to 1.46.0 by @dependabot  
    Bump github.com/stretchr/testify from 1.7.0 to 1.7.1 by @dependabot
    Bump go.opentelemetry.io/otel from 1.0.0-RC1 to 1.6.3 by @dependabot 
    Update Dockerfile by @rovandep
    Redesign logging by @mhmxs   
    Introduce git push hook to validate source before push by @mhmxs 
    ```

!!! example "How to use"
    Replace the image parameter within the DaemonSet with either: 
    ```
    ghcr.io/ondat/trousseau:3b11f867b06150f311459ddc2e01101e9b7ed309
    ghcr.io/ondat/trousseau:v1.1.3
    ```

### Wafer Cookie
??? note "Release notes - v1.1.2"
    This minor version is a code cleaning and polishing release including:  
    - a fix for CVE-2021-38561  
    - an end-2-end workflow review 

    [Git Compare with previous version](https://github.com/ondat/trousseau/compare/v1.1.0...v1.1.2)

??? info "What's Changed"
    ```
    * bump google.golang.org/grpc from 1.38.0 to 1.45.0 #63 
    * Fix code scanning alert - CVE-2021-38561 Package: golang.org/x/text #64 
    * bumping golang.org/x/text from 0.3.6 to 0.3.7 by @rovandep in #65 
    * introduction of end-2-end worfklow by @rovandep in #66 
    * e2e workflow update by @rovandep in #67 
    * Update dependabot.yml by @rovandep in #69 
    * Polish Go lint errors by @mhmxs in #82 
    ```

??? example "How to use"
    Replace the image parameter within the DaemonSet with either: 
    ```
    ghcr.io/ondat/trousseau:1ad8e11fbcd4f4c7f2587200dd8eca620fed4a5e
    ghcr.io/ondat/trousseau:v1.1.2
    ```

### Bad Batch Cookie 
??? danger "Release notes - v1.1.1"
    The release has been removed as part of a discovered CVE.   
    Code change has been addressed in v1.1.2 and container registry has been cleaned up.    

    **If you are still running v1.1.1 - consider upgrading as soon as possible to newer release**. 

    [Git Compare with previous version](https://github.com/ondat/trousseau/compare/v1.1.0...v1.1.1)

### Chocolate Chip Funny Cookie 
??? note "Release notes - v1.1.0"
    We are please to release the first update of version 1.0.0 adding chocolate chips on the original 'Funny Cookie'.   

    **Thanks to @mhmxs @hubvu @vfiftyfive and @cannischan for your contributions!**

    [Git Compare with previous version](https://github.com/ondat/trousseau/compare/v1.0.0...v1.1.0)

??? info "What's Changed"
    ```
    - Code polishing and optimisations #41 - Thanks to @mhmxs 
    - Module import update due to move to Ondat organization repository #44 
    - Automated creation of /opt/vault-kms on control planes #42 #44
    - Refactoring of the deployment methodology #31 #44 by:
    - removing the usage of configuration files for Trousseau 
    - authenticating with Vault using a ServiceAccount and Vault Kubernetes Auth
    - recovering the config parameters using ConfigMap and a HCL Vault configuration file
    - injection of the configuration within Trousseau's Pod
    - Introduction of UBI and Ubuntu container image based debug options #39 
    ```

??? example "How to use"
    Replace the image parameter within the DaemonSet with either: 
    ```
    ghcr.io/ondat/trousseau:1ad8e11fbcd4f4c7f2587200dd8eca620fed4a5e
    ghcr.io/ondat/trousseau:v1.1.2
    ```

### Funny Cookie
??? note "Release notes - v1.0.0"
    Funny Cookie' is out of the box!   
    We are please to release the very first version of Trousseau this December 1st 2021!  
    Trousseau is a Kubernetes KMS Provider plugin with support of Hashicorp Vault.   

    **The Trousseau Project would like to thanks @wwojcik and @kruc for their precious contributions!** 
 
??? info "The current release offers the followings"
    ```
    * respectful of the Kubernetes KMS Provider framework
    * provide support for Hashicorp Vault Community, Enterprise and Cloud Platform based deployment
    * deployment tested on Vanilla, RKE, and RKE2 Kubernetes clusters
    * build on distroless for small footprint 
    ```

??? warning
    The current release contains a security warning from gosec that will be address in point release:
    - https://github.com/Trousseau-io/trousseau/issues/15

??? example "How to use"
    The complementary asset is the container image accessible via the tag ```latest``` or ```v1.0.0```  
    Following the Deployment documentation.
