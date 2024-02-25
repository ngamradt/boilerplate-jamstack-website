# Jamstack Website

The intent of this project is to allow a web development engineer, who wants to use Markdown for their blog posts, to set up a small and affordable personal blog.  It could also be used for a small business informational website, but this is not intended for any large-scale sites.  Though the foundations for a large-scale informational website are here, you would want to add some additional features for a larger site (which is outside the scope of this documentation).

Based on some rough calculations, I suspect this solution should have no problem supporting hundreds of medium-sized blog posts.  Given I cannot ever see writing more than one a week, this should all work for years before running close to any limits.

This repository contains the main [11ty](https://www.11ty.dev) implementation and needed [Infrastructure as Code (IaC)](https://en.wikipedia.org/wiki/Infrastructure_as_code) to get this project deployed to [Amazon Web Services (AWS)](https://aws.amazon.com/).

> [!IMPORTANT]
> Do not use this repository directly, the "Use this template" button should be utilized to create your own copy of this repository.  This will allow you to make modifications to everything in this repository to suit your needs.  When there are enhancements to the source repository, you can always pull them in as needed.

> [!TIP]
> This project was designed to run from [AWS CodeCommit](https://aws.amazon.com/codecommit/).  Once you have a copy of this repository, you can upload it to CodeCommit in your personal AWS account.  You can read my ["Blog on a Budget" series](https://nealgamradt.com/posts/2023/06/blog-on-a-budget-overview/index.html#series-posts) to get details on how to set up your account.  It would be possible for the [CodePipeline template](v1/iac/cfn/codepipeline/main.yaml) to be modified to pull from GitHub, but that is outside the scope of this documentation.  I chose to use CodeCommit because I really had not worked with it in a long time, so I wanted to see how much it had improved.

---

## Table of Contents

- [Related Repositories](#related-repositories)
- [Project Setup](#project-setup)
- [License](#license)

---

# Related Repositories

This repository is part of a small group of repositories that are used together in order to deploy a simple 11ty Jamstack website.  Here is the list of related repositories:

1. [Jamstack Website Core](https://github.com/ngamradt/boilerplate-jamstack-website) (this repository): This repository is the core repository that stitches everything together.  It houses the following components:
    - The [main AWS CodePipeline](v1/iac/cfn/codepipeline/main.yaml) that will deploy all infrastructure and the Jamstack website itself.
    - All needed [CloudFormation templates](v1/iac/cfn) to create the required infrastructure.
    - The 11ty [liquid templates](v1/dynamic/_includes) for the structure of the website.
    - The [supporting CSS and JavaScript files](v1/content/assets) for the Jamstack site.
2. [Jamstack Website Tools](https://github.com/ngamradt/boilerplate-jamstack-website-tools): This repository has any supporting tools for the Jamstack website.
3. [Jamstack Website Build](https://github.com/ngamradt/boilerplate-jamstack-website-build): This repository has the Lambda function that is used to produce the 11ty dynamic pages.
4. [Jamstack Website Static Resources](https://github.com/ngamradt/boilerplate-jamstack-website-static): This repository has all large static files that are used either by the site in general, or by the individual posts.  These files are kept separate in order to keep the build process fast and easy.

> [!NOTE]
> This polyrepo setup was done to keep the deployment process simple and keep the size of the core repository small (thus helping keep deployments fast).  In theory these could have all been grouped together, but in this case, each repository serves a distinct function, so I am okay with this setup for my personal website.

These repositories are part of my ["Blog on a Budget" series](https://nealgamradt.com/posts/2023/06/blog-on-a-budget-overview/index.html).  View that blog post series for more details on how these repositories all work together.

# Project Setup

## Prerequisites

1. You will need to have already set up a [free AWS account](https://aws.amazon.com/free/).  This is covered in [this blog post](https://nealgamradt.com/posts/2023/06/aws-account-billing-budgets/index.html). 
2. You will need to have already purchased your own [domain name](https://en.wikipedia.org/wiki/Domain_name).
3. Copies of the [files](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-create-file.html#how-to-create-file-console) from the [Jamstack Website Core](https://github.com/ngamradt/boilerplate-jamstack-website), [Jamstack Website Build](https://github.com/ngamradt/boilerplate-jamstack-website-build), and [Jamstack Website Static Resources](https://github.com/ngamradt/boilerplate-jamstack-website-static) repositories will have to be put into [AWS CodeCommit repositories](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-create-repository.html#how-to-create-repository-console) in your AWS account.

> [!TIP]
> If you purchase your domain name through [AWS](https://aws.amazon.com/getting-started/hands-on/get-a-domain/), it is possible that you won't need [this Route53 template](v1/iac/cfn/route53/hosted-zone/public.yaml).  You would then want to comment out [this block](https://github.com/ngamradt/boilerplate-jamstack-website/blob/61f36c1106b034a665f9466c17f1460fc0cfa0c8/v1/iac/cfn/codepipeline/main.yaml#L595) in the CodePipeline template.

> [!IMPORTANT]
> I strongly recommend [setting up a budget](https://nealgamradt.com/posts/2023/06/aws-account-billing-budgets/index.html#set-up-an-actual-cost-budget) with your account before you launch you site, this ensures that you do not have any surprise billing.

## Getting Started

This project uses an [AWS CodePipeline](https://aws.amazon.com/codepipeline/) to deploy all of the needed CloudFormation templates to your AWS account.  However, there is [one initial template](v1/iac/cfn/codepipeline/main.yaml) that you need to manually deploy.  I recommend doing that through the AWS Console.  From that point forward, all of the other infrastructure will be deployed by the CodePipeline.  The CodePipeline will pull all of the other CloudFormation templates from your repository and then deploy them.  It will also deploy any future changes that you make to the templates through the repository.  This is a concept known as [GitOps](https://en.wikipedia.org/wiki/DevOps#GitOps).

The CodePipeline has a number of parameters that you need to fill out before you can create the stack.  Some of these parameters have `Yes/No` values that you need to select; these enabled and disabled features of the CodePipeline.  Because of this, I call this a "polymorphic" CodePipeline as it changes based on what we need it to do.

> [!NOTE]
> The only reason I am reusing the same CodePipeline for everything is to keep things as cost-effective as possible.  You get one free CodePipeline per AWS account, so to keep my blog costs to a minimum, I decided to just make my one free CodePipeline smarter.  This is not usually a recommended practice, but was sort of fun to do and actually works well.

To kick off everything, you will need to do the following:

1. [Sign in](https://signin.aws.amazon.com) into the AWS Console with your [IAM credentials](https://nealgamradt.com/posts/2023/06/aws-create-iam-user/index.html).
2. Go to the [CloudFormation console](https://us-east-1.console.aws.amazon.com/cloudformation/home).
3. Ensure you are in the region you want to set things up.  I set mine up in the Ohio (us-east-2) region.
4. Start the process to create a new [CloudFormation stack](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html).
5. You will then need to use a copy of [this CodePipeline template file](v1/iac/cfn/codepipeline/main.yaml) to create the stack.
6. You will need to fill in the parameters for the stack.
    - For all of the `Yes/No` parameters, the correct value should have already been selected.
    - The General Configuration and CloudFront Configuration values are most-likely the main values you will want to update.
7. Once the stack completes successfully, it should have created a new [CodePipeline](https://us-east-2.console.aws.amazon.com/codesuite/codepipeline/pipelines) (in your selected region).
    - If you don't see your CodePipeline, mkae sure to double-check your region.
8. If all the repositories were set up correctly, you should see the CodePipeline trigger and start setting up infrastructure.

> [!CAUTION]
> If your domain name is not hosted with AWS and the CodePipeline creates your Route53 Zone, you will need to get the NS records added to your DNS provider before the ACM certificates will be able to complete.  You cannot point your DNS to the proper NS records until they are created by the template.

Once the initial setup is complete, you will need to then deploy the Lambda:

1. Go back to the [CloudFormation console](https://us-east-1.console.aws.amazon.com/cloudformation/home).
2. Ensure you are in the correct region.
3. Select the stack that you previously created through the console in the previous series of steps.
4. Select the option to update the stack.
5. Switch the value of the `EnableInfrastructureStage` parameter to `No`.
6. Switch the value of the `EnableLambdaStage` parameter to `Yes`.
7. Run the stack updates.
8. If everything is set up correctly, the Lambda (that is used to process the 11ty templates into static pages) should be successfully deployed.

Now that the Lambda is deployed, we will want to set it to start building and deploying files to our S3 origin:

1. Go back to the [CloudFormation console](https://us-east-1.console.aws.amazon.com/cloudformation/home).
2. Ensure you are in the correct region.
3. Select the stack that you previously created through the console in the first series of steps.
4. Select the option to update the stack.
5. Switch the value of the `EnableLambdaStage` parameter to `No`.
6. Switch the value of the `EnableBuildStage` parameter to `Yes`.
7. Switch the value of the `EnableDeployStage` parameter to `Yes`.
8. Switch the value of the `EnableStaticStage` parameter to `Yes`.
9. Run the stack updates.
10. If everything is set up correctly, the CodePipeline should now be set up to build and deploy your 11ty files to the S3 origin of your website.
11. Once the files are deployed to S3, you should be able to reach your site homepage.

For ongoing use of the CodePipeline, you would enable and disable different stages of the CodePipeline (using parameters) as needed.  For instance, if you needed to redeploy the Lambda with updates, you would switch the value of `EnableLambdaStage` to `Yes`.  This would then enable that stage and the updates would get deployed.  You can then disable this stage again once you are done.

> [!NOTE]
> This solution is **not** using [S3 web hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EnableWebsiteHosting.html), just direct access to S3 objects.  Due to this fact, the only `index.html` page that automatically resolves is the root one for your homepage.  If you a linking directly to any posts, you need to link to the full URL which includes the `index.html`.  This is a consequence of cost (and to an extent, speed) being my main driver for this project.  I personally don't care that this works this way, because I suspect people will never be typing in the full path to my individual blog posts; a vast majority of the time it will be a link that they click on.  If this is important to you, there are a number of ways to get the directory index to work with this solution, and I may add that as an enhancement to this project in the future.  However, for now, if you use this solution, always link to the `index.html` page.  This doesn't seem to hurt SEO because this solution does create a `sitemap.xml` file to help search engines crawl the site.

# License

This repository is released under [the MIT license](https://en.wikipedia.org/wiki/MIT_License).  View the [local license file](./LICENSE).