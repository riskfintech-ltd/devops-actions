# RFT custom Github workflow action helpers

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
- uses: riskfintech-ltd/actions/prepare-build@v1
  id: build
```

## Build and Publish to ECR

### Input
* **ecr-repository** [Optional] -- Name of ECR repository (prepared by `prepare-build` action), used one from `env` if not set.
* **build-version** [Optional] -- Build version (prepared by `prepare-build` action), used one from `env` if not set.
* **access-key** [Optional] -- AWS Access Key. `${{ secrets.AWS_ACCESS_KEY_ID }}` will be used if not specified.
* **secret** [Optional] -- AWS Access Key Secret. `${{ secrets.AWS_ACCESS_SECRET }}` will be used if not specified.
* **region** [Optional] -- AWS Region name. Default region is `eu-west-2`.
* **build-args** [Optional] -- Build args for `docker build`.

### Output
* **image** -- Docker image that was built and pushed to ECR

### Usage
```yaml
  - uses: riskfintech-ltd/actions/publish-ecr@v1
    id: ecr
    with:
      build-args: --build-arg RELEASE_SIGNATRUE=${{ steps.build.outputs.name }}-${{ env.build_version }}
```

## Helm

Publish project package to Chart Museum

### Input
* **name** [Optional] -- Name of project. Will be equal to Git repo name `${{ github.event.repository.name }}` if not set. 
* **build-version** [Optional] -- Build version (prepared by `prepare-build` action), used one from `env` if not set. 
* **chart-version** [Optional] -- Chart version (prepared by `prepare-build` action), is equal to `{build-version}-chart` if not set. 
* **chartmuseum-url** [Optional] -- URL of Chart Museum (default: https://chartmuseum.riskfintech.co.uk).
* **chartmuseum-username** [Optional] -- User name for Chart Museum, (default: ${{ secrets.CHARTMUSEUM_USERNAME }}).
* **chartmuseum-password** -- Password for Chart Museum (default: ${{ secrets.CHARTMUSEUM_PASSWORD }})

## Output
* **chart-package** -- Package that was published to Chart Museum

## Example
```yaml
  - uses: riskfintech-ltd/actions/helm@v1
    id: helm
```

# Slack
Notify Slack about build result (successful or failed).

## Input
* **name** [Optional] -- Name of project. Will be equal to Git repo name `${{ github.event.repository.name }}` if not set. 
* **build-type** [Optional] -- Build type release/development (prepared by `prepare-build` action), used one from `env` if not set. 
* **build-version** [Optional] -- Build version (prepared by `prepare-build` action), used one from `env` if not set. 
* **chart-version** [Optional] -- Chart version (prepared by `prepare-build` action), is equal to `{build-version}-chart` if not set. 
* **slack-webhook-url** [Optional] -- URL of Slack Webhook to notify (default: ${{ secrets.SLACK_WEBHOOK_URL }})
