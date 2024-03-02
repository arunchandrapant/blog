---
date: 2024-03-02
title: Checklist for new .NET C# projects
categories:
  - C# Notes
---

When starting a new .NET C# project, get these things right from the start.

<!-- more -->

> These are my notes on C#. Information here is best to my limited knowledge and may be incomplete or incorrect. Use a definitive source as reference for your use cases. This page might be updated in future.

It's important to start things the right way. If start is not proper, you might end up with poor quality down the road, fixing which will take a lot more effort as time passes by.

While starting a new .NET C# project on any supported (.NET 6.0 and .NET 8.0) version, you can go through these points and incorporate those that make sense for your project.

These consierations apply only for projects that will last for some time and see regular contributions. For small proof of concepts and other temporary projects, these might be unnecessary.

## NuGet Dependency Management
### Enable Central Package Management
Once you have multiple projects in a solution with many dependencies, tracking their version and keeping them update with security fixes can be challenging. With [Central Package Management][central-package-management], package versions are managed centrally in a *'Directory.Packages.Props'* file at the solution (or repository) root. This is particularly useful for mono repos that contain many projects in a single repository. Onboarding is as simple as adding a *'ManagePackageVersionsCentrally'* MSBuild property to *'True'*. This is fairly easy to setup even for projects that have significant past contributions. If you want to override package version for a specific project, it's possible by using hierarchy of version files. After enabling this feature, you use NuGet as you usually do and Visual Studio (or dotnet CLI) will take care of updating correct files.

### Enable Repeatable Package Restore
There are a bunch of nitty-gritties in NuGet package restore process, which can lead to dependencies for same commit being restored differently at different times. These are well explained in the [Repeatable Package Restore][repeatable-package-restore] feature blog. By enabling this feature restore process involves a *'packages.lock.json'* file that contains exact dependencies (recursive sub-dependencies) with cryptographic hashes. When restoring, operation fails if exact match is not resolved. Enabling this also provides you protection against some supply chain attacks that target NuGet ecosystem. Enabling this feature in a project with significant past contributions is easy.

### Enabling Central Package Feed With Upstream Source Integration
If you project needs to restore NuGet packages from multiple sources, possibly including both private (internal to your organization) and public sources, then you can consider use a central private source with integration to upstream sources. It's very easy to setup in Azure DevOps using [Azure Artifacts Upstream Sources][azure-artifacts-upstream-sources] feature. Benefits include a simplified NuGet setup in your project, protection against supply chain attacks and some other benefits. By ordering upstream sources properly, you can protect against package substitution supply chain attacks.


## Code Quality
### Configure Code Analysis
[Code analysis][code-analysis] is the native code analysis tool for C#. It is built-in and enabled by default for all recent .NET framework versions. However a very small set of rules are enabled in the 'Default' profile. Full ruleset is vast and covers many areas including design, maintainability, security, reliability and performance. You will have to manually configure your project to use larger - 'Recommended' profile. Enabling recommended profile will make sure that some common quality issues are surfaced as warnings at built time which can be fixed to keep code in a good stage. It's strongly recommended to enable this while starting a new project, otherwise acummulated debt over time can make it difficult to enable and fix all issues at a later stage. 

### Install StyleCop Analyzers
[StyleCop Analyzers][stylecop-analyzers] as the name suggests deals with consistent styling of source code. It has a large set of opinionated style rules, for which code is scanned and violations are raised. This ensures that your codebase looks like a team-work rather than a patch-work involving multiple people who follow their own styles. Enabling this from start is recommended to prevent acummulated tech debt over time. A tool like this is especially required for C# as it allows for very flexible styling.

## DevOps
### Release Versioning Using Nerdbank.GitVersioning
There can be multiple strategies for release versioning depending on the project requirements. [Nerdbank.GitVersioning][nerdbank-git-versioning] is an attempt to tackle this age old problem using a new approach. It maintains [Semantic Versioning][sematic-versioning] of project releases by comitting the version data to a *'version.json'* file directly in the project Git repository. This is opposed to using Git Tags that are not very stable and often manually created, although you can also create tags using this tool. At the time of building the project in pipeline, it can tag generated DLLs with release version, which can then be referenced and consumed from inside the code.

### Setup Quality Gate Workflow
Setting up a quality gate workflow early on can prevent technical debt from acummulating. You can run all sorts of quality, security and compliance check in this workflow whenever a new contribution is pushed to your repository. One common strategy is to let contributions only via Pull Requests (PR) and run quality gate workflow everytime a PR is created or updated. Merging of PR can be blocked until all issues reported by workflow are fixed.

## Security
### CodeQL
[CodeQL][code-ql] is a static analysis (SAST) tool that is typically used to surface security issues in code. It has easy and well-documented integration with GitHub repositories. For compiled languages like C#, the setup is a bit more complex as it involves buliding the code. It makes sense to have CodeQL as a part of Quality Gate workflow in your repository. Issues surfaced are annotated in the pull request and also visible directly in GitHub repository security tab. It's free for public repositories and can be licensed for commercial uses.

[central-package-management]: https://learn.microsoft.com/en-us/nuget/consume-packages/Central-Package-Management
[repeatable-package-restore]: https://devblogs.microsoft.com/nuget/enable-repeatable-package-restores-using-a-lock-file/
[azure-artifacts-upstream-sources]: https://learn.microsoft.com/en-us/azure/devops/artifacts/concepts/upstream-sources?view=azure-devops
[code-analysis]: https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/overview?tabs=net-8
[stylecop-analyzers]: https://github.com/DotNetAnalyzers/StyleCopAnalyzers
[nerdbank-git-versioning]: https://github.com/dotnet/Nerdbank.GitVersioning/tree/main#nerdbankgitversioning
[sematic-versioning]: https://semver.org/
[code-ql]: https://codeql.github.com/