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
                "Federated": "arn:aws:iam::<AWS_ACCOUNT>:oidc-provider/oidc.circleci.com/org/ORGANIZATION_ID"
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

- [Example](./circleci/config.yml)
- Replace ACCOUNT_ID and ROLE_NAME from `config.yml`
