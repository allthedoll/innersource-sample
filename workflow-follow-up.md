# Workflow consultation follow-up
Thank you for choosing GitHub, the flexible, secure platform for building better software faster. By taking the time to have a conversation with the GitHub Professional Services team, you’ve taken an important step towards a successful GitHub implementation in your organization.

The content and philosophy behind our workflow consultations is optimized for developer happiness. We care about developers, and our guidance in workflows should simplify, clarify, and enhance the developer experience. Having well defined, mature workflows reduces friction and uncertainty, making developers, managers, and stakeholders productive and happy; this results in making shipping easier and faster. Our methodologies are based on real-world use cases; they're not just idealized, but actually obtainable. There is no one size fits all solution, in fact, there are no solutions--only trade-offs. We strive to balance the needs of various business units, encouraging interdepartmental collaboration. Collaboration in key: people build better software *together*, and become better developers when teaming up to learn from each other.

It's important to remember that in addition to the technical challenges of changing workflow processes, a *cultural* shift also needs to take place. Humans are creatures of habit and it's always difficult to enact even positive changes, so it will be important to keep this in mind when implementing your new workflows with your teams. Try to keep changes as simple as possible and remember that these changes are unlikely to occur overnight.

## Workflows
Two main ways to manage software in Git are GitHub Flow and Git flow, which are broken down here. We recommend starting as simply as possible, with the GitHub Flow, and scaling up to the Git flow if you find the need for longer-running branches.

### GitHub Flow
GitHub Flow is a lightweight, branch-based workflow that supports teams and projects where deployments are made regularly:

![GitHub Flow diagram](https://cloud.githubusercontent.com/assets/4215/16997425/a1e816a2-4e72-11e6-9de7-0961ada64ea6.png)

[@schacon](https://github.com/schacon) wrote the [original blog post describing GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html), including some comparisons to [git-flow](http://nvie.com/posts/a-successful-git-branching-model/), back in 2011.

At GitHub, we use the GitHub Flow pattern to collaborate, deploying feature branches to staging and production environments. We use a variety of tools and practices to make this possible. These include ChatOps, deploy queues, deploy locks, and more.

We've detailed our processes and tooling in our GitHub Engineering Blog post titled ["Deploying Branches to GitHub"](http://githubengineering.com/deploying-branches-to-github-com/).

**Additional resources:**
- [Our interactive GitHub Flow guide](https://guides.github.com/introduction/flow/)
  - [PDF version](https://guides.github.com/pdfs/githubflow-online.pdf)
- :tv: [Effective GitHubbing: The GitHub Flow](https://vimeo.com/68378254)

### Git flow
At a rudimentary level, Git flow is like GitHub Flow, but instead of merging into `master`, you're merging into other long-running branches like `develop`, `features`, `hotfix`, or `release`. Doing [`git rebase`](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) is common in this workflow.

In this flow, you create a `feature` branch, and rebase it inside the `develop` branch once the feature is completed and working. Once all new features related to a new release are rebased into the `develop` branch, it is merged into the `release` branch to be tested before the final deployment.

Note that once you branch your code from `develop` to `release`, you should avoid adding new features—but do fix bugs inside the `release` branch until it's stable. Once tested and stable, you can push `release` to the live deploy in `master`, and tag the version inside the `master` branch.

![Git flow diagram](https://cloud.githubusercontent.com/assets/4215/22933236/f7260d3e-f298-11e6-9f39-6f498011b702.png)

### Other workflow tips
- Strive for tightly-scoped, short-lived feature and bug branches (think days, not weeks or longer)
- `master` should represent the current production codebase
- Tight loops (branch from `master`, commits, merge back to `master`) can be a good indicator of healthy workflow, e.g.: https://github.com/github/backup-utils/network
  - In Customer's case, your tight loops would be from feature or bug branches into `master` or the release branch, if applicable
- Use [Releases](https://help.github.com/articles/creating-releases/) to record a history of versions if necessary. Upload any required build artifacts or other compiled binaries as [release attachments](https://developer.github.com/v3/repos/releases/#upload-a-release-asset) instead of checking them into version control
- Open pull requests early, even before code is written, to discuss implementation. Use [@mentions](https://help.github.com/articles/basic-writing-and-formatting-syntax/#mentioning-users-and-teams) to bring individuals or relevant teams into conversations
- Feature flags, or what [Martin Fowler refers to as "feature toggles"](https://martinfowler.com/articles/feature-toggles.html) can be an effective way to break large features into smaller components that can be tested and merged independently. At GitHub, we use [Flipper](https://github.com/jnunemaker/flipper) to manage the feature flags we use throughout GitHub.com
- [Scripts to rule them all](https://githubengineering.com/scripts-to-rule-them-all/) is a concept that we've embraced at GitHub to maintain a consistent set of scripts within every project to make bootstrapping, testing, and starting local versions of software easier. We've also [shared and open-sourced our default set of scripts](https://github.com/github/scripts-to-rule-them-all) so you can see how this works in practice (these are largely Ruby-specific, but the broader pattern can be adapted for any language or toolset)

## Protected branches and required status checks
[Protected branches ensure that collaborators on your repository cannot make irrevocable changes to branches](https://help.github.com/enterprise/admin/guides/developer-workflow/about-protected-branches-and-required-status-checks/). Protecting a branch disables force pushes to that branch, and also prevents the branch from being deleted on GitHub Enterprise. At the very least, protect `master`.

You can also require pull requests to pass a set of checks before they can be merged. For example, you can block pull requests that don't pass status checks. [See this help article for more information on protected branches and required status checks](https://help.github.com/enterprise/user/articles/defining-the-mergeability-of-pull-requests/).

[You can also restrict who can push to a protected branch](https://github.com/blog/2137-protected-branches-improvements), effectively limiting who can merge pull requests. Individual people or entire teams can be granted push access to protected branches. For many customers, **we've found that this eliminates the need for a "fork & pull request" collaboration model**.

## CI/Automated tests and the Status API
- Separate tests & status updates can provide developers with valuable additional context:
![Multiple status checks](https://cloud.githubusercontent.com/assets/4215/24567070/27e4b768-162a-11e7-964c-ad8801f4beca.png)
- Separating tests can help with parallelization and a "fail fast" approach to test pipelines.
- Even if your test pipeline is triggered by a single outbound webhook, you can configure component steps of your pipeline to [report statuses back to GitHub](https://developer.github.com/v3/repos/statuses/) separately.

## Code review
- Requiring [pull request reviews](https://help.github.com/enterprise/user/articles/about-pull-request-reviews/) means that contributors can't merge a pull request without another contributor's :+1:.
- Developers should [request reviews from specific collaborators](https://help.github.com/enterprise/2.12/user/articles/requesting-a-pull-request-review/) when they're ready to merge.
  - Customer should work on adding peer reviews into their process, and consider adding language-specific teams to the Organization for this purpose.
- [Protected branches](https://help.github.com/articles/about-branch-restrictions/) can restrict which users or teams can merge to specified branches; this will allow Customer developers to merge their feature branches into the release branch, but not into `master` and similar scenarios where you want to restrict merging.
  - In general for Customer, I recommend allowing developers to merge into `master` so as to not create bottlenecks in the process, but this is a feature to be aware of if you find it necessary for some projects.

## Documentation
Documentation helps keep track of all aspects of an application and improves the quality and usability of software. Having well documented projects and processes helps developers stay focused on development with minimal effort in the transfer of knowledge to contribute or use a piece of code. Successful documentation is easily accessible and discoverable, and helps new users get started quickly.

- Standardize hierarchy for repositories to make docs easily discoverable in a consistent way across repositories (e.g. `/docs`, `/images`, `/playbooks` etc.)
- Use the `innersource` repository we created to share scripts and templates that might be used or tweaked and used across teams (e.g. [`Jenkinsfile`s](https://jenkins.io/doc/book/pipeline/jenkinsfile/), `README.md`s, `CONTRIBUTING.md`s, `ISSUE_TEMPLATE`s, etc.)
- Make sure description fields for repositories are filled, as they are indexed and displayed in search results

### Repository docs
Each repository should contain two customized documents, `README.md` and `CONTRIBUTING.md`, which will enable developers to easily get started working with each repository. Additionally, each repository should also contain a `.gitignore`, and `CODEOWNERS` file, which will automate repository functions for Git-LFS and assigning reviewers.

You have your own customer templates we made stored in your innersource repository.

- [`README.md`](https://help.github.com/articles/about-readmes/)
  - [Relative links and image paths](https://help.github.com/articles/about-readmes/#relative-links-and-image-paths-in-readme-files)
  - [Project/product `README.md` template example](https://gist.github.com/allthedoll/bb80032b18f75903c9502bf692aa73c2)
  - [Team `README.md` template example](https://gist.github.com/allthedoll/76b36afa687b2e0a424a7851919830b8)
  - [Making `README`s readable](https://github.com/18F/open-source-guide/blob/18f-pages/pages/making-readmes-readable.md)
- [`CONTRIBUTING.md`](https://help.github.com/articles/setting-guidelines-for-repository-contributors/)
  - [`CONTRIBUTING.md` examples](https://help.github.com/articles/setting-guidelines-for-repository-contributors/#examples-of-contribution-guidelines)
- [`.gitignore`](https://github.com/github/gitignore)
  - To tell Git which [files to ignore](https://help.github.com/articles/ignoring-files/)
- [`CODEOWNERS`](https://help.github.com/articles/about-codeowners/)
  - [Require reviews for pull requests](https://help.github.com/articles/enabling-required-reviews-for-pull-requests)
  - [Introducing `CODEOWNERS` blog post](https://blog.github.com/2017-07-06-introducing-code-owners/)
  - [Require multiple reviewers for pull requests](https://blog.github.com/2018-03-23-require-multiple-reviewers/)

#### Additional repository documentation resources
- [About issue and pull request templates](https://help.github.com/articles/about-issue-and-pull-request-templates/)
  - [Creating `ISSUE_TEMPLATE`s](https://help.github.com/articles/manually-creating-a-single-issue-template-for-your-repository/)
  - [Creating `PULL_REQUEST_TEMPLATE`s](https://help.github.com/articles/creating-a-pull-request-template-for-your-repository/)

#### Repository tools
- [truffleHog](https://github.com/dxa4481/truffleHog)
- [GitHub Marketplace](https://github.com/marketplace)
- [GitHub Actions](https://github.com/features/actions)
- Our [Works with GitHub](https://github.com/works-with) directory is a comprehensive listing of products that integrate with the GitHub platform, including [GitHub Enterprise](https://github.com/works-with?utf8=%E2%9C%93&enterprise_compatible=on)
- [Ngrok](https://ngrok.com) - a secure localhost tunnel, perfect for testing webhooks

## Helpful resources for users
- Review the Git you learned with @hollenberry by following our GitHub for Developers [training manual](https://githubtraining.github.io/training-manual/#/01_getting_ready_for_class)
- [Git aliases](https://githubtraining.github.io/training-manual/#/app_aliases)
- [`.gitconfig`](https://www.numpy.org/devdocs/dev/gitwash/configure_git.html)
- [@allthedoll's person .gitconfig](https://gist.github.com/allthedoll/1a5bb4e6a6d7eb1b9bda29339eccc56d)
- [@allthedoll's template stash](https://github.com/allthedoll/template-stash)
- [Mastering Markdown](https://guides.github.com/features/mastering-markdown/)
- [Basic Git commands](https://guides.github.com/introduction/git-handbook/#basic-git)
- [Other guides](https://guides.github.com/)
- [Dealing with line endings](https://help.github.com/articles/dealing-with-line-endings/)
- GitHub's [Deployments API](https://developer.github.com/v3/repos/deployments/)

## Open source resources
- [Hubot](https://hubot.github.com) - our open-source, customizable, life embetterment robot 🤖
- [PRobot](https://github.com/probot/probot) - a trainable robot that responds to activity on GitHub. The [Probot organization](https://github.com/probot) also contains a variety of Probot plugins
- [Hygieia](https://github.com/capitalone/Hygieia) - CapitalOne's open-source DevOps dashboard
- [Flipper](https://github.com/jnunemaker/flipper) - Feature flagging/flipping for Ruby applications
- [Scientist](https://github.com/github/scientist) - A Ruby library for refactoring critical paths using the [branch by abstraction](https://martinfowler.com/bliki/BranchByAbstraction.html) pattern. We detailed the philosophy behind and implementation of Scientist [in this engineering blog post](https://githubengineering.com/scientist/)
