# Google Cloud Platform

Find all users with a certain role in a GCP organization be they on whatever project/folder

```
gcloud asset search-all-iam-policies --scope=organizations/[ORG_ID] --query="policy:roles/[ROLE_NAME]" --format=json
```
