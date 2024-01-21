---
layout: post
title:  "C# Notes: Static Code Analysis"
date:   2024-01-21 20:00:00 +0530
categories: CSharp Notes
---

> This article contains my notes on C#. Information here is best to my limited knowledge and may be incomplete or incorrect. Use a definitive source as reference for your use cases. This page will be updated with new information as I find out more.

Static code analysis is performed on the code while it is 'static', in other words 'not running'. It is in contrast to 'dynamic' or 'runtime' analysis that is done while code is executing. 

Static code analysis can surface styling, consistency, maintainability, logic and security related issues in code.

In contrast runtime analysis can be used to surface performance (memory, CPU, latency) issues, security issues and execution statistics.

Static code analysis is a very important in any software project. Modern languages are so vast (especially C#), that without static code analsyis it's virtually impossible for (less-experienced) programmers to write good quality code. A nice thing is that most static analysis tools can highlight issues interactively at the time of writing code in any modern IDE like VS and VS Code.

Furthermore these tools can be integrated into CI/CD processes, such that if any issues are surfaced, the pull request can be blocked and code anotated with issues. This ensures that entire team follows rules configured for the project.

In this article, I will give a brief about static code analysis tools available for C#.

## Code Analysis
[Code analysis][code-analysis] is a native code analysis tool for C#. It is enabled by default for all recent .NET framework versions. However a very small set of rules are enabled in the 'Default' profile. Full ruleset is vast and covers many areas including design, maintainability, security, reliability and performance. You will have to manually configure your project to use larger - 'Recommended' profile.

Rules surfaced by code anlaysis are shown with ID prefixed by 'CA' or 'IDE'. For instance 'CA2260'. If you are using Visual Studio and VS Code, issues are visible interactively visible on UI while editing/browsing code.

Issues are surfaced as build warnings. You have option of configuring them as build errors, if you want to be strict.

Configuring this tool is very easy. Just set some properties in 'csproj' file. That way it will be commited into source control ensuring uniform setup across team.

Configuring in CI pipelines can be difficult as the provided [GitHub action][code-analysis-github-action] has not been updated in years, is still in beta and only available for Windows. Another option is to write custom script/action to achieve the same.

## StyleCop Analyzers
[StyleCop Analyzers][stylecop-analyzers] as the name suggests deals with consistent styling of source code. It has a large set of opinionated style rules, for which code is scanned and violations are raised.  

It has to be installed as a nuget package at the project level. If there are many projects in a solution, 'Directory.Build.props' file can be used to configure it for all projects in a solution. Rules can be configured via a '.ruleset' file which can be set at solution level.

Violations are surfaced as build warnings. While coding in editor (VS/Code), issues are surfaced interactively. Wherever possible automated corrections are suggested. Integration with CI pipeline can be done by writing custom script.

Since it is focused on a very narrow area of styling, it must be used in combination with analyzers that cover other essential areas.

## SonarSource
[SonarSource][sonar-source] are a set of rules that work with [SonarQube][sonar-qube] - a popular multi-language code quality tool. Rules cover broad areas (security, logic, design, clean code etc.) with a lot of overlap to Code analysis rules. A major area of analysis is - 'Clean Code Attributes'. These attributes are categorized into four areas - 'Consistent', 'Intentional', 'Adaptable' and 'Responsible'. For more details on this classification refer their [document][soanr-clean-code-attributes].

To configure these rules for interactive editing in IDE, use [SonarLint][sonar-lint] IDE plugin. However, to gain full advantage of features, integrate it into SonarQube via CI pipeline. CI integration is very easy with ready made actions for most CI platforms. After integration, it's very easy to navigate issues using web UI provided by the product.

SonarQube provides many code quality features apart from typical code analysis. For instance it highlights duplciate lines and offers unit-test coverage integration. It implements 'Clean as you Code' methodology which helps in reducing tech-debt over time. It has excellent documentation on all aspects of configuring and integrating it into your project.

Downside is that, you have to pay for commercial version. While community version exists, it's difficult to integrate and use it efficiently in real life projects. 

## CodeQL
[CodeQL][code-ql] is a semantic code analysis tool owned by GitHub. It supports many languages including C#. It is provided as a part of GitHub Advanced Security suite, where it's used to surface security vulnerability. However, you can use free [command line][codeql-cli] version and run it for [broader analysis][codeql-csharp-rules] which includes checks similar to SonarSource and Code analysis.

CodeQL is a popular SAST (Static Application Security Testing) tool, typically used by security researchers. It supports a query language using which custom checks can be written. This makes it the only tool in the list which is extensible and customisable for those who want to put in that level of effort (not practical for most people).

It can be easily integrated into CI pipeline using [codeql-action][codeql-action] provided by GitHub. The default integration is very easy but checks only for security issues (can be modified to do more checks). You can do custom integration with scripts via CodeQL CLI.

Before analysis, code needs to be built, during which a database is populated. Analysis is run over this database using queries. On large codebases the entire process can take long (10+ minutes). Output is generated to SARIF files which can be uploaded to GitHub UI for interactive viewing.

Due to it's nature, an IDE plugin which can interactively surface issues while coding is not available.

[code-analysis]: https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/overview?tabs=net-8
[code-analysis-github-action]: https://github.com/dotnet/code-analysis
[stylecop-analyzers]: https://github.com/DotNetAnalyzers/StyleCopAnalyzers
[sonar-source]: https://rules.sonarsource.com/csharp/
[sonar-qube]: https://www.sonarsource.com/products/sonarqube/
[soanr-clean-code-attributes]: https://docs.sonarsource.com/sonarqube/10.3/user-guide/clean-code/
[sonar-lint]: https://www.sonarsource.com/products/sonarlint/
[ft-business-book-2022]: https://ig.ft.com/sites/business-book-award/books/2022/winner/chip-war-by-chris-miller/
[code-ql]: https://codeql.github.com/
[codeql-cli]: https://docs.github.com/en/code-security/codeql-cli/getting-started-with-the-codeql-cli
[codeql-csharp-rules]: https://github.com/github/codeql/tree/main/csharp/ql/src
[codeql-action]: https://github.com/github/codeql-action