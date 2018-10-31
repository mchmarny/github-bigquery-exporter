# github-bigquery-exporter

GitHub BigQuery export utility, for those times when more granular PR and Issue queries are required. This is also good way to query data for periods longer than the GitHub max of `30` days.

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

Optionally, to configure the import script you can edit the data-set name and configure the `issue` and `pull` table name. This step is only required if you for some reason have name conflicts in your BiqQuery project.

```shell
declare -r ds="github"
declare -r issues_table="issues"
declare -r pulls_table="pulls"
```

## Export

To execute the GitHub export script run this command:

```shell
./export
```

The expected output should look something like this

```shell
Downloading issues for org/repo-1...
Downloading prs for org/repo-1...
Downloading issues for org/repo-2...
Downloading prs for org/repo-2...
```

## Import

To execute the BigQuery import script run this command:

```shell
./import
```

The expected output should look something like this

```shell
Dataset 'project:github' successfully created.
Table 'project:github.issues' successfully created.
Table 'project:github.pulls' successfully created.
Waiting on bqjob_..._1 ... (0s) Current status: DONE
Waiting on bqjob_..._1 ... (0s) Current status: DONE
```

### Query

When the above scripts completed successfully you should be able to query the imported data using SQL in BigQuery console:

```sql
select
  i.repo,
  count(*) num_of_issues
from gh.pulls i
where date_diff(CURRENT_DATE(), date(i.ts), day) < 31
group by
  i.repo,
order by 2 desc
```

### TODO

* Add org user export/import
* Sort out the 2nd run where tables have to be appended
* Bash, really? Can I haz me a service?
