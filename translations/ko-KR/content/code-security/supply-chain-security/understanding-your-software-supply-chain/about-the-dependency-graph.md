---
title: About the dependency graph
intro: You can use the dependency graph to identify all your project's dependencies. The dependency graph supports a range of popular package ecosystems.
redirect_from:
  - /github/visualizing-repository-data-with-graphs/about-the-dependency-graph
  - /code-security/supply-chain-security/about-the-dependency-graph
versions:
  fpt: '*'
  ghes: '*'
  ghae: issue-4864
  ghec: '*'
type: overview
topics:
  - Dependency graph
  - Dependencies
  - Repositories
shortTitle: Dependency graph
---

<!--For this article in earlier GHES versions, see /content/github/visualizing-repository-data-with-graphs-->
<!--Marketing-LINK: From /features/security and /features/security/software-supply-chain pages "How GitHub's dependency graph is generated".-->

## Dependency graph availability

{% ifversion fpt or ghec %}The dependency graph is available for every public repository that defines dependencies in a supported package ecosystem using a supported file format. Repository administrators can also set up the dependency graph for private repositories.{% endif %}

{% data reusables.dependabot.dependabot-alerts-dependency-graph-enterprise %}

## About the dependency graph

The dependency graph is a summary of the manifest and lock files stored in a repository. For each repository, it shows{% ifversion fpt or ghec %}:

- Dependencies, the ecosystems and packages it depends on
- Dependents, the repositories and packages that depend on it{% else %} dependencies, that is, the ecosystems and packages it depends on. {% data variables.product.product_name %} does not calculate information about dependents, the repositories and packages that depend on a repository.{% endif %}

When you push a commit to {% data variables.product.product_name %} that changes or adds a supported manifest or lock file to the default branch, the dependency graph is automatically updated.{% ifversion fpt or ghec %} In addition, the graph is updated when anyone pushes a change to the repository of one of your dependencies.{% endif %} For information on the supported ecosystems and manifest files, see "[Supported package ecosystems](#supported-package-ecosystems)" below.

{% ifversion fpt or ghes > 3.1 or ghae or ghec %}
When you create a pull request containing changes to dependencies that targets the default branch, {% data variables.product.prodname_dotcom %} uses the dependency graph to add dependency reviews to the pull request. These indicate whether the dependencies contain vulnerabilities and, if so, the version of the dependency in which the vulnerability was fixed. For more information, see "[About dependency review](/code-security/supply-chain-security/about-dependency-review)."
{% endif %}

## Dependencies included

The dependency graph includes all the dependencies of a repository that are detailed in the manifest and lock files, or their equivalent, for supported ecosystems. 여기에는 다음이 포함됩니다.

- Direct dependencies, that are explicitly defined in a manifest or lock file
- Indirect dependencies of these direct dependencies, also known as transitive dependencies or sub-dependencies

The dependency graph identifies indirect dependencies{% ifversion fpt or ghec %} either explicitly from a lock file or by checking the dependencies of your direct dependencies. For the most reliable graph, you should use lock files (or their equivalent) because they define exactly which versions of the direct and indirect dependencies you currently use. If you use lock files, you also ensure that all contributors to the repository are using the same versions, which will make it easier for you to test and debug code{% else %} from the lock files{% endif %}.

{% ifversion fpt or ghec %}
## Dependents included

For public repositories, only public repositories that depend on it or on packages that it publishes are reported. This information is not reported for private repositories.{% endif %}

## Using the dependency graph

You can use the dependency graph to:

- Explore the repositories your code depends on{% ifversion fpt or ghec %}, and those that depend on it{% endif %}. For more information, see "[Exploring the dependencies of a repository](/github/visualizing-repository-data-with-graphs/exploring-the-dependencies-of-a-repository)." {% ifversion fpt or ghec %}
- View a summary of the dependencies used in your organization's repositories in a single dashboard. For more information, see "[Viewing insights for your organization](/articles/viewing-insights-for-your-organization#viewing-organization-dependency-insights)."{% endif %}
- View and update vulnerable dependencies for your repository. For more information, see "[About alerts for vulnerable dependencies](/code-security/supply-chain-security/about-alerts-for-vulnerable-dependencies)."{% ifversion fpt or ghes > 3.1 or ghec %}
- See information about vulnerable dependencies in pull requests. For more information, see "[Reviewing dependency changes in a pull request](/github/collaborating-with-issues-and-pull-requests/reviewing-dependency-changes-in-a-pull-request)."{% endif %}

## Enabling the dependency graph

{% ifversion fpt or ghec %}To generate a dependency graph, {% data variables.product.product_name %} needs read-only access to the dependency manifest and lock files for a repository. The dependency graph is automatically generated for all public repositories and you can choose to enable it for private repositories. For information about enabling or disabling it for private repositories, see "[Exploring the dependencies of a repository](/github/visualizing-repository-data-with-graphs/exploring-the-dependencies-of-a-repository)."{% endif %}

{% ifversion ghes or ghae %}If the dependency graph is not available in your system, your enterprise owner can enable the dependency graph and {% data variables.product.prodname_dependabot_alerts %}. For more information, see  "[Enabling the dependency graph and {% data variables.product.prodname_dependabot_alerts %} on your enterprise account](/admin/configuration/managing-connections-between-your-enterprise-accounts/enabling-the-dependency-graph-and-dependabot-alerts-on-your-enterprise-account)."{% endif %}

When the dependency graph is first enabled, any manifest and lock files for supported ecosystems are parsed immediately. The graph is usually populated within minutes but this may take longer for repositories with many dependencies. Once enabled, the graph is automatically updated with every push to the repository{% ifversion fpt or ghec %} and every push to other repositories in the graph{% endif %}.

## Supported package ecosystems

The recommended formats explicitly define which versions are used for all direct and all indirect dependencies. If you use these formats, your dependency graph is more accurate. It also reflects the current build set up and enables the dependency graph to report vulnerabilities in both direct and indirect dependencies.{% ifversion fpt or ghec %} Indirect dependencies that are inferred from a manifest file (or equivalent) are excluded from the checks for vulnerable dependencies.{% endif %}

| Package manager | 다국어 지원                           | Recommended formats                                    | All supported formats                                                     |
| --------------- | -------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------- |
| Composer        | PHP                              | `composer.lock`                                        | `composer.json`, `composer.lock`                                          |
| `dotnet` CLI    | .NET languages (C#, C++, F#, VB) | `.csproj`, `.vbproj`, `.nuspec`, `.vcxproj`, `.fsproj` | `.csproj`, `.vbproj`, `.nuspec`, `.vcxproj`, `.fsproj`, `packages.config` |
{%- ifversion fpt or ghes > 3.2 or ghae %}
| Go modules | Go | `go.sum` | `go.mod`, `go.sum` |
{%- elsif ghes = 3.2 %}
| Go modules | Go | `go.mod` | `go.mod` |
{%- endif %}
| Maven | Java, Scala |  `pom.xml`  | `pom.xml`  | | npm | JavaScript |            `package-lock.json` | `package-lock.json`, `package.json`| | Python PIP      | Python                    | `requirements.txt`, `pipfile.lock` | `requirements.txt`, `pipfile`, `pipfile.lock`, `setup.py`* |
{%- ifversion fpt or ghes > 3.3 %}
| Python Poetry | Python                    | `poetry.lock` | `poetry.lock`, `pyproject.toml` |{% endif %} | RubyGems             | Ruby           | `Gemfile.lock` | `Gemfile.lock`, `Gemfile`, `*.gemspec` | | Yarn | JavaScript | `yarn.lock` | `package.json`, `yarn.lock` |

{% note %}

**Note:** If you list your Python dependencies within a `setup.py` file, we may not be able to parse and list every dependency in your project.

{% endnote %}

## 더 읽을거리

- "[Dependency graph](https://en.wikipedia.org/wiki/Dependency_graph)" on Wikipedia
- "[Exploring the dependencies of a repository](/github/visualizing-repository-data-with-graphs/exploring-the-dependencies-of-a-repository)"{% ifversion fpt or ghec %}
- "[Viewing insights for your organization](/organizations/collaborating-with-groups-in-organizations/viewing-insights-for-your-organization)"{% endif %}
- "[Viewing and updating vulnerable dependencies in your repository](/github/managing-security-vulnerabilities/viewing-and-updating-vulnerable-dependencies-in-your-repository)"
- "[Troubleshooting the detection of vulnerable dependencies](/github/managing-security-vulnerabilities/troubleshooting-the-detection-of-vulnerable-dependencies)"
