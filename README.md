# github-bigquery-exporter

GitHub BigQuery export utility, for those times when more granular PR and Issue queries are required.

## Requirements

### token

You can run the export script without the GitHub API token but you will be subject to much stricter rate limits. To avoid this (important for larger organizations) get a personal API tokens by following [these instructions](https://blog.github.com/2013-05-16-personal-api-tokens/) and define it in an `GITHUB_ACCESS_TOKEN` environment variable

```shell
export GITHUB_ACCESS_TOKEN="your long token string goes in here"
```

> Remember, you have to be org admin for this to work

### json2csv

GitHub API exports data in JSON format. The simplest way to import desired data elements is to convert the data into CSV using `json2csv`, a Node.js utility that converts JSON to CSV.

```shell
npm install -g json2csv
```

## Configuration

To configure the export script you will need to define the `organization` and provide list of `repositories` in this organization.

```shell
declare -r org="my-org-name"
declare -a repos=("my-repo-1"
                  "my-repo-2"
                  "my-repo-3")
```

## Execution

To run the