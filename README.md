# IAM-and-Secrets-Manager
Terraform Project
### Objective:
Write a terraform module that creates:
-   A secret in Secrets Manager of a JSON containing a "username" and
    "password" named `MyTestSecret`
    -   The "username" will be an input variable
    -   The "password" should be a randomly-generated string
-   A policy that allows an entity to read that secret only
-   A role that has permission to fetch that secret named `SecretsUser`
    -   Allow all users of the AWS account to assume role
-   An IAM user named `testuser` that has permission to assume the role
    of `SecretsUser`
    -   This user should have an access key created for them


### Requirements:
-   The resources can be created with `terraform apply`
-   Once resources are created, subsequent invocations of
    `terraform apply` do not result in any changes (meaning module is
    idempotent)
-   The resources can be destroyed with `terraform destroy`
-   The resources can be immediately created again with `terraform apply`
-   Use a random string as the password at the time of secret creation
-   Input variables:
    -   `username`: username used in the secret, optional
-   Output variables:
    -   `secrets_user_role_arn`: ARN of `SecretsUser` role
    -   `aws_access_key_id`: Access key ID of `testuser`
    -   `aws_secret_access_key`: Access key secret of `testuser`


### Testing:
Using the AWS CLI...
-   Configure the access credentials of the `testuser` in `~/.aws/config`
-   Configure the role profile for `SecretsUser` in `~/.aws/config`
-   Receive `AccessDeniedException` when attempting to read the secret
    value as `testuser`, e.g.:
```
$ aws secretsmanager get-secret-value --secret-id MyTestSecret --profile testuser
An error occurred (AccessDeniedException) when calling the GetSecretValue operation: User: arn:aws:iam::****:user/testuser is not authorized to perform: secretsmanager:GetSecretValue on resource: MyTestSecret
```
-   Succeed in reading the secret value when assuming the role of
    `SecretsUser`
```
$ aws secretsmanager get-secret-value --secret-id MyTestSecret --profile secretsuser
<JSON output of secret and metadata>
```


### Structure
Use the following file structure/layout to build and run the module:
```
project/
    modules/
        secrets/
            main.tf
            outputs.tf
            variables.tf
    live/
        secrets/
            main.tf     # use `secrets` module from modules/ as source
            outputs.tf
```
