---
layout: post
title:  "Interact with Route 53"

categories:
  - DNS
tags:
  - DNS
  - Route53
  - API
  - AWS

excerpt: A short guide to interact with Route 53
excerpt_separator:  <!--more-->

permalink: /:categories/:title/
---

# Route 53

You've seen this everywhere, and perhaps you use it too! Maybe you've just recently adopted or looking to adapt to [Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html).

The idea of me writing this article is to show one of the many (10,000) methods how to interact with Route 53 to

- add records
- update records
- delete records

in <b>Route 53<b>


To achieve this, I'll be leveraging something called [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
and the [AWS API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)


Here are some useful things to be aware of before proceeding further

- [Route 53 CLI / methods](https://docs.aws.amazon.com/cli/latest/reference/route53/index.html)
- [Using IAM based controls for Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/access-control-managing-permissions.html)
- [Editing IAM policies in general](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html)
- [Boto 3 and what is Boto 3](http://boto3.readthedocs.io/en/latest/index.html)
- [Boto 3 methods for Route 53](http://boto3.readthedocs.io/en/latest/reference/services/route53.html)


Primarily, we'll be using the [change-resource-record-sets](https://docs.aws.amazon.com/cli/latest/reference/route53/change-resource-record-sets.html) method to achieve the purpose/scope of this article.


> The change-resource-record-sets method is provided by Route 53 and may be levegaged from the CLI or API. We're gonna use the Boto 3 to interact with this method therein interacting with Route 53 via an API.



So here's what your use case looks like
1. Lambda function is hit by an API with certain parameters
2. Lambda function wil process and hit Route 53's API(via Boto 3)
3. Lambda function will return a response to us


## Why would one want to do this?

- Automation
- Orchestration
- Ease of life etc.


## Let's jump right in!

1. Find the [lambda code here](https://github.com/vishalbasra/Route53-things/blob/master/interact/lambda_function.py)
2. Find an example of [inputs that the function specifies here](https://github.com/vishalbasra/Route53-things/blob/master/interact/API-inputs.json)
3. Create a lambda function with the code from above (modify if needed , better yet [create a pull request!](https://help.github.com/articles/creating-a-pull-request/))
3. Create an API gateway endpoint - be considerate about security, access and controls.
4. Fire away to your api endpoint with an appropriate input from above!
