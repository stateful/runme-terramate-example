# terramate-example-code-generation

![CI Status](https://github.com/terramate-io/terramate-example-code-generation/actions/workflows/ci.yml/badge.svg)
[![Join Discord](https://img.shields.io/discord/1088753599951151154?label=Discord&logo=discord&logoColor=white)](https://terramate.io/discord)

This project shows an example file/dir structure you can use with
[Terramate](https://github.com/terramate-io/terramate) to keep your Terraform
code DRY.

Be sure to read through the [Terramate documentation](https://github.com/terramate-io/terramate)
to understand the features of Terramate used here.

The example is organized as two environments, each environment will have:

- Its own [Google Cloud Project](https://cloud.google.com/storage/docs/projects).
- Service account to be used when deploying Cloud Run services.
- Two [Cloud Run](https://cloud.google.com/run) applications.

The [Cloud Run](https://cloud.google.com/run) applications are simple
echo servers that will be reachable through public URLs provided by
[Cloud Run](https://cloud.google.com/run).

Note: This code is solely for demonstration purposes.
This is not production-ready code, so use it at your own risk.

# How to use this project?

## Pre-Requisites

- [Terraform](https://www.terraform.io/) `~> 1.8`
- [Terramate](https://github.com/terramate-io/terramate) `~> 0.8.4`
- Configure your Google Cloud credentials using one of the supported [authentication mechanisms](https://registry.terraform.io/providers/hashicorp/google/latest/docs/guides/provider_reference#authentication)
- Google Cloud Provider account
- At least one [Google Cloud project](https://cloud.google.com/storage/docs/projects)
- The Google Cloud project has a proper billing account configured

# How is the code organized?

This is the overall structure of the project:

```ini {"id":"01J1N5425WZ9SZMJT7K67XA1JA"}
├── modules
│   ├── cloud-run
│   └── service-account
└── stacks
    ├── prod
    │   ├── cloud-runs
    │   │   ├── app1
    │   │   ├── app2
    │   └── service-accounts
    │       └── cloud-run
    └── staging
        ├── cloud-runs
        │   ├── app1
        │   ├── app2
        └── service-accounts
            └── cloud-run
```

- `modules/cloud-run`: Local module, useful to showcase change detection and DRY code generation.
- `modules/service-account`: Local Terramate config that keeps code generation DRY between environments.
- `stacks/prod`: All stacks belonging to the prod environment.
- `stacks/staging`: All stacks belonging to the staging environment.
- `stacks/<env>/service-accounts/cloud-run`: Stack that creates service accounts used to execute the cloud run services.
- `stacks/<env>/cloud-runs/{app1,app2}`: Stacks that create Cloud Run services.

As you navigate the project you will find multiple Terramate configuration files.
Each file will have documentation guiding you through its purpose and usage.

## Listing Stacks

To check if your Terramate installation is working and get an overview of the
available stacks just run:

```sh {"id":"01J1N5425WZ9SZMJT7K7SZTDP7"}
terramate list
```

To check how each stack is defined in detail you can use `terramate run`:

```sh {"id":"01J1N5425WZ9SZMJT7K9TD47MF"}
terramate run -- cat stack.tm.hcl
```

This will run on each stack directory the command `cat stack.tm.hcl`.
The output will be the definition of all stacks.

Later we are going to use the same mechanism to create and destroy all stacks.

## Deploying Stacks

Before we try to deploy any stacks, beware that this will require you
to have [Google Cloud credentials](https://cloud.google.com/docs/authentication/getting-started)
and deploying infrastructure will incur costs (check the
[pre-requisites](#pre-requisites) section for more details).

On `stacks/config.tm.hcl` you will find the `terraform_google_provider_project`
global which configures the project where infrastructure will be created.

It is important to change that to a [Google Cloud project](https://cloud.google.com/storage/docs/projects)
where you have appropriate permissions.

Once the configuration is changed we need to update the generated code by running:
At this point, since our project has uncommitted changes Terramate will prevent us
from running any commands. Create a branch (or use the flag `--disable-check-git-uncommitted`
to disable the git checks):

```sh {"id":"01J1N5425WZ9SZMJT7KC24FTWK"}
git checkout -b runme-cloud-renderers
```

Generate code again (this steps was missing?):

```sh {"id":"01J1N5FPRKTKKV8A48JCM86D53"}
terramate generate
```

And commit all the changed files.

Now we initialize all our stacks:

```sh {"id":"01J1N5425WZ9SZMJT7KEDWCBF0"}
terramate run -- terraform init
```

Check how their plans look like:

```sh {"id":"01J1N5425WZ9SZMJT7KEWYDNRD"}
terramate run -- terraform plan
```

And apply them:

```sh {"id":"01J1N5425WZ9SZMJT7KHJW510X"}
terramate run -- terraform apply
```

For each Cloud Run service deployed, there will be an output with the URL to
the deployed service, like this:

```sh {"id":"01J1N5425WZ9SZMJT7KJ8WY5WP"}
url = "https://terramate-app1-<env>-<hash>-lz.a.run.app"
```

You can check the outputs with:

```sh {"id":"01J1N5425WZ9SZMJT7KNZ6DQWQ","name":"APP_URLS"}
terramate run -C stacks/prod \
  terraform output -json 2>/dev/null \
  | jq -r '.url.value' \
  | grep -v null \
  | tail -n 1
```

Open the URL on the browser to check the running service.

To avoid unnecessary charges to your account let's destroy all stacks:

```sh {"id":"01J1N5425WZ9SZMJT7KP3VAPG7"}
terramate run --reverse -- terraform destroy
```

The `--reverse` flag runs all stacks in reversed order, which is desirable
when destroying resources.
