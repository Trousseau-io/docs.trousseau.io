## Welcome! 

We are glad that you want to contribute to our projects and we want to make it as easy and transparent as possible! If you aren't sure what to expect, here are some guidelines so you feel more comfortable with how things will go.

If this is your first contribution, we encourage you to walk through this guideline helping you to:

- setup your dev environment 
- make a first change and test it
- open a pull request about it 

--- 
## Code of Conduct
We want to ensure the community has a safe space to contribute reason why every contributors will follow the [CNFC Code of Conduct](https://github.com/cncf/foundation/blob/master/code-of-conduct.md). This includes and not limited to: any projects (directly or indirectly), GitHub repositories, discussions, interaction on social media, conferences and meetups. 

--- 
## License
By contributing, you agree that your contributions will be licensed under its [Apache License 2.0](https://github.com/Trousseau-io/trousseau/blob/main/LICENSE).   
In short, when you submit code changes, your submissions are understood to be under the same [Apache License 2.0](https://github.com/Trousseau-io/trousseau/blob/main/LICENSE) that covers the project.   

**Feel free to contact the maintainers if that's a concern.**

--- 
## Ways to contribute

Every projects follows the same template inspired by the CNCF recommendations which standardize the contributing experience. 

We welcome many different types of contributions includings:

* Answering questions on ```GitHub Discussions```
* Documentation
* Social Media, blog post, webinar 
* Issue triage, new feature, bug fix
* Build, CI/CD, QA help, release management
* Review Pull Request
* Web, logo, diagrams design

Not everything happens through a GitHub pull request. Do not hesitate to start a GitHub Discussions within the relevant project about any of the above topics or anything other relevant to the project. 

### Defect and Support Issues
In this category, we assume that you run into a challenge with trying to run or with a running version of our project.   

!!! warning "Security defect/issue"
    We take security and users' trust seriously. If you believe you have found a security issue in one of our project, please responsibly disclose by following the security policy available within the project GitHub repository.

To ensure a proper triage and handling, the following template can be used as a starting point:

``` title="Issue template is used for reporting defects or support issues."

<!--- Provide a general summary of the issue in the Title above -->

## Detailed Description
<!--- Provide a detailed description of the defect or support issue -->

## Expected Behavior
<!--- Tell us what should happen -->

## Current Behavior
<!--- Tell us what happens instead of the expected behavior -->

## Steps to Reproduce
<!--- Provide a link to a live example, or an unambiguous set of steps to -->
<!--- reproduce this bug. Include code to reproduce, if relevant -->
1.
2.
3.
4.

## Context (Environment)
<!--- How has this issue affected you? What are you trying to accomplish? -->
<!--- Providing context helps us come up with a solution that is most useful in the real world -->

## Possible Solution/Implementation
<!--- Not obligatory, but suggest an idea, a fix/reason for the bug, a documentation update -->

## Possible PR (use the "#" to tag the relevant entry)
<!--- #42 -->
```

### Contribut to an Issue
We use GitHub to host code, track issues and feature requests, as well as accept pull requests. As a start, we have labelled ```good first issue``` for new contributors and ```help wanted``` issues suitable for any contributor who isn't a core maintainer. 

Sometimes there won't be any issues with these labels. That's ok! There is likely something for you to work on. If you want to contribute but you don't know where to start or can't find a suitable issue, you can reach out via the ```GitHub Discussions``` for guidance. 

Once you see an issue that you would like to work on, please post a comment saying that you want to work on it. Something as simple as "I want to work/help on this" is fine :)

### Ask for Help
The best way to reach us with a question when contributing is to ask on ```GitHub Discussions``` or join us on ```Slack``` for a longer chat. 

### Which branch to use 
For any issues, your branch should be against the ```main``` branch except if explicit guidance within the ```GitHub Issues``` description. 

### Signing your commits
Licensing is important to open source projects. It provides some assurances that
the software will continue to be available based under the terms that the
author(s) desired. We require that contributors sign off on commits submitted to
our project's repositories. The [Developer Certificate of Origin
(DCO)](https://developercertificate.org/) is a way to certify that you wrote and
have the right to contribute the code you are submitting to the project.

You sign-off by adding the following to your commit messages. Your sign-off must
match the git user and email associated with the commit.

    This is my commit message

    Signed-off-by: Your Name <your.name@example.com>

Git has a `-s` command line option to do this automatically:

    git commit -s -m 'This is my commit message'

If you forgot to do this and have not yet pushed your changes to the remote
repository, you can amend your commit with the sign-off by running 

    git commit --amend -s 

--- 
## Coding, Testing, Repeating

### Local Development 
Each projects has a file called ```localdev.md``` detailing how to setup your development environment in order to run the latest version of the code and subsequently a version including your own contribution. 

### Error handling

The project is using standard Go error handling with the following rules:

 * Each error has to be wrapped with meaningful context: `fmt.Errorf("...:%w", err)`
 * Errors without any validation in the code base should be in-line: `errors.New()` or `fmt.Errorf()`
 * Errors with validation must be package private structs with constructor in a separated file:
   ```
   type customError struct { error }
   func (e *customError) Error() string { return fmt.Sprintf("custom error %s", e.error) }
   func newCustomError(err error) error { return &customError{error: err} }
   ```
   `_, ok := err.(*customError)`
 * Errors require validation outside of it's package have to publish validation function(s):
   ```
   type customErrorType interface { customErrorType() }

   type customError struct {
      error
      customErrorType //lint:ignore U1000 type check
   }

   func (e *customError) Error() string {
      return fmt.Sprintf("custom error %s", e.error)
   }

   func newCustomError(err error) error {
      return &customError{error: err}
   }

   func IsCustomError(err error) bool {
      return errors.As(err, new(customErrorType))
   }
   ```
   `package.IsCustomError(err)`

### Logging standards
The project is using `klog` on top of `zap` logging. The log engine is configured via command line arguments:

 * `--v` verbosity of info logs, default value is 3, allowed values are 0-5.
 * `--zap-encoder` log message encoder, default value is `console`, allowed values are `console`, `json`.

Logging rules:

 * Fatal: core component stopped to work, better to terminate the application
 * Error: application doesn't work properly, operator has to wake up and fix the problem
 * Info: must to know information, included recoverable errors, this level ignores `--v` settings
 * V(1-3).Info: good to know information
 * V(4-5).Info: debug logs, do not use in production, may contains sensitive information

--- 
## Pull Request
For none related code changes like like documentation improvement, misspellings and such, you can submit a PR directly including a clear descriptions. 

For any code changes, all pull request have to be linked with a ```GitHub Issue```. If not, please make an issue first, explain the problem or motivation for the code change you are proposing. When the solution isn't solution is not straightforward, make sure to outline the expected outcome with examples. Your PR will go smoother if the solution is agreed upon via the ```GitHub Issue``` before you have spent a lot of time implementing it. 

### Pull Request Lifecycle
When you submit your pull request, or you push new commits to it, our ```GitHub Actions``` will run some checks on your new code. We require that your pull request passes these checks, but we also have more criteria than just that before we can accept and merge it. 

0. The pull request template is mandatory and part of the workflow along with the ```GitHub   Actions```. While we are more on the documentation changes, **all unsigned code change submitted without a PR template will be either stalled or rejected.** 

1. You create a draft or WIP pull request. Reviewers will ignore it mostly unless you mention someone and ask for help. Feel free to open one and use the pull request to see if the CI passes. Once you are ready for a review, remove the WIP and leave a comment that it's ready for review. 

1. A reviewer will assign themselves to the pull request. If you don't see anyone assigned after 3 business days, you can leave
   a comment asking for a review. Sometimes we have busy, sick, vacation days, so a little patience is appreciated ;)

1. The reviewer will leave feedback. 
   * Suggestions might be shared that you may decide to incorporate into your pull request or not without further comments. 
   * It can help to use a 👍 or a task list/checkbox to track if you have implemented any of the suggestions.
   * It is okay to clarify if you are being told to make a change or if it is a suggestion.

1. After you have made the changes (in new commits please!), leave a comment or check the task list. If 3 business days go by 
   with no review, it is ok to bump. 
   
1. If your commits does not content all the suggestions, please open an issue with the title ```Follow-On PR #00```. This will 
   allow to pursue or not these suggestions if needed.

1. If your pull request will require a ```Documentation``` update, make sure to provide a 
   a extract of what needs to be changed with the proposed changed. Best would be to clone the wiki and perform a parallel pull 
   request.

1. When a pull request has been approaved, the reviewer will squash and merge your commits. If you prefer to rebase your own 
   commits, at any time leave a comment on the pull request to let them know that. 
   
At this point your changes are available in the ```main``` release of Trousseau! At this stage, the changes are not yet push tagged container image and user will need to build from sources to benefit of your latest and greatest contribution. 
The Trousseau maintainers might invite you to be part of the Contributors team - it's up to you to accept ;)

### Pull Request Template
``` title="PR template"
## Description

Please include: 
- [ ] a summary of the change and which issue is fixed
- [ ] relevant motivation and context
- [ ] any dependencies that are required for this change

Fixes # (issue)

## Type of change

Please check options that are relevant.

- [ ] Bug fix (non-breaking change which fixes an issue)
- [ ] New feature (non-breaking change which adds functionality)
- [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
- [ ] This change requires a documentation update

# How Has This Been Tested?

Please describe: 
- the tests that you ran to verify your changes 
- the instructions so we can reproduce 
- the list any relevant details for your test configuration

**Test Configuration**:
* Kubernetes distribution
* Kubernetes version:
* How many control plane node:
* KMS: 
* KMS version: 

# Checklist:

- [ ] My code follows the style guidelines of this project
- [ ] I have performed a self-review of my own code
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes
- [ ] Any dependent changes have been merged and published in downstream modules
```
