# Jamstack Website

This repository will contain the 11ty implementation and needed Infrastructure as Code (IaC) to get this project deployed to AWS.

This is a placeholder at the moment as the original code is getting a bit of clean up to make the code more generic.

---
**NOTE**

Do not use this repository directly, the "Use this template" button should be utilized to create your own copy of this repository.  This will allow you to make modifications to everything in this repository to suit your needs.  When there are enhancements to the source repository, you can always pull them in as needed.

---

## Table of Contents

- [Related Repositories](#related-repositories)
- [License](#license)

---

# Related Repositories

This repository is part of a small group of repositories that are used together in order to deploy a simple 11ty Jamstack website.  Here is the list of related repositories:

1. [Jamstack Website Core](https://github.com/ngamradt/boilerplate-jamstack-website) (this repository): This repository is the core repository that stitches everything together.  It houses the following components:
    - The main AWS CodePipeline that will deploy all infrastructure and the Jamstack website itself.
    - All needed CloudFormation templates to create the required infrastructure.
    - The 11ty liquid templates for the structure of the website.
    - The supporting CSS and JavaScript files for the Jamstack site.
    - All of the source content files for each blog post.
2. [Jamstack Website Tools](https://github.com/ngamradt/boilerplate-jamstack-website-tools): This repository has any supporting tools for the Jamstack website.
3. [Jamstack Website Build](https://github.com/ngamradt/boilerplate-jamstack-website-build): This repository has the Lambda function that is used to produce the 11ty dynamic pages.
4. [Jamstack Website Static Resources](https://github.com/ngamradt/boilerplate-jamstack-website-static): This repository has all large static files that are used either by the site in general, or by the individual posts.  These files are kept separate in order to keep the build process fast and easy.

These repositories are part of my ["Blog on a Budget" series](https://nealgamradt.com/posts/2023/06/blog-on-a-budget-overview/index.html).  View that blog post series for more details on how these repositories all work together.

# License

This repository is released under [the MIT license](https://en.wikipedia.org/wiki/MIT_License).  View the [local license file](./LICENSE).