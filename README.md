# OIDC config for pipelines to AWS

## CircleCI

[Using OpenID Connect Tokens in Jobs](https://circleci.com/docs/openid-connect-tokens/)

1\. AWS IAM Identity provider

- **Provider:** https://oidc.circleci.com/org/${ORGANIZATION_ID}
- **Audience:** ${ORGANIZATION_ID}
- **Thumbprints:** "Generate when creating Identity provider"

2\. AWS Role Trust relationships:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Principal": {
                "Federated": "arn:aws:iam::AWS_ACCOUNT:oidc-provider/oidc.circleci.com/org/ORGANIZATION_ID"
            },
            "Condition": {
                "StringEquals": {
                    "oidc.circleci.com/org/ORGANIZATION_ID:aud": [
                        "ORGANIZATION_ID"
                    ]
                }
            }
        }
    ]
}
```

3\. Create a CicleCI context:

- [Create and use a context](https://circleci.com/docs/contexts/#create-and-use-a-context)
- **example**: aws

4\. Set up workflow using `circleci/aws-cli@3.1`

- [config.yml](./.circleci/config.yml) example
- Replace ACCOUNT_ID and ROLE_NAME from `config.yml`

## GitHub Actions

[Configuring OpenID Connect in Amazon Web Services](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)

1\. AWS IAM Identity provider

- **Provider:** https://token.actions.githubusercontent.com
- **Audience:** sts.amazonaws.com
- **Thumbprints:** "Generate when creating Identity provider"

2\. AWS Role Trust relationships:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::AWS_ACCOUNT:oidc-provider/token.actions.githubusercontent.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                    "token.actions.githubusercontent.com:sub": "repo:github-org/github-repo:*"
                },
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                }
            }
        }
    ]
}
```

3\. Set up workflow using `aws-actions/configure-aws-credentials`

- Workflow example: [oidc-aws.yml](./.github/workflows/oidc-aws.yml) 
- Replace ROLE_ARN and AWS_REGION from `oidc-aws.yml` envs