
## We are there! 
The power of open source is in building and contributing to the community. 

Please reach out for any questions, issues, ideas, or just want to chat via one the following channels: 

* GitHub for Defect, RFE, Discussions, PR
* Join us on [Slack](https://storageos.slack.com/) for chats and longer debate
* Follow us on Twitter for events and stuffy stuff

Each projects has its own space , check the [Project page](/) for relevant link to any of the above.

--- 

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

--- 
### Request for Enhancement (RFE)
New features are more than welcome. We would kindly ask to verify the ```Project``` tab within the relevant Project GitHub repository and the ```Issues``` to check on the roadmap and existing RFEs if a similar idea has been posted. In this case, do not hesitate to share your perspective about the existing RFE, open a ```GitHub Discussion``` or join us on ```Slack``` for a longer chat. 

To ensure a proper triage and handling, the following template can be used as a starting point:

``` title="RFE template is used to report new ideas and features"
<!--- Provide a general summary of the RFE in the Title above -->

Is it linked to a user story, issue, defect? (use the "#" to tag the relevant entry)
<!--- #42 -->

What do we want to build?
<!--- Provide a detailed description of your idea or feature -->

Why do we want to build it?
<!--- Provide a detailed description of what it solves -->

How do we want to design it?
<!--- Not obligatory, but do not hesitate to share a clear structured view of how to design, implement or even have a code change -->

```

--- 
### Propose a change (PR)
All are projects follows a specific workflow to help all contributors and maintainers with quality control using a structured approach for code changes.   

The Pull Request (PR) template is one piece of this workflow along with the GitHub Actions associated with. 

!!! warning "Pull Request (PR)" 
    While are more relax on documentation changes, **all unsigned code change submitted without a PR template will be either stalled or rejected.**

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

