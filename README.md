# OIDC config for pipelines to AWS

## CircleCI

[CircleCI OIDC documentation](https://circleci.com/docs/openid-connect-tokens/)

1\. AWS IAM Identity provider

- **Provider:** `https://oidc.circleci.com/org/${ORGANIZATION_ID}`
- **Audience:** `${ORGANIZATION_ID}`
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
                "Federated": "arn:aws:iam::{AWS_ACCOUNT}:oidc-provider/oidc.circleci.com/org/{ORGANIZATION_ID}"
            },
            "Condition": {
                "StringEquals": {
                    "oidc.circleci.com/org/{ORGANIZATION_ID}:aud": [
                        "{ORGANIZATION_ID}"
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

[GitHub Actions OIDC documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)

1\. AWS IAM Identity provider

- **Provider:** `https://token.actions.githubusercontent.com`
- **Audience:** `sts.amazonaws.com`
- **Thumbprints:** "Generate when creating Identity provider"

2\. AWS Role Trust relationships:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::{AWS_ACCOUNT}:oidc-provider/token.actions.githubusercontent.com"
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

## BitBucket Pipelines

[BitBucket Pipelines OIDC documentation](https://support.atlassian.com/bitbucket-cloud/docs/deploy-on-aws-using-bitbucket-pipelines-openid-connect/)

1\. AWS IAM Identity provider

Provider and audience from `Repository Settings` -> `OpenID Connect`

- **Provider:** `https://api.bitbucket.org/2.0/workspaces/<WORKSPACE>/pipelines-config/identity/oidc`
- **Audience:** `ari:cloud:bitbucket::workspace/<WORKSPACE_ID>`
- **Thumbprints:** "Generate when creating Identity provider"

2\. AWS Role Trust relationships:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::{AWS_ACCOUNT}:oidc-provider/api.bitbucket.org/2.0/workspaces/{WORKSPACE}/pipelines-config/identity/oidc"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                    "api.bitbucket.org/2.0/workspaces/{WORKSPACE}/pipelines-config/identity/oidc:sub": "{REPO_UUID}:*"
                }
            }
        }
    ]
}
```

3\. Set up workflow using `oidc: true`

- Workflow example: [bitbucket-pipelines.yml](./bitbucket-pipelines.yml) 
- Replace AWS_REGION and AWS_ROLE_ARN from `bitbucket-pipelines.yml` envs

## GitLab CI

[GitLab CI OIDC documentation](https://docs.gitlab.com/ee/ci/cloud_services/aws/)

1\. AWS IAM Identity provider

- **Provider:** `https://gitlab.com`
- **Audience:** `https://gitlab.com`
- **Thumbprints:** "Generate when creating Identity provider"

2\. AWS Role Trust relationships:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::{AWS_ACCOUNT}:oidc-provider/gitlab.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                "gitlab.com:sub": "project_path:{GITLAB_USERID}/{PROJECT_NAME}:ref_type:branch:ref:main"
                }
            }
        }
    ]
}
```

3\. Set up workflow using `.gitlab-ci.yml`

- Workflow example: [gitlab-ci.yml](./.gitlab-ci.yml) 
- Replace AWS_REGION and AWS_ROLE_ARN from `gitlab-ci.yml` envs

## BuildKite

[BuildKite documentation](https://buildkite.com/docs/pipelines)

1\. AWS IAM Identity provider

- **Provider:** `https://agent.buildkite.com`
- **Audience:** `sts.amazonaws.com`
- **Thumbprints:** "Generate when creating Identity provider"

2\. AWS Role Trust relationships:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::{AWS_ACCOUNT}:oidc-provider/agent.buildkite.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                "agent.buildkite.com:sub": "organization:{ORG_NAME}:pipeline:{PIPELINE_NAME}:ref:refs/heads/main"
                }
            }
        }
    ]
}
```

3\. Set up workflow using `pipeline.yml`

- Workflow example: [pipeline.yml](./.buildkite/pipeline.yml) 
- Replace AWS_REGION and AWS_ROLE_ARN from `pipeline.yml` envs
