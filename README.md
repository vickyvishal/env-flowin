# orgflow-action/env-flowin

Use OrgFlow to commit the metadata in your Salesforce org to a branch in a Git repository (flow in).

## Input

| Name | Required? | Default | Description |
| - | - | - | - |
| `stack-name` | ✔️ | `Main` |  The name of the stack that contains the environments that you'd like to flow in. |
| `environment-name` | ✔️ | |  Name of the environmentthat you'd like to flow in. |
| `commit-message` | | |  Optionally override the default message applied to the commits. |
| `force` | | `false` |  Force commit (bypass OrgFlow's merging strategy and commit the meatadata from Salesforce as it is, even if the Git repository is ahead of your Salesforce org)? Type `true` to enable. |
| `salesforce-username` | ✔️ | |  The username to use during authentication to your production Salesforce org. |
| `salesforce-password` | ✔️ | |  The password to use during authentication to your production Salesforce org. You may need to also include your [security token](https://help.salesforce.com/s/articleView?id=sf.user_security_token.htm&type=5), depending on how your org is configured. If this is the case, then you will need to append your security token to th end of your password (e.g. `<password><security-token>`). |
| `git-token` | ✔️ | |  The token or password used to authenticate to the remote Git repository. Most likely this value should be `${{ secrets.GITHUB_TOKEN }}`. |
| `license-key` | ✔️ | |  Your OrgFlow license key. You can get a license key from www.orgflow.io if you do not already have one. |

## Output

| Name | Description |
| - | - |
| `flow-in-from` |  A string of JSON that represents the results of the inbound flow of the source environment (see below). |
| `flow-in-into` |  A string of JSON that represents the results of the inbound flow of the target environment (see below). |
| `flow-out-into` |  A string of JSON that represents the results of the outbound flow of the target environment (see below). |
| `deployable-components` |  A string of JSON that represents the components that were (or could be) deployed (see below). |
| `undeployable-components` |  A string of JSON that represents the components that were not (or could not be) deployed (see below). |

All JSON strings can be converted to a JSON object or a JSON data type by using the [`fromJSON(value)`](https://docs.github.com/en/actions/learn-github-actions/expressions#fromjson) function. You can also use `jq` to manipulate or filter this data.

### `flow-in-from` & `flow-in-into`

Example output (formatted for readability- actual output does not contain the new line or whitespace formatting):

```json
{
  "result": {
    "commitResult": {
      "files": []
    }
  },
  "stats": {
    "retrieve": {
      "counts": {
        "packages": 1,
        "components": 368,
        "batches": 1,
        "bytes": 237171
      },
      "duration": "00:00:14.8735796"
    },
    "apply": {
      "componentCounts": {
        "total": 1173,
        "added": 0,
        "copied": 1173,
        "deleted": 0,
        "excluded": 0
      },
      "duration": "00:00:00.2597250"
    },
    "commit": {
      "counts": {
        "filesCommitted": 0,
        "commits": 0
      },
      "duration": "00:00:00"
    },
    "duration": "00:00:23.3408468"
  }
}
```

### Examples

Use `orgflow-actions/env-flowmerge` to merge two environmets and deploy the result to Salesforce:

```yaml
jobs:
  env-flowmerge:
    name: "Merge environments"
    runs-on: ubuntu-latest    
    container: 
      image: orgflow/cli:latest
      env:
        ORGFLOW_ACCEPTEULA: true
    steps:
    - uses: orgflow-actions/env-flowmerge@v1
      id: env-flowmerge
      with:
        stack-name: Main
        source-envronment: QA
        target-envronment: Production
        check-only: false
        salesforce-username: ${{ secrets.SALESFORCE_USERNAME }}
        salesforce-password: ${{ secrets.SALESFORCE_PASSWORD }}
        git-token: ${{ secrets.GITHUB_TOKEN }}
        license-key: ${{ secrets.ORGFLOW_LICENSEKEY }}
```

## OrgFlow is a true Git-based DevOps tool for Salesforce

Find out how OrgFlow can help you improve your Salesforce DevOps process at [orgflow.io](https://www.orgflow.io).
