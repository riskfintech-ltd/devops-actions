# RFT custom Github workflow action helpers
A reference Github workflow file is in ./github/workflows/example.yaml

## Prepare build
Prepare set of variables related to project build that will be used later.

### Input
* **name** [Optional] -- Name of project to build. Will be equal to Git repo name `${{ github.event.repository.name }}` if not set.

### Output
* **name** -- Name of the project.  
* **build-type** -- `release` or `development`.
* **build-version** -- Generated build version (also accessed via ${{ env.build-version }}).
* **chart-version** -- Generated chart version.
* **ecr-repository** -- Name of ECR repository (also accessed via ${{ env.ecr-repository }}).
* **sha-short-var** -- Current Git commit head (short version).
* **sha-full-var** -- Current Git commit head (full version).

### Usage
```yaml
- uses: riskfintech-ltd/devops-actions/prepare-build@v1
```

## Build and Publish to ECR

### Input
* **access-key** -- AWS Access Key. `${{ secrets.AWS_ACCESS_KEY_ID }}` will be used if not specified.
* **secret** -- AWS Access Key Secret. `${{ secrets.AWS_ACCESS_SECRET }}` will be used if not specified.
* **region** [Optional] -- AWS Region name. Default region is `eu-west-2`.
* **build-args** [Optional] -- Build args for `docker build`.
* **ssh** [Optional] -- SSH parameter for `docker build`.
* **file** [Optional] -- Name of `Dockerfile` file. Supported from `v2`.

### Output
* **image** -- Docker image that was built and pushed to ECR

### Usage
```yaml
  - uses: riskfintech-ltd/devops-actions/publish-ecr@v2
    with:
      build-args: --build-arg RELEASE_SIGNATRUE=${{ steps.build.outputs.name }}-${{ env.build_version }}
```

## Helm
Publish project package to Chart Museum

### Input
* **name** [Optional] -- Name of project. Will be equal to Git repo name `${{ github.event.repository.name }}` if not set. 
* **chartmuseum-url** -- URL of Chart Museum.
* **chartmuseum-username** -- Username for Chart Museum.
* **chartmuseum-password** -- Password for Chart Museum.

## Output
* **chart-package** -- Package that was published to Chart Museum

## Example
```yaml
  - uses: riskfintech-ltd/devops-actions/helm@v1
```

# Slack
Notify Slack about build result (successful or failed).

## Input
* **name** [Optional] -- Name of project. Will be equal to Git repo name `${{ github.event.repository.name }}` if not set. 
* **slack-webhook-url** -- URL of Slack Webhook to notify.

## Example:
```yaml
  - uses: riskfintech-ltd/devops-actions/slack@v1
    with:
      slack-webhook-url: ${{ secrets.SLACK_WEBHOOK_URL }}
```