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
| `result` |  A string of JSON that represents the results of the inbound flow (see below). |

All JSON strings can be converted to a JSON object or a JSON data type by using the [`fromJSON(value)`](https://docs.github.com/en/actions/learn-github-actions/expressions#fromjson) function. You can also use `jq` to manipulate or filter this data.

### `result`

Example output (formatted for readability- actual output does not contain the new line or whitespace formatting):

```json
{
  "result":{
    "commitResult":{
      "files":[
        {
          "filePath":"unpackaged/objects/Account.object",
          "renamedFrom":null,
          "changeType":129,
          "commit":{
            "hash":"c8b65bd3f81a67dc63efa78e4d216013cc34a5f2",
            "author":{
              "name":"Billy Mill",
              "email":"00509000008XvVNAA0"
            }
          },
          "isNonItem":false,
          "component":{
            "id":"",
            "namespacePrefix":null,
            "type":"CustomObject",
            "name":"Account"
          }
        },
        {
          "filePath":"unpackaged/layouts/Account-Account Layout.layout",
          "renamedFrom":null,
          "changeType":129,
          "commit":{
            "hash":"3153c714825701e66bf853eaba99dc50c025acb8",
            "author":{
              "name":"Billy Mill",
              "email":"00509000008XvVNAA0"
            }
          },
          "isNonItem":false,
          "component":{
            "id":"00h0900000DdOV0AAN",
            "namespacePrefix":null,
            "type":"Layout",
            "name":"Account-Account Layout"
          }
        }
      ]
    }
  },
  "stats":{
    "retrieve":{
      "counts":{
        "packages":1,
        "components":362,
        "batches":1,
        "bytes":234714
      },
      "duration":"00:00:14.3269632"
    },
    "apply":{
      "componentCounts":{
        "total":1522,
        "added":1,
        "copied":1153,
        "deleted":0,
        "excluded":368
      },
      "duration":"00:00:00.2464416"
    },
    "commit":{
      "counts":{
        "filesCommitted":2,
        "commits":2
      },
      "duration":"00:00:01.8135295"
    },
    "duration":"00:00:24.1976562"
  }
}
```

### Examples


Use `orgflow-actions/env-flowin` to merge two environmets and deploy the result to Salesforce:

```yaml
jobs:
  env-flowin:
    name: "Flow in an environment"
    runs-on: ubuntu-latest    
    container: 
      image: orgflow/cli:latest
      env:
        ORGFLOW_ACCEPTEULA: true
    steps:
    - uses: orgflow-actions/env-flowin@v1
      id: env-flowmerge
      with:
        stack-name: Main
        envronment-name: QA
        salesforce-username: ${{ secrets.SALESFORCE_USERNAME }}
        salesforce-password: ${{ secrets.SALESFORCE_PASSWORD }}
        git-token: ${{ secrets.GITHUB_TOKEN }}
        license-key: ${{ secrets.ORGFLOW_LICENSEKEY }}
```

## OrgFlow is a true Git-based DevOps tool for Salesforce

Find out how OrgFlow can help you improve your Salesforce DevOps process at [orgflow.io](https://www.orgflow.io).
