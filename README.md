# Integrate Shipyard

[![GitHub License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](https://raw.githubusercontent.com/shipyard/github-action/master/LICENSE) [![Github Action Community](https://img.shields.io/badge/community-Github%20Actions%20Discuss-343434.svg)](https://github.community/c/github-ecosystem/github-apps/64)

Securely authenticate into [Shipyard](https://shipyard.build)-generated environments to test before merge by running E2E tests against an ephemeral environment on every commit.

This action connects to Shipyard using your **API Token** and fetches the environment variables necessary to test against your existing environments within your [GitHub Actions](https://docs.github.com/en/actions) workflow. 

## Setup
- Obtain your API token from your Shipyard **Org Settings** page
  
  <img src="https://docs.shipyard.build/img/screenshots/Shipyard-API-Token.png" width="500px">
- Create a new GitHub repository secret named `SHIPYARD_API_TOKEN`
- Open a PR on your Shipyard-enabled app

## How to use

In your GitHub workflow file located in `.github/workflows/`, you can use the **Shipyard GitHub Action** as per the following example:

```
on: [pull_request]

jobs:
  cypress-e2e-tests:
    runs-on: ubuntu-latest
    name: Collect the bypass token and URL for an authenticated ephemeral environment attached to this PR
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Integrate Shipyard
        uses: shipyard/shipyard-action@1.0.0
        env:
          SHIPYARD_API_TOKEN: ${{ secrets.SHIPYARD_API_TOKEN }}
      - name: Run E2E tests against the ephemeral environment
        run: npm run test
        shell: bash
        env:
            CYPRESS_BASE_URL: $SHIPYARD_ENVIRONMENT_URL
            CYPRESS_BYPASS_TOKEN: $SHIPYARD_BYPASS_TOKEN
        
```

This action can be configured by passing inputs or environment variables:

**Inputs**
```
  - name: Integrate Shipyard
    uses: shipyard/shipyard-action@1.0.0
    with:
        api-token: ${{ secrets.SHIPYARD_API_TOKEN }}
        timeout-minutes: 30
```

| Input name | Description | Default Value |
| --------------- | --------------- |--------------- |
| `api-token` | Token required to connect to Shipyard's APIs. Can be obtained from your Shipyard **Org Settings** page | -|
| `timeout-minutes` | Number of minutes to wait for Shipyard environment before timing out. | 60|
| `app-name` | Filter the environments by name of the application on the Shipyard app. | -|


**Environment Variables**
```
  - name: Integrate Shipyard
    uses: shipyard/shipyard-action@1.0.0
    env:
      SHIPYARD_API_TOKEN: ${{ secrets.SHIPYARD_API_TOKEN }}
      SHIPYARD_TIMEOUT: 30
      INPUT_APP_NAME: 'react-app'
```

| Environment Variable | Description | Default Value |
| --------------- | --------------- |--------------- |
| `SHIPYARD_API_TOKEN` | Token required to connect to Shipyard's APIs. Can be obtained from your Shipyard **Org Settings** page  |-|
| `SHIPYARD_TIMEOUT` | Number of minutes to wait for Shipyard environment before timing out. |60|
| `SHIPYARD_APP_NAME` | Filter the environments by name of the application on the Shipyard app. |-|

**NOTE**: Inputs are given precedence over environment variables.

If input `api-token` or environment variable `SHIPYARD_API_TOKEN` is not provided, error is raised.

On a successful run, the following environment variables are set, which can then be passed on to other actions in the same workflow:

| Parameter Name | Description |
| --------------- | --------------- |
|`SHIPYARD_ENVIRONMENT_URL` | URL of the ephemeral environment |
|`SHIPYARD_ENVIRONMENT_ID`  | ID of the ephemeral environment  |
|`SHIPYARD_BYPASS_TOKEN`    | Token to bypass authentication   |


## Resources

[Shipyard Documentation](https://docs.shipyard.build/docs/)
