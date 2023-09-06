# Shipyard GitHub Action

[![GitHub License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](https://raw.githubusercontent.com/shipyard/github-action/master/LICENSE) [![Github Action Community](https://img.shields.io/badge/community-Github%20Actions%20Discuss-343434.svg)](https://github.community/c/github-ecosystem/github-apps/64)

Use this GitHub action to authenticate into your Shipyard ephemeral environments via a bypass token and run jobs on them.

This job connects with Shipyard during a [GitHub Actions](https://docs.github.com/en/actions) job, fetching necessary environment variables in order to run E2E tests where authentication via OAuth is normally required.

## How to use

In your GitHub workflow file located in `.github/workflows/`, you can use the Shipyard GitHub Action as per the following example:

```
on: [pull_request]

jobs:
  cypress-e2e-tests:
    runs-on: ubuntu-latest
    name: Collect the bypass token and URL for an authenticated ephemeral environment attached to this PR
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Fetch Shipyard Tokens
        uses: shipyard/shipyard-action/@1.0.0
        env:
          SHIPYARD_API_TOKEN: ${{ secrets.SHIPYARD_API_TOKEN }}
      - name: Run the E2E tests on the ephemeral environment
        run: npm run test
        shell: bash
        env:
            CYPRESS_BASE_URL: $SHIPYARD_ENVIRONMENT_URL
            CYPRESS_BYPASS_TOKEN: $SHIPYARD_BYPASS_TOKEN
        
```

The Github Action can be configured by passing inputs or environment variables:

**Inputs**
```
  - name: Fetch Shipyard Tokens
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
  - name: Fetch Shipyard Tokens
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

On successful run, the following environment variables are set, which can then be passed on to other actions in the same workflow.

| Parameter Name | Description |
| --------------- | --------------- |
|`SHIPYARD_ENVIRONMENT_URL` | URL of the ephemeral environment |
|`SHIPYARD_ENVIRONMENT_ID`  | ID of the ephemeral environment  |
|`SHIPYARD_BYPASS_TOKEN`    | Token to bypass authentication   |


## Resources

[Shipyard Documentation](https://docs.shipyard.build/docs/)
