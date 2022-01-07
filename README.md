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
* **release-name** -- Release name (it can be used for making a release archive, for example), it's also accessible via ${{ env.release_name }}.  

### Usage
```yaml
- uses: riskfintech-ltd/devops-actions/prepare-build@v3
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
  - uses: riskfintech-ltd/devops-actions/publish-ecr@v3
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
  - uses: riskfintech-ltd/devops-actions/helm@v3
    with:
      chartmuseum-url: "https://..."
      chartmuseum-username: ${{ secrets.username }}
      chartmuseum-password: ${{ secrets.password }}

```

# Slack
Notify Slack about build result (successful or failed).

## Input
* **name** [Optional] -- Name of project. Will be equal to Git repo name `${{ github.event.repository.name }}` if not set. 
* **slack-webhook-url** -- URL of Slack Webhook to notify.

## Example:
```yaml
  - uses: riskfintech-ltd/devops-actions/slack@v3
    with:
      slack-webhook-url: ${{ secrets.SLACK_WEBHOOK_URL }}
```

# Release
Make a release with archive and tag on GitHub

## Input
* **folder** -- folder with release content.
* **github-token** -- GitHub token with access to the repo for deployment and tag.

## Output
* **release-archive** -- name of release archive (also available from `${{ env.release_archive }}`.

## Example:
```yaml
  - uses: riskfintech-ltd/devops-actions/release@v3
    with:
      folder: 'models/'
      github-token: ${{ secrets.TOKEN }}
```
