# GRACE Style Guide

## Terraform

The GRACE development team will use the [**_Terraform Best Practices_**](https://www.terraform-best-practices.com/) GitBook by [Anton Babenko](https://github.com/antonbabenko) as a style guide for GRACE Terraform code.  The team will document additional clarification, modifications, deviations and addendums to the guide in this repository.

### References for Terraform Style Guides

1. [Terraform Best Practices](https://www.terraform-best-practices.com/)
1. [Hashicorp Terraform Style Conventions](https://www.terraform.io/docs/configuration/style.html)
1. [Hashicorp Terraform Recommended Practices](https://www.terraform.io/docs/enterprise/guides/recommended-practices/index.html)
1. [Hashicorp Terraform Getting Started - AWS Dependencies](https://learn.hashicorp.com/terraform/getting-started/dependencies)
1. [GRACE IAM Naming Standard-Draft](https://docs.google.com/document/d/1XWOmYadQg4ya4saVc3IeupfObGmmD6Bqb0nV702ZwGA)
1. [Jon Brouse Terraform Style Guide](https://github.com/jonbrouse/terraform-style-guide)
1. [ASICS Digital Group Terraform Style Guide](https://github.com/asicsdigital/terraform-reference/wiki/Terraform-Style-Guide)
1. [How to use Terraform as a team – Gruntwork - Gruntwork Blog](https://blog.gruntwork.io/how-to-use-terraform-as-a-team-251bc1104973)
1. [Introducing Terraform at GumGum](https://techblog.gumgum.com/articles/introducing-terraform-at-gumgum)
1. [Template/Module iteration terraform style - Google Groups](https://groups.google.com/d/topic/terraform-tool/b97OFyNcNkQ)
1. [Pete's Terraform Tips – Pete Shima – Medium](https://medium.com/@petey5000/petes-terraform-tips-694a3c4c5169)

## Go

GRACE was using the [`gometalinter`](https://github.com/alecthomas/gometalinter) to enforce community recommended style guidelines on Go code.  However, the `gometalinter` project has been deprecated in favor of [`golangci-lint`](https://github.com/golangci/golangci-lint) and GRACE will be migrating to this linter instead.

## AWS Resource Naming

### IAM

There is a draft [**_GRACE IAM Naming Standard_**](https://docs.google.com/document/d/1XWOmYadQg4ya4saVc3IeupfObGmmD6Bqb0nV702ZwGA) in Google Docs.

## Public domain

This project is in the worldwide [public domain](LICENSE.md). As stated in [CONTRIBUTING](CONTRIBUTING.md):

> This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).
>
> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.
