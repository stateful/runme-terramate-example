## Embedding GCP ☁️ resources

Runme's Cloud-Native Bash Kernel understand how to replace ENV variables with their values. This allows you to generically embed GCP cloud resources in your notebook.

```sh {"id":"01J1R72RX06Y44WXRW2Z04AMY5","terminalRows":"3"}
export PROJECT_ID="runme-ci"
echo "PROJECT_ID set to $PROJECT_ID"
```

Let's see what VMs we've got running inside the GCP project:

```sh {"id":"01J1R73JG6GD83EQG1AWVSHTGM"}
https://console.cloud.google.com/compute/instances?project=$PROJECT_ID
```

## Now let's deep-link AWS  ☁️ resources

Let's select a profile first. Runme is using the offical AWS SDK under the hood, so it can use the same profiles as the AWS CLI.

```sh {"id":"01J1R7DQNK2YG9149XA6A900JT","terminalRows":"3"}
export AWS_PROFILE="stateful"
echo "Using AWS Profile $AWS_PROFILE"
```

Let's see what Kubernetes clusters we've got running in EKS:

```sh {"id":"01J1R7E9Y8F13T4SSG6N86GNFQ","terminalRows":"3"}
export EKS_REGION="us-east-1"
echo "EKS_REGION set to $EKS_REGION"
```

```sh {"id":"01J1R7FC03F50HHMNRH7FR5G8Q"}
https://$EKS_REGION.console.aws.amazon.com/eks/home?region=$EKS_REGION#/clusters
```

## Super useful 🤙 for workflows

```sh {"id":"01J1R7VC7QP9Z0CSDCYV4KAY7N","interactive":"false"}
cat assets/consoles.png
```

> 💡 Here's a [video](https://www.youtube.com/watch?v=eeVZQNw5KRU) if you don't remember Xzibit's TV show and how it became a meme.